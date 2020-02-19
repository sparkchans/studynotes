1. scp: 将文件从一台主机复制到另一台主机

   - -r: 递归复制

   - 复制文件到本机

     ```shell
     scp root@192.169.0.105:/home/chenwei/1.txt ./
     ```

   - 复制到其它主机

     ```shell
     scp 1.txt root@192.168.0.106:/home/chenwei/
     ```

     