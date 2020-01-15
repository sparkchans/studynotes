1. **Linux 文件拥有者,群组,其它人:**  
    Linux 文件的权限分为三个部分的权限: 文件拥有者的权限, 群组的权限以及其它人的权限. 文件的拥有者默认为文件的创建者; 文件的群组默认为文件创建者所在的群组; 其它人指的是除去文件拥有者和群组用户的其它用户.   

    Linux 用户信息记录在`/etc/passwd`文件内, 密码记录在`/etc/shadow`文件内, 用户组记录在`/etc/group`内.
2. **目录与文件权限的意义:**  
   * 对于文件:
     * r(read): 表示可以读取文件的内容.
     * w(write): 可以编辑, 新增或修改文件的内容.
     * x(execute): 操作系统根据是否具有该权限来判断该文件是否可以被执行. 这与 Windows 系统根据文件后缀名来判断不一样.
   * 对于目录:
     * r(read contents in directory): 表示你可以查询该目录下的文件名列表.
     * w(modify contents of directory): 因为对于目录这种文件来说, 它的 block 中存放的是该目录下的文件名. 因此具有目录的 w 权限说明可以更改该目录下的文件名.
     * x(access directory): 目录的 x 代表的是你能不能进入到该目录. 这好比你要查看一个房间的东西, 首先你需要拿到这个房间的钥匙才能进入到房间, 而 x 权限的作用就类似于钥匙.  
总的来说就是: 对一般文件来说, rwx 主要是针对**文件的内容**来设计权限, 对目录来说, rwx 是针对**目录内的文件列表**来设计权限. 
3.**更改文件权限的命令:**
* chgrp: 
  * 改变文件所属群组:`chgrp -R groupName fileName` 
    * 示例: `chgrp -R root testDirectory`   
    R 表示更改该目录及目录下所有的文件和目录的用户组. 
* chown: 
  * 改变文件拥有者:`chown -R username fileName`
    * 示例: `chown -R root testDirectory` 
  * 同时改变文件拥有者和用户组:`chown -R username:groupName fileName`
    * 示例: `chown -R root:root testDirectory`
* chmod:改变文件的权限
  * 数字方式: 数字方式就是把 rwx 看作一个二进制数据, 每一个位 1 表示有该权限, 0 表示没有该权限.
    * 示例: `chmod -R 700 testDirectory`
  * 符号方式: u, g, o 分别表示 user, group, others.   
  `|chmod|u g o a| +(加入) -(除去) =(设置)|r w x| 文件|`
    * 设置权限: `chmod u=rwx,go=rx testDirectory`
    * 增加权限: `chmod a+w testDirectory`
    * 去掉权限: `chmod a-x testDirectory`
     