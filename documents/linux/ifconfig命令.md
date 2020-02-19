1. ifconfig 命令:

   - -a: 显示所有网络接口信息, 包括活动的和非活动的

   - -s: 显示每个接口的摘要数据, 是有关接口活动性的, 每个接口显示一行信息.

   - up: 激活一个网络接口

     ```shell
     ifconfig ens33 up
     ```

   - down: 使网络接口无效

     ```shell
     ifconfig ens33 down
     ```

   - 为一个网络接口配置多个 IP 地址:

     ```shell
     # 临时配置
     sysconfig ens33:0 192.168.0.7 netmask 255.255.255.0
     # 永久配置
     cp /etc/sysconfig/network-scripts/ifcfg-ens33 /etc/sysconfig/network-scripts/ifcfg-ens33:0
     # 配置文件配置
     BOOTPROTO=static
     IPADDR=192.168.0.107
     NETMASK=255.255.255.0
     GATEWAY=192.168.0.1
     NAME=ens33:0
     DEVICE=ens33:0
     ```

   - netmask: 为一个指定的网络接口指定子网掩码

     ```shell
     ifconfig ens37 netmask 255.255.0.0
     ```

   - addr: 为网络接口指定的 IP 地址.

     ```shell
     ifconfig ens37 addr 192.168.0.108
     ```

   - broadcast:  为网络接口指定广播地址

     ```shell
     ifconfig ens37 broadcast 192.168.0.255
     ```

     