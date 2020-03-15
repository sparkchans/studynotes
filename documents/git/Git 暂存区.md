##### diff 命令比较

```shell
# 工作区和暂存区比较
git diff
# 工作区和版本库比较
git diff HEAD
# 暂存区和版本库比较
git diff --cached
```

##### git status -s 说明

- 第一列 M

    版本库和暂存区的文件相比有改动

- 第二列 M

    工作区和暂存区的文件相比有改动

##### 暂存区

执行 `git status(git diff)` 命令时，先根据 `.git/index` 文件记录的时间戳，长度等信息判断工作区文件是否改变，如果工作区文件的时间戳改变了，说明文件的内容可能被改变了，需要打开文件，读取文件内容，与更改前的原始文件相比较，判断文件是否更改。如果文件内容没有更改，则将该文件新的时间戳记录到 `.git/index` 文件中。

- 执行 `git add` 命令时，暂存区的目录树将被更新，同时工作区修改的文件内容会被写入到对象库中的一个新的对象中，而该对象的 ID 被记录在暂存区的文件索引中。
- 执行 `git commit` 命令时，暂存区的目录树会写入到版本库中。master 最新指向的目录树就是提交时原暂存区的目录树（这里的提交本质是就是将 master 指针指向当前暂存区目录树，也就是一个指针的移动过程）。
- 执行 `git reset HEAD` 命令时，暂存区的目录树会被重写，工作区则不做出改变（本质上就是将暂存区指针指向 master）
- 执行 `git rm --cached <file>` 命令时，直接从暂存区删除文件，工作区不会做出改变。
- 执行 `git checkout .` 或 `git checkout -- <file>` 命令时，会用暂存区全部或指定的文件替换工作区的文件。
- 执行 `git checkout HEAD .` 或 `git checkout HEAD <file>` 命令时，会用 master 分支中的全部或部分文件替换暂存区和工作区的文件。

##### 查看暂存区及 HEAD 目录树

```shell
# 查看 HEAD 目录树
git ls-tree -l HEAD
# 查看暂存区目录树
git ls-files -s
git write-tree | xargs git ls-tree -l -r -t
```

