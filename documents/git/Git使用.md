##### 基本使用

- ###### 初始化版本库	

    ```shell
    git init
    ```
    
- ###### 将修改从工作区添加到暂存区

    ```shell
    git add fileName
    ```

- ###### 将暂存区的修改提交
    ```shell
    git commit -m "commit message"
    ```

- ###### 提交记录查看
    ```shell
    git log
    ```

- ###### **回退版本**
    ```shell
    # 可以用 HEAD~100 指定版本
    git reset --hard HEAD^
    ```

- ###### 前进到之前的版本	
    ```shell
    git reflog
    git reset --hard 版本号ID
    ```

##### 概念理解

- ###### 工作区(Working Direcory)

    电脑中能够看到的目录

- ###### 版本库(Repository)

    - ###### 暂存区(Stage)

        git add 时将修改提交到暂存区

    - ###### 分支(Branch)

        默认分支为 master。git commit 就是将暂存区的所有修改提交到当前分支。

##### 参考

[1] : [Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600)

[2] : [Git 本地仓库操作](https://www.cnblogs.com/feeland/p/4500721.html)

[3] : [Git 分支 - 分支的新建与合并]([https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6](https://git-scm.com/book/zh/v2/Git-分支-分支的新建与合并))