# 三、[Git 分支](https://www.progit.cn/#_git_branching)

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [三、Git 分支](#三-git-分支httpswwwprogitcn_git_branching)
  - [3.1 分支简介](#31-分支简介)
    - [3.1.1 分支创建](#311-分支创建)
    - [3.1.2 分支切换](#312-分支切换)
  - [3.2 分支的新建与合并](#32-分支的新建与合并)
    - [3.2.1 新建分支](#321-新建分支)
    - [3.2.2 分支合并](#322-分支合并)
    - [3.2.3 遇到冲突时的分支合并](#323-遇到冲突时的分支合并)
  - [3.3 分支管理](#33-分支管理)
  - [3.4 分支开发工作流](#34-分支开发工作流)
    - [3.4.1 长期分支](#341-长期分支)
    - [3.4.2 特性分支](#342-特性分支)
  - [3.5 远程分支](#35-远程分支)
    - [3.5.1 推送](#351-推送)
    - [3.5.2 跟踪分支](#352-跟踪分支)
    - [3.5.3 拉取](#353-拉取)
    - [3.5.4 删除远程分支](#354-删除远程分支)
  - [3.6 变基](#36-变基)

<!-- /code_chunk_output -->

## 3.1 分支简介

分支：将工作从主线上剥离出来。

git 如何保存数据：

- git 保存的是文件快照
- 进行提交操作时，git 会创建提交对象，该对象会包含一个指向暂存内容快照的指针，还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。
- 首次提交的对象没有父对象，普通提交的对象有一个父对象。由多个分支产生合并的提交对象有多个父对象。
- 暂存操作会计算校验和，然后会把当前版本的文件快照保存到 Git 仓库中（Git 使用 blob 对象来保存它们），最终将校验和加入到暂存区域等待提交
- 当使用 git commit 进行提交操作时，Git 会先计算每一个子目录（本例中只有项目根目录）的校验和，然后在 Git 仓库中这些校验和保存为树对象。 随后，Git 便会创建一个提交对象，它除了包含上面提到的那些信息外，还包含指向这个树对象（项目根目录）的指针。

Git 的分支，其实本质上仅仅是指向提交对象的可变指针。

### 3.1.1 分支创建

会在当前所在的提交对象上创建一个指针

```shell
git brash [分支名]
```

HEAD 指针指向当前所在的本地分支。

查看各个分支所指向的对象：

```shell
git log --oneline --decorate
```

### 3.1.2 分支切换

```shell
git cheackout [分支名]
```

输出你的提交历史、各个分支的指向以及项目的分支分叉情况：

```shell
git log --oneline --decorate --graph --all
```

> 分支切换会改变工作目录的文件内容，如果 git 不能很好的完成修改文件内容，那么它会禁止这个操作。

## 3.2 分支的新建与合并

### 3.2.1 新建分支

新建并同时切换到该分支上：

```shell
git checkout -b [分支名]
```

合并分支：

```shell
git merge [被并合并的分支名]
```

删除分支：

```shell
git branch -d [分支名]
```

### 3.2.2 分支合并

git 会自行决定哪一个提交作为最优的共同祖先。

### 3.2.3 遇到冲突时的分支合并

遇到合并冲突时，git 在合并的时候会暂停下来，等待用户去解决冲突。

查看因合并合并冲突，而处于未合并状态的文件：

```shell
git status
```

出现冲突的文件会包含一些特殊字段：

```shell
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

HEAD 所指示的版本，在这个区段的上半部分（======= 的上半部分）。

解决冲突：

- 保留一个修改
- 删除<<<<<<< , ======= , 和 >>>>>>>
- 图形化工具解决：`git mergetool`

## 3.3 分支管理

获取分支列表：

```shell
git branch
```

支前的 \* 字符：它代表现在检出的那一个分支

查看每个分支的最后一次提交：

```shell
git branch -v
```

可以过滤这个列表中已经合并或尚未合并到当前分支的分支:

```shell
git branch --merged
git branch --no--merged
```

强制删除某个分支：

```shell
git branch -D [分支名]
```

## 3.4 分支开发工作流

### 3.4.1 长期分支

只在 master 分支上保留完全稳定的代码——有可能仅仅是已经发布或即将发布的代码。 他们还有一些名为 develop 或者 next 的平行分支，被用来做后续开发或者测试稳定性——这些分支不必保持绝对稳定，但是一旦达到稳定状态，它们就可以被合并入 master 分支了。

![alt](https://www.progit.cn/images/lr-branches-2.png)

### 3.4.2 特性分支

特性分支是一种短期分支，它被用来实现单一特性或其相关工作。

![alt](https://www.progit.cn/images/topic-branches-2.png)

## 3.5 远程分支

显式获取远程分支的所有列表：

```shell
git remote show (remote)
```

命名形式：`(remote)/(branch)`

修改默认远程仓库名：

```shell
git clone -o [远程仓库名]
```

抓取本地没有的数据，并更新本地数据库：

```shell
git fetch [远程仓库名]
```

添加一个远程仓库并简写：

```shell
git remote add [简写] [url]
```

### 3.5.1 推送

推送分支:

```shell
git push (remote) (branch)
```

eg:`git push origin serverfix:serverfix`:“推送本地的 serverfix 分支，将其作为远程仓库的 serverfix 分支”

当抓取到新的远程跟踪分支时，本地不会自动生成一份可编辑的副本（拷贝）

如果想要在自己的分支上工作，可以将其建立在远程跟踪分支之上：

```shell
git checkout -b [本地分支] [远程跟踪分支]
```

### 3.5.2 跟踪分支

从一个远程跟踪分支检出一个本地分支会自动创建一个叫做 “跟踪分支”（有时候也叫做 “上游分支”）。 跟踪分支是与远程分支有直接关系的本地分支。

设置跟踪分支：

```shell
git checkout -b [branch] [remotename]/[branch]
```

快捷操作：

```shell
git checkout --track [remotename]/[branch]
```

设置已有的本地分支跟踪一个刚刚拉取下来的远程分支，或者想要修改正在跟踪的上游分支：

```shell
git branch -u [remotename]/[branch]
```

查看设置所有的跟踪分支：

```shell
git branch -vv
```

该命令并没有连接服务器，它只会告诉你关于本地缓存的服务器数据。统计最新的数据：

```shell
git fetch --all; git branch -vv
```

### 3.5.3 拉取

git fetch：

- git fetch 命令从服务器上抓取本地没有的数据时，它并不会修改工作目录中的内容。 它只会获取数据然后让你自己合并。

git pull：

- 大多数情况下它的含义是一个 git fetch 紧接着一个 git merge 命令。
- 尝试合并

### 3.5.4 删除远程分支

删除远程分支：

```shell
git push [remotename] --delete [branch]
```

只是从远程仓库上移除指针，git 服务器通常会保存一段时间，直到垃圾回收运行。

## 3.6 变基

整合不同分支的两种方法:

- merge
- rebase

变基：

- 把某一分支的修改移动到另一分支上。
- 使得提交历史变得简洁。

取出 client 分支，找出处于 client 分支和 server 分支的共同祖先之后的修改，然后把它们在 master 分支上重放一遍：

```shell
git rebase --onto master server client
```

### 3.6.1 变基的风险

不要对在你的仓库外有副本的分支执行变基。

### 3.6.2 用变基解决变基

Git 除了对整个提交计算 SHA-1 校验和以外，也对本次提交所引入的修改计算了校验和—— 即 “patch-id”。

如果遇到前面提到的 有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交 那种情境，如果我们不是执行合并，而是执行`git rebase [orgin]/[branch]`, Git 将会：

- 检查哪些提交是我们的分支上独有的
- 检查其中哪些提交不是合并操作的结果
- 检查哪些提交在对方覆盖更新时并没有被纳入目标分支
- 把查到的这些提交应用在 [orgin]/[branch] 上面

另一种方法：

```shell
git pull --rebase
```

等价于：

```shell
git fetch
git rebase [orgin]/[branch]
```

### 3.6.3 变基与合并

对尚未推送或分享给别人的本地修改执行变基操作清理历史，从不对已推送至别处的提交执行变基操作。
