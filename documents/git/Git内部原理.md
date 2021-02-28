## Git 内部原理

### Git 对象类型

- Blob：

  在 git 中，文件的内容被存放在 Blob 对象中。如果你有两个文件不同名，但是文件内容相同，git 只会保存一份内容。

- Tree：

  Tree 包含了 tree 和 blob 对象。

- Commit：

  Commit 对象指向一个 tree，保留了作者，提交者，提交信息，父 commit 等信息。

- Tag：

  标签给一次提交提供了一个永久的名称。

### 引用

Git 中的引用主要有 branches 和 remotes。Git 中的 branch 仅仅是 .git/refs/heads/ 目录中的文件，包含了最近的 commit 的 SHA-1 。

### 分支

Git 中创建分支只是在 .git/refs/heads/ 目录中的创建一个文件。

Remotes：

Remotes 是一个指向其它电脑中相同代码库分支的指针。