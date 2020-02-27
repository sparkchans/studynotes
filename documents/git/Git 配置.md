##### Git 配置设置

```shell
### git 配置文件分为全局的和局部的
### 系统配置文件在 /etc/gitconfig 文件
### 用户配置文件在当前用户工作路径下的 gitconfig 文件
### 局部配置文件在 .git/gitconfig 文件
# 配置系统配置
git config --system user.name sparkchan
git config --system user.email sparkchan@gmail.com

# 配置用户配置
git config --global user.name sparkchan
git config --global user.email sparkchan@gmail.com

# 配置局部配置
git config user.name sparkchan
git config user.email sparkchan@gmail.com
# 查看配置
git config --list --show-origin
### 设置别名
# 所有用户
git config --system alias.st status
# 当前用户
git config --global alias.st status

# 删除配置
git config --global --unset st
git config --global --unset-all
# 编辑配置文件
git config --global --edit

### 指定其它配置文件
# 向配置文件 test.ini 中添加配置
GIT_CONFIG=test.ini git config a.b.c.d "hello"
# 从配置文件 test.ini 中读取配置
GIT_CONFIG=test.ini git config a.b.c.d
```

##### Git 版本库位置查看

```shell
# 显示版本库 .git 目录所在位置
git rev-parse --git-dir
# 显示工作区根目录
git rev-parse --show-toplevel
# 显示工作区根目录的相对目录
git rev-parse --show-prefix
# 显示从当前目录后退到工作区的根的深度
git rev-parse --show-cdup
```

##### Git 修正提交人信息

```shell
# 查看提交信息
git log --pretty=fuller
# 配置提交人信息
git config user.name sparkchan_amend
# 修正提交信息
git commit --amend --allow-empty --reset-author
```

##### 参考

[1] : [How to Install latest version of Git ( Git 2.x ) on CentOS 7](https://computingforgeeks.com/how-to-install-latest-version-of-git-git-2-x-on-centos-7/)

[2] : [Can't Find Global .gitconfig FIle](https://stackoverflow.com/questions/39670247/cant-find-global-gitconfig-file)