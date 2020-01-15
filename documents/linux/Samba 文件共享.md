1. **需求分析:**   
    创建两个用户 sambauser 和 sambaadmin. sambauser 只能访问它自己的共享文件夹, sambaadmin 可以访问所有的共享文件夹. 系统环境: CentOS 7 和 Windows 10.
2. **samba 安装及配置:**  
    * 安装 samba: `yum install samba samba-client`
    * 启动服务: `systemctl start smb.service, systemctl nmv.service`    
    `smbd` 服务分别在 139 和 445 端口利用 TCP 提供文件共享和打印服务; `nmbd` 在端口 137 上利用 UDP 提供服务. 
    * 开启防火墙:`firewall-cmd --permanent --zone=public --add-service=samba`
    * 创建 samba 用户和目录结构:
      * 创建共享根目录并分配用户组:  
        * 创建根目录: `mkdir /samba`
        * 创建用户组: `groupadd sambashare`
        * 更改目录用户组: `chgrp sambashare /samba` 
    * 创建 Samba 用户:   
     Samba 使用 Linux 的用户和用户组权限, 但是它有自己的鉴权机制. 也就是说应该使用 `smbpasswd` 来为用户设置访问密码.  
       * 创建普通用户:
         * 创建 sambauser 用户:   
         `useradd -M -d /samba/sambauser -s /usr/sbin/nologin -G sambashare sambauser` 
           * -M: 不创建用户主目录
           * -d: 设置用户主目录为 `/samba/sambauser`
           * -s: 禁止该用户登陆 Shell
           * -G: 将用户加到用户组
         * 创建 sambauser 用户主目录并更改权限:  
            `mkdir /samba/sambauser`    
            `chown sambauser:sambauser /samba/sambauser`
            `chmod 770 /samba/sambauser`
         * 设置 sambauser 密码并启用用户:  
            `sambapasswd -a sambauser`   
            `sambapasswd -e sambauser`
     * 创建管理员用户:
       * 创建 sambaadmin 用户:   
       `useradd -M -d /samba/users -s /usr/sbin/nologin -G sambashare,sambauser sambaadmin` 
       * 创建目录并更改用户组:  
       `chown sambaadmin:sambashare /samba/users`  
       `chmod 2770 /samba/users`   
       `samba/users` 可以被所有通过授权的用户访问.
   * 配置共享:
        ```
        [users]
               path = /samba/users
               browseable = yes
               read only = no
               force create mode = 0660
               force directory mode = 2770
               valid users = @sambashare @sambaadmin

        [sambauser]
              path = /samba/sambauser
              browseable = no
              read only = no
              force create mode = 0660
              force directory mode = 2770
              valid users = sambauser @sambadmin
        ```
        * [users] 和 [sambauser]: 登陆时所使用的共享名称
        * path: 共享的路径
        * browseable: 是否该共享目录可以展示能够共享的列表
        * read only: 定义的合法用户是否有可写的权限
        * force create mode: 设置新创建文件的权限
        * force directory mode: 设置新创建目录的权限
        * valid users: 允许访问的用户和用户组列表, 用户组使用 @ 前缀表示.
3. **客户端连接:** 
   * Windows: 
     * 在**此电脑**上右击, 然后点击映射网络驱动器.
     * 然后输入服务器地址和要共享的文件夹:
     `\\192.168.1.181\\sambauser`, 然后输入对应的用户名和密码即可. 
4. **注意:**
   * 更改了配置之后需要重启服务:   
   `systemctl restart smb.service`   
   `systemctl restart nmb.service`
   * 若是客户端连接不上, 则执行:   
   `setenforce 0` 
5. **参考:**  
    [1]: [How to Install and Configure Samba on CentOS 7](https://linuxize.com/post/how-to-install-and-configure-samba-on-centos-7/)   
    [2]: [CentOS7中Samba环境搭建实录](https://blog.csdn.net/wc1695040842/article/details/91866500)  
    [3]: [网络文件夹目前是以其他用户名和密码进行映射的——映射盘更换登录用户名问题](https://blog.csdn.net/hao134838/article/details/50663359)