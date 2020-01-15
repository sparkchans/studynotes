* 获取 Tomcat 压缩包: 
  `wget http://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.5.50/bin/apache-tomcat-8.5.50.tar.gz /opt`
* 解压缩包:  
  `tar -xzvf apache-tomcat-8.5.50.tar.gz` 
* 创建指向 Tomcat 安装目录的符号链接, 这里不直接修改 Tomcat 安装目录名称是为了将来能够安装多个版本的 Tomcat 而不丢失其版本信息.
  `ln -s /opt/apache-tomcat-8.5.50 /opt/tomcat8`
* 调整日志文件位置, 这里统一将日志文件放在 Linux 的`/var/log`目录下以统一管理.
 `mkdir /var/log/tomcat8`
 `rm -rf /opt/tomcat8/logs`
 `ln -s /var/log/tomcat8 /opt/tomcat8/logs`
* 创建用户并赋予权限
 `useradd tomcat`
 `chown -R tomcat:tomcat /opt/apache-tomcat-8.5.50`
 `chown -R tomcat:tomcat /var/log/tomcat8` 
 * 更改脚本执行权限
 `chmod 770 /opt/tomcat8/bin/*.sh`
 * 添加 Tomcat 服务, 增加`/etc/systemd/system/tomcat.service`
    ```
    [Unit]
    Description=Tomcat
    After=syslog.target network.target

    [Service]
    Type=forking
    User=tomcat
    Group=tomcat
    ExecStart=/opt/tomcat8/bin/catalina.sh start
    ExecStop=/opt/tomcat8/bin/catalina.sh stop

    [Install]
    WantedBy=multi-user.target

    ```
    使用`systemctl daemon-reload`重新加载服务
* 开启防火墙并重新加载防火墙
    `firewall-cmd --zone=public --add-port=8080/tcp --permanent` 
    `firewall-cmd --reload`
* 设置服务开机启动:
    `systemctl enable tomcat`
* 设置 tomcat 用户能够控制服务, 增加`/etc/sudoers.d/tomcat`配置文件
    ```
    %tomcat ALL= NOPASSWD: /bin/systemctl start tomcat
    %tomcat ALL= NOPASSWD: /bin/systemctl stop tomcat
    %tomcat ALL= NOPASSWD: /bin/systemctl restart tomcat
    tomcat ALL= NOPASSWD: /bin/systemctl start tomcat
    tomcat ALL= NOPASSWD: /bin/systemctl stop tomcat
    tomcat ALL= NOPASSWD: /bin/systemctl restart tomcat

    ```
    并且在`/etc/sudoers`设置:
   `#includedir /etc/sudoers.d`
   配置完成后, 就可以直接使用命令`sudo systemctl start tomcat`来启动 tomcat 服务.
* 设置 tomcat 的管理员账号.
  * 开放局域网 ip 访问, 编辑`manager/META-INF`下的`context.xml`文件:
    ```XML
    <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="^192.168.1.*$" />
    ```
  * 增加用户, 编辑`conf/tomcat-users.xml`文件:
    ```XML
    <role rolename="manager-gui"/>	
    <role rolename="manager-script"/>	
    <role rolename="manager-jmx"/>	
    <role rolename="manager-status"/>	
    <user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status"/>
    ```

* 参考: 
[1] : [How to install apache tomcat on Linux RHEL 8 / CentOS 8](https://linuxconfig.org/how-to-install-apache-tomcat-on-linux-redhat-8) 
[2] : [How could we allow non-root users to control a system.d service?
](https://unix.stackexchange.com/questions/192706/how-could-we-allow-non-root-users-to-control-a-system-d-service)
[3] : [Restarting systemd service only as a specific user?](https://unix.stackexchange.com/questions/496982/restarting-systemd-service-only-as-a-specific-user)
