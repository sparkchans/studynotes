1. 无法上网问题解决:

   ```shell
   cd /etc/sysconfig/network-scripts
   vi ifcfg-ens33
   change ONBOOT from no to yes
   service network start
   ```

参考[解决Centos 7安装完成之后无法联网](https://blog.csdn.net/sinat_32079337/article/details/70238107)

