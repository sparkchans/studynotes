## Linux 命令之 netstat 命令

netstat 命令用来显示本机网络连接，运行端口和路由表等信息。

### netstat 命令支持的选项：

- -a: 显示本机所有连接和监听端口

- -n: 以网络 IP 地址的形式显示当前建立的有效连接和端口

- -r: 显示路由表信息

- -s: 显示按协议的统计信息

- -c: 表示每隔几秒刷新一次

- -i: 显示接口状态

- -p: 显示连接对应的 PID 与程序

- -l: 仅显示连接状态为 LISTEN 的服务的网络状态

### netstat 命令使用示例：

- 显示所有的 TCP 连接：

  `netstat -ant`

  其中的 State 列为 TCP 连接的状态，包括：LISTEN，SYN_SENT，SYN_RECEIVED，TIME_WAIT，FIN_WAIT1，FIN_WAIT2。

  ![image-20201011211235732](C:\Users\15003\AppData\Roaming\Typora\typora-user-images\image-20201011211235732.png)

- 查看对外开放端口

  `netstat -nltp`

  Local Address 为 0.0.0.0 表示该端口对外开放；为 127.0.0.1 表示只能本机访问。

  ![image-20201011213437040](C:\Users\15003\AppData\Roaming\Typora\typora-user-images\image-20201011213437040.png)

### 参考

[1] : [Linux的netstat查看端口是否开放见解（0.0.0.0与127.0.0.1的区别）](https://www.cnblogs.com/lemon-flm/p/7396536.html)

[2] : [[meaning of Netstat local address column](https://unix.stackexchange.com/questions/139938/meaning-of-netstat-local-address-column)](https://unix.stackexchange.com/questions/139938/meaning-of-netstat-local-address-column)