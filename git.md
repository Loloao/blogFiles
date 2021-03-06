# Git
在刚工作时，git 对于我来说就是一个黑盒子，我的每一次 git 操作都笼罩在这个操作会不会把我的或是别人的代码删除的阴影中，特别是那些传说中的`git merge`，`git cherry pick`将代码丢失的传说，更是让我在使用 git 时胆战心惊。
但是，面对困难必须迎难而上才能解决，git 也一样，只有熟知 git 原理，才能高效率地使用它。

## What is Git
git 和其他 VCS 系统最大的区别在于 git 如何认定它的数据，git 认为它的数据应该是一系列的快照`snapshot`
在 git 储存数据之前会进行`checksum`和校验，所以不会在修改了文件或目录之后，git 一无所知
git 使用 SHA-1 算法计算数据的校验和，通过对文件的内容和目录的结构计算出一个 SHA-1 哈希值作为指纹字符串，它由 14 个字符组成看起来像是
`24b9da6552252987aa493b52f8696cd6d3b00373`
所有保存在 git 数据库中的东西都是依赖这个字符串作为索引
git 的大部分操作都是仅仅把数据推送到数据库

## git 基础
在你的工作目录中的每个文件只会有两种状态：`tracked`或是`untracked`。
`tracked`文件存在最后一个快照`snapshot`中，它们可能为`unmodified`，`modified`和`staged`。
`untracked`文件不存在最后一个快照中，或是不存在`staging area`
![文件生命周期状态](https://git-scm.com/book/en/v2/images/lifecycle.png)
### `git status`判断文件处于哪个阶段
- `Changes not staged for commit`表示文件已经更改过但是还没有`stage`
- `Changes to be commited`表示文件`stage`过，但是还没有`commit`
- `Untracked files`表示你的那些文件不处于上一个快照`commit`中
- `unmerged paths`表示存在没有解决冲突的文件
- `-s --short`可以显示简略的文件状态信息，开头的两列表示文件状态，第一列表示`stage area`，第二列表示`working tree`状态
  ```shell
  $ git status -s 
  M README ## 第二个 M 在 working tree 已经更改但还没有 staged
  MM Rakefile
  A  lib/git.rb ## 文件被 add 进 stage area
  M  lib/simplegit.rb ## 第一个 M 表示更改过且已经 staged
  ?? LICENSE.txt ## untracked files
  ```
### `git diff`用于显示更精确的更改信息
它会显示文件每行更改过的信息。`git diff`不加参数可以显示未`staged`的更改
- `--staged`会对比`staged`的内容和最后一次快照
- `--cached`和`--staged`同义
> 可以使用`git difftool`代替`git diff`，它允许你使用其他工具浏览`git diff`，具体使用`git difftool --tool-help`

### `git add <files>`
用于`tracking`文件，接收一个路径，如果是文件夹，会递归`add`文件夹中的内容
- `-i | --interactive`可用来交互式地 stage 内容
- `-p | --patch`可用来 stage 文件中的特定行

### `git reset HEAD <file>`
将文件从`staging area`移除，`git status`可以提示
- `--patch`用于回退部分文件

### `git checkout`
- `-- <file>`用于将文件从更改状态变为未更改状态，`git status`可以提示，这个命令很危险，它会把文件还原为上一次 commit 的状态
- `git checkout <branchname>`用于切换分支，会将`HEAD`指向指定的分支
- `-b <newbranchname>`用于创建一个分支并切换到新分支
- `-b <newbranchname> <remote>/<branchname>`用于将远程分支拉到本地
- `-track <remote>/<branchname>`和上面的命令作用相同，但是不能自己定义分支名，分支名自动为`branchname`
- `--patch`切换`checkout`部分文件

### `git switch`
从 git 2.23 开始用于代替`git checkout`切换分支
- `git switch <branchname>`切换分支
- `-c | --create`用于创建并切换到新分支
- `-`返回到上次切换的分支

### `git restore`
git 2.23.0 新出的命令，可以在许多回退操作中代替`git reset`
- `git restore <file>`可以将文件回退为上次 commit 的状态
- `--staged`用于将`stagding area`中的文件回退

### `git commit`
`commit`只是保存了你的`stage area`的一个快照
- `-m`能够单行输入一个`commit message`。
- `-a -m`能够将未被`stage`的内容全部进行`add`并`commit`
- `--amend`当忘记`add`文件，或是写错了`commit message`，可以先 stage 再重新 commit
  ```shell
  git commit -m "Intial commit"
  git add forgotten_file
  git commit -amend
  ```
> 当你 amend 上一个 commit 时，你是创建了一个新的 commit 来代替旧的 commit，就想上一次`commit`从没发生过一样
> 当你在本地 amend 一个已经 push 的 commit，push 时会报错

### `git rm`
能够精确地将文件从`staging area`移除，同时也会把把它从你的工作目录中移除，这样你也不会见到它在你的`Untracked files`中
- `-f`如果已经更改了文件或是已经在`staging area`就可以使用此选项移除，但它不能被恢复
- `--cached`用于将文件从`staging area`移除但不会从工作文件夹中移除
- 你能使用文件，文件夹，或是`glob parttern`路径匹配器，比如`git rm \*~`

### 移动文件
和 VCS 系统不同，git 不会跟踪文件的移动，如果你想在 git 中`rename`一个文件，可以使用
`git mv file_from file_to`
但是，它和以下命令是相同效果
```shell
mv README.md README
git rm README.md
git add README
```

### `git log`
这是最基本或是最基本的命令用来查看`commit history`，直接使用会展示这个仓库按时间倒序排列的`commit`。
`git log`有非常多的选项，这里只展示几个最受欢迎的选项
- `-p | --patch`它会展示每个 commit 的不同，可以通过`-num`用于限制显示的数量
- `--stat`可以显示缩略信息，它会展示哪些文件被改变过，以及在文件中增加或是删除的行数
- `--pretty`它会根据`format`格式来进行显示，比如
  - `git log --pretty=oneline`单行显示`commit`
    ```shell
    $ git log --pretty=oneline
    ca82a6dff817ec66f44342007202690a93763949 Change version number
    085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 Remove unnecessary test
    a11bef06a3f659402fe7563abf99ad00de2209e6 Initial commit
    ```
  - `git log --pretty=format:"%h - %an, %ar : %s"`
    ```shell
    $ git log --pretty=format:"%h - %an, %ar : %s"
    ca82a6d - Scott Chacon, 6 years ago : Change version number
    085bb3b - Scott Chacon, 6 years ago : Remove unnecessary test
    a11bef0 - Scott Chacon, 6 years ago : Initial commited
    ```
- `--pragh`可以显示一个文件的branch图，也可以加上 format 进行格式化
这个命令不会展示所有分支的 commit 信息，它只会展示当前分支的 commit 信息
- `git log <branchname>`可以展示对应分支的 commit 信息
- `--all`可展示所有分支的 commit 信息
- `--decorate`可以显示当前指针指向哪个分支指针
- `--abbrev-commit`可以使展示的 commit 的 SHA-1 值只有 7 位
很多命令可以限制 log 输出
- `--since | --until`可以限制某个时间段的 commit 展示，比如`--since=2.weeks`
- `--author`用于展示每个 commit 作者的 commit
- `--grep`通过`commit message`中的字段进行筛选
- `-S`用于查找更改过的信息
- `-- path/to/file`能够过滤更改过这个文件或是文件夹的 commit
- `--no-merges`不显示 merge 的 commit

### `git remote`
远程仓库就是在网上保存的你的项目的一个版本
- `git remote`展示远程仓库的默认服务名，默认为`origin`
- `-v`用于展示服务名和它的 URLs
- `add <shortname> <url>`添加远程仓库
- `show origin`展示远程仓库详细信息
- `rename <oldname> <newname>`用来更改远程仓库名
- `remove <remotename>`用于删除服务名
- `git ls-remote <remote>`或是`git remote show <remote>`可以用来更多的 remote 信息，但还是有一种更通用的方法。
`Remote-tracking branches`是根据远程分支的状态进行参考，如果你想看到最后一次链接远程仓库时的 master 分支，你需要`check`这个`origin/master`分支

### `git tag`
用于定义仓库版本，`git tag`可以展示定义的`tag`，直接使用会按照字母顺序排序
- `-l | --list`通过匹配项过滤所有`tag`，比如`git tag -l "v1.8.5*"`
git 支持两种类型的 tag：`lightweight` 和 `annotated`
`lightweight`只是一个特定 commit 的指针
`annotated`会保存 git 数据的完整对象，它会包含数据的检验和`checksum`，包含`tagger name`，`email`和`date`，还有`tag message`，还可以通过 GPG 认证
- `-a <tagname> -m <tagmessage>`用于创建一个`annotated` tag，通过`git show v1.4`可以对信息进行查看
- `git tag v1.4`用于创建一个`lightweight` tag
能够直接为一个 commit 加上一个 tag
- `-a v1.4 <commitHash>`
`git push`不能直接将 tag 传送到远程仓库，你必须使用`git push origin <tagname>`，`git push origin --tags`可一次传送多个 tag
- `-d <tagname>`用于删除 tag，但它不会删除远程仓库的 tag
- `git push origin --delete <tagname>`来删除远程仓库的 tag
- `git push origin --delete <branchname>`用于删除远程仓库的 branch
如果你想展示 tag 的文件版本，可以使用`git checkout`
- `git push <remote> <branchname>`或是`git push <remote> <branchname>:<remoteBranchname>`用于推送你的分支到远程仓库，其中`<remotebranchname>`表示在远程仓库中你的分支名
> 如果你不想在每次 push 的时候输入账号密码，可以使用`git config --global credential.helper cache`解决，更多信息见[这里](https://git-scm.com/book/en/v2/ch00/_credential_caching)

### git alias
别名可以使 git 更简单，熟悉，你能通过`git config`来进行配置
`git config --global alias.co checkout`
`git config --global alias.last 'log -1 HEAD'`用于查看上次的 commit

### `git fetch`
可用来获取远程仓库的数据`git fetch <remote>`将所有的元层仓库数据拉到本地，你能在任何时候 merge，这个命令不会自动 merge
- `--all`用来拉去所有分支的数据

### `git pull`
可以自动 fetch 并 merge

### `git push`
当你想要分享你本地数据时，可以使用`git push <remote> <branch>`

### `git clone`
可将远程仓库数据拉到本地并将远程`master`分支作为本地`master`分支

### Ignore Files
有时， 你会希望一些文件不被 git 自动`add`或是显示`Untracked files`。这时你可以增加一个`.gitignore`文件:
- 空行或是以`#`开头的行会被忽略
- 标准的路径匹配，在`working tree`中将会递归匹配
- 在开头加上`/`可以避免递归匹配
- 在末尾加上`/`可以匹配一个文件夹

## Git Branching
git 的分支模型`branching model`让它和其他 vcs 系统区分开来
git 不会保存一系列项目的变化，它保存的是快照。当你 commit 之后，git 会保存一个 commit 对象保存你`staged`的快照，它同样包含作者名，email 和`commit message`，还有`parent commit`
当你使用`git commit`后，git 会`checksum`每个子文件夹，并把它们当做一个树对象`tree object`存在 git 仓库中，之后 git 会创建`commit object`保存反射的数据和指向项目树`root project tree`的指针，这样就可以在必要时创建快照
你的仓库会包含很多对象：很多`blobs`(每个代表对应的文件内容)，一个树列出文件夹的内容并且指出文件名包含在哪个 blob 中，还有一个`commit`指向树的根节点的指针以及所有的`commit指出文件名包含在哪个 blob 中，还有一个`commit`指向树的根节点的指针以及所有的`commit`反射数据`
![当仓库有三个文件时](https://git-scm.com/book/en/v2/images/commit-and-tree.png)
如果你更改并再次 commit，则下一个 commit 会保存这个 commit 的指针
一个分支在 git 中就是一个轻量`lightweight`，可移动的`movable`指针指向这些 commit 中的某个。每当你进行 commit，branch 就会自动向前移动指向当前 commit
- `git branch <branchname>`创建一个新分支时，相当于会创建一个能够四处移动的新指针，同时文件夹会变为当前分支快照所保存的工作目录
git 如何知道你现在在哪个分支呢，它会保持一个特别的称为`HEAD`的指针。在 git 中，这是一个指向你所在的本地分支的指针
在 git 中一个分支只是用 40 个字符 SHA-1 指向的一个 commit。所以创建一个新分支只是创建一个 41bytes 的文件

### `git branch`
- `-d <branchname>`用于删除本地分支
- `-v`可以查看分支信息和最后一次`commit`信息
- `--merged`和`--unmerged`可以过滤已经合并和未合并到当前分支的分支，也可以加上分支名用于查看
- `-D`用于强制删除分支
- `--move <oldbranchname> <newbranchname>`用于更改分支名，但推送后远程分支依旧存在，此时需要使用`git push origin --delete <oldbranchname>`来进行删除
> 注意当有其他人使用这个分支时，不要更改它的名字。
- `-u`或是`--set-upstream-to`可以将本地分支跟踪其他远程分支
- `-vv`用于查看所有本地分支以及它的远程分支情况，比如
  ```shell
  $ git branch -vv
  # ahead 2 表示本地有两个新的 commit
  iss53     7e424c3 [origin/iss53: ahead 2] Add forgotten brackets
  master    1ae2a45 [origin/master] Deploy index fix
* serverfix f8674d9 [teamone/server-fix-good: ahead 3, behind 1] This should do it
  testing   5ea463a Try something new
  ```

### `git merge`
- `fast-forward`合并分支时，如果原分支没有新的 commit 且新分支有 commit，则 git 只会简单的将原来分支的指针移动到新的 commit 上
- `merge commit`合并分支时，如果原分支和需要合并的分支都有新的 commit 时，git 会创建一个 commit 指向它们共同祖先的快照
![merge commit](https://git-scm.com/book/en/v2/images/basic-merging-1.png)
当你合并分支时在两个分支上更改了同一个文件就会产生一个`conflict`。此时，git 不会自动产生一个`merge commit`，它会暂停合并进程直到你解决冲突
可以使用`git mergetool`来使用一个图形界面工具解决冲突
解决冲突需要在每个冲突文件夹处理对应代码
- `@{u} | @{upstream}`当本身处于对应远程分支上时，可以使用它代替`git merge <remote>/<branchname>`

### `git rebase`
git 有两种把一个分支的改变整合到另一个分支的方法，`merge`和`rebase`
当工作分支分叉时，比如：
```
          c4 experiment
        / 
c1 - c2 - c3 master
```
我们可以我们可以把`c4`的更改重新应用到`c3`的前面，这种叫做`rebasing`
```
          c4 experiment // 移除
        / 
c1 - c2 - c3 - c4 expriment // 新增
```
比如你想将`experiment`分支`rebase`到`master`上
```shell
$ git checkout experiment
$ git rebase master
```
此时的工作流如下
```
        master
c1 - c2 - c3 - c4 expriment
```
此时再进行以下命令
```shell
$ git checkout master
$ git merge expriment
```
此时的工作流如下
```
              expriment
c1 - c2 - c3 - c4 master
```
它会展示一条线性的历史，在最终的成果中和其他整合分支的效果并不会有什么不同
它最终指向的快照就是最后一次的 commit
- `--onto <branchname1> <branchname2> <branchname3>`可以将基于`branchname2`分支而非`branchname1`的改变，跳过`branchname2`直接放到`branchname1`中重演
- `git rebase <主分支> <特性分支>`可去除特性分支并在在主分支上重演
```shell
$ git checkout <主分支>
$ git merge <特性分支>
```
*一旦分支中的提交对象发布到公共仓库中，就不要对该分支进行`rebase`操作*
你必须遵守这个准则，不然人们会讨厌你，你的家人和朋友会嘲笑你。因为`rebase`操作实际上是抛弃了现存的提交对象并创建了一些不同的提交对象
所以在`rebase`分支后在进行`pull`后会有之前的更改覆盖当前内容。因此需要把`rebase`当成一种在推送之前清理提交历史的手段

### Branching Workflow
因为 git 处理分支的成本很低，所以你可以开放很多个分支用于处理项目的多个阶段
- `Long-Running Branches`许多开发者会有这种分支作为基本分支，同时会有其他分支比如`develop`，`test`或是`next`用于不同的地方，之后会在合适的时机再将它们合并到基本分支
- `Topic Branches`这种分支在任何大小的项目中都很实用，它的生命周期很短，创建出来只是为了实现一个特别的`feature`或有关的工作，之后直接和比恩
  在这种分支中你的更改能保存很久，而且它会让你在 code review 时更直观方便。
具体详情见[这里](https://git-scm.com/book/en/v2/ch00/ch05-distributed-git)

## Git Tools
有些 git 工具可能你每天不会用，但在一些情况下自有它的用法

### 修订版本`Revision Selection`
git 允许你有特定的一种或几种方法来指定特定的或一定范围内的提交
- `Single Revision`你可以使用给出的 SHA-1 值来指明一次提交，不过也有更简单的方法来指明。
- `Short SHA-1`git 可以通过你提供的前几个字符来识别你想要的那次提交，只要你提供的部分 SHA-1 值不少于四个字符
- `Branch Refrence`指明一次提交的最直接的方法要求有一个指向它的分支引用。这样，你就可以在任何需要一个提交对象或是 SHA-1 值的 git 命令中使用该名称了，比如你想显示一个分支最后一次提交的 commit 对象，可以：
`git show <branchname>`
- `rev-parse`可以看出分支指向哪个 SHA-1
- `git reflog`可以用来看本地的引用日志，如果想看 HEAD 在前五次的值，可以使用`git show HEAD@{5}`或是查看`master`分支昨天在哪，可以使用`git show master@{yesterday}`
- `Ancestry References`还有其他朱洋达方法去指明某次提交是通过它的祖先，如果你在引用最后加上一个`^`，则 Git 将其理解为此次提交的父提交
`git show HEAD^`可在最后加个数字，用于看第几个父祖先，这种语法只在合并提交时有用，因为合并提交可能有多个父提交，第一父提交是你合并时所在的分支，第二父提交是你所合并的分支
`git show HEAD~`如果不加数字和`git show HEAD^`一样，但当加上数字比如`2`时，表示的是第一父提交的第一父提交
- `Commit Ranges`用于指明一定范围的提交，比如可以看出这个分支上有哪些分支没有合并到主分支
`git log <branchname1>..<branchname2>`中的`..`可让 git 区分出可从`branchname2`分支中获得而不能从`branchname1`分支中获得提交，这个语法的常见用途是查看你将吧什么推送到远程
`$ git log origin/master..HEAD`此命令可以省略`HEAD`
还可以使用`^`或是`--not`选项来指明你不希望包含的分支，以下三个命令等价
```shell
$ git log refA..refB
$ git log ^refA refB
$ git log refB --not refA
```
`$ git log master...experiment`中的`...`这个可以指定被两个引用中的一个包含但又不被两者同时包含的分支

### 交互式暂存`Interactive Staging`
Git提供了很多脚本来辅助某些命令行任务。这里，你将看到一些交互式命令，它们帮助你方便地构建只包含特定组合和部分文件的提交。
`git add -i | --interactive`可输入交互式的命令来进行交互，此时会出现以下内容
```shell
$ git add -i
           staged     unstaged path
  1:    unchanged        +0/-1 TODO
  2:    unchanged        +1/-1 index.html
  3:    unchanged        +5/-1 lib/simplegit.rb

*** Commands ***
  1: [s]tatus     2: [u]pdate      3: [r]evert     4: [a]dd untracked
  5: [p]atch      6: [d]iff        7: [q]uit       8: [h]elp
What now>
```
- `2 | u`可选择哪个文件进行`stage`
- `3 | r`可对已暂存文件进行`unstage`
- `6 | d`可查看已缓存文件的 diff，类似于`git diff --cached`
- `5 | p`可缓存对印文件的特定行

### `Stashing and Cleaning`
当你在当前分支有很多杂乱的工作，但此时却需要切换到其他分支工作，问题是，你不希望 commit 当前进行了一半的工作，此时，可以使用`git stash`
> 在 2017 年十月份之后，最好用`git stash push`代替原来的`git stash save`
- `git stash`会将当前的更改储存到一个栈中，使用
- `git stash list`可查看你储存的`stashs`，此时会展示
```shell
$ git stash list
stash@{0}: WIP on master: 049d078 Create index file
stash@{1}: WIP on master: c264051 Revert "Add file_size"
stash@{2}: WIP on master: 21d80a5 Add number to log
```
此时可以应用一个 stash 使用
- `git stash apply <stashname>`如果没有指定`stashname`则会应用于上一个 stash
你可以在其中一个分支上进行 stash，随后切换到另外一个分支，再重新应用。在工作目录里包含已修改和未提交的文件时，你也可以应用 stash，如果有任何冲突则 stash 不会被应用
- `git stash apply --index`可以重新应用被暂存的变更，因为未暂存的更改会恢复，但暂存的文件却不会被再次被暂存
- `git stash drop <stashname>`
- `git stash pop`可将 stash 立即应用之后删除
- `git stash show -p <stashname> | git apply -R`当你应用了 stash 之后，进行了一些更改，又想取消之前的 stash，就可以使用这个命令，`stashname`未指定时，会指定为上一次 stash
可以为这个命令加一个别名`git config --global alias.stash-unapply 'git stash show -p <stashname> | git apply -R'`
- `git stash branch <newBranchname>`当你在你 stash 后的地方做了更改，而你重新应用 stash 将引起冲突，此时可以使用这个命令将当前 commit 移动当新分支上并应用当前更改，如果成功会删除原来的 stash
- `git clean`当你不想 stash 更改，只想丢弃当前更改时，可以使用这个命令，它会清除当前为`Untracked`的更改，但是注意这是*不可逆*的。可以使用`git stash --all`来代替
- `git clean -n | --dry-run`可以查看如果运行`git clean`时会移除哪些文件
- `git clean -n -x`直接使用`git clean`不会移除被 git ignore 的文件，而使用`-x`选项可将被 git ignore 的文件一并删除
- `git clean -i`可进行交互式的删除操作

### Signing Your Work
如果你想你的项目的 commit 来自于一个可信的来源，Git 有一些方法让你登陆并使用 GPG 来验证工作
首先你需要一个私人的 key
- `$ gpg --list-keys`可以看到你的 key
- `$ gpg --gen-key`可以生成 key，一旦你有一个私人的 key 去登陆，你就能配置 Git 使用`user.signingkey`去登陆
- `$ git config --global user.signingkey <pubkey>`可用来配置 git 的私人登陆 key
- `git tag -s <newtag>`将`-s`代替`-a`选项可以发现自己的 GPG 信息依附在 tag 上
- `git tag -v <tagname>`可进行 gpg 验证
- `git commit -S`可进行 gpg 验证
- `git log --show-signatrue`可展示当前的验证信息
- `git log --pretty="format:%h %G? %aN  %s"`中的`%G`也能展示验证信息
如果想要使用这种登陆验证，就需要你团队的每个人知道如何进行操作

### Git Tools - Searching
无论多大的代码量，都需要查看函数的调用或定义，或者方法的历史。git 提供了一些工具来查找这些信息
- `git grep`允许你在工作目录中简单地进行查找
- `-n | --line-number`可打印出匹配项的行数
- `--count`打印出那些文件包含匹配项
- `-p | --show-function`展示匹配的方法或是函数
- `--and`哪行能够匹配多个匹配条件
- `git log -S <searchString>`可用于展示含有匹配项的 commit
- `-G`可使用正则进行匹配
- `-L`可展示函数调用历史和它的行数，它也可接收一个正则

### Rewriting History
在将你的工作分享给其他人前，你能在任意时刻修订你的 commit
> 请在你很满意你的工作之后再将它推送到远程仓库
- `git commit --amend`将会打开一个`edit session`中并在其中修改`commit message`，另外注意它和上一个 commit 的 SHA-1 值不同，就像一个小的 rebase，不要 amend 一个已经 push 的 commit
  `git commit --amend --no-edit`可以在不改变`commit message`的情况下更改 commit
> `git commit --amend`更改的不仅是`commit message`同时还有 commit 的内容
虽然 git 没有工具改变`commit history`，但是你能使用
`git rebase -i`互动式地使用`git rebase`，它会打开一个编辑器，你需要在其中编辑`rebase script`
  - `e | edit`可编辑对应的 commit
    ```shell
    # 将 pick 改为 edit
    edit f7f3f6d Change my name a bit
    pick 310154e Update README formatting and add blame
    pick a5f4a0d Add cat-file
    ```
    保存之后 git 会将对应的 commit 作为最后一个 commit 此时使用
    `git comit --amend`
    更改 commit 之后退出，再使用
    `git rebase --continue`后，后面的 commit 会自动加入到分支中
  - 可以直接在`rebase script`中改变 commit 顺序
  - `s | squash`可以将几个 commit 合并为一个
    ```shell
    pick f7f3f6d Change my name a bit
    squash 310154e Update README formatting and add blame
    squash a5f4a0d Add cat-file
    ```
    保存并退出之后，git 会打开一个编辑器用于编辑合并 commit 的 message
  - 可将一个 commit 分开，在`rebase script`中将需要分开的 commit 的`pick`改为`edit`，保存退出之后，`reset`这个 commit 再进行`add`，`commit`等一系列操作
  ```shell
  $ git reset HEAD^
  $ git add README
  $ git commit -m 'Update README formatting'
  $ git add lib/simplegit.rb
  $ git commit -m 'Add blame'
  $ git rebase --continue
  ```
  - `d | drop`用于删除一个 commit，但这样会使在之后的 commit 都被重写，可能会有很多冲突，使用`git rebase --abort`可以恢复到删除 commit 操作之前的状态
`filter-branch`相当于一个核弹选项，它可以改变你全局的 email 地址或者从所有 commit 中移除一个文件，所以最好在这个项目没有`public`之前使用它。

## Reset Demystified
Git 作为一个系统管理器，在它的平常操作中控制三个`tree`
`HEAD`: 上一个 commit 的快照，下一个 commit 的`parent`
`Index`: 推断过的下一个 commit 快照
`Working Diretory`: 沙箱`Sandbox`
### HEAD
`HEAD`可以简单地认为是在当前这个分支中最后一个 commit 的快照
`git cat-file -p HEAD`用于展示快照
`git ls-tree -r HEAD`可以展示文件夹以及 SHA-1

### The Index
指的就是`Staging Area`，也就是暂存区
`git ls-files -s`用于展示暂存区文件内容

### Working Directory
其他两棵树通过麻烦但高效的方式保存在`.git`文件夹中。`working directory`能够解压它们到实际的文件中让你更方便地编辑它们。它就是一个沙盒`sandbox`让你在将它们放置在`staging area`之前更改它们

### The Workflow
切换分支或是克隆`cloning`都会经过同样的流程。当你`checkout`一个 branch，它会更改 HEAD 指针到新分支，将 branch 的数据植入。复制`Index`的内容到`working directory`中
`git reset`会通过简单和可预见的方式操作这三棵树，它通过三个基本的操作
1. `Move HEAD`首先`reset`将会将`HEAD`移动到指定的指针上，当你移动到前面的`HEAD`时，它会返回到对应的位置但不会改变`Index`或是`Working Directory`
2. `Updating the Index(--mixed)`git 会使用`HEAD`指向的当前快照的内容来更新索引。它会撤销上一次提交，但还是会取消暂存所有东西
3. `Updating work directory(--hard)`会使 Git 真正地销毁数据，任何形式的`reset`调用都可以轻松撤销，但是`--hard`不能，它会真正地销毁数据。`working directory`中的内容会和`HEAD`内容一致

### `Checkout`
和`reset`一样，`checkout`也操作三棵树，不过它们也有不同，它会更新所有三棵树使其看起来更像`[branch]`，但有两点重要区别
- 不同于`reset --hard`，`checkout`对工作目录是安全的，它会通过检查确保不会将已更改的文件弄丢。它会简单的合并一下，更新所有未更改的文件
- `reset`会移动`HEAD`分支的指向，而`checkout`只会移动`HEAD`自身来指向一个分支

下面的图表列出了命令对树的影响
```
                           HEAD	Index	Workdir	WD Safe?
Commit Level

reset --soft [commit]       REF NO    NO      YES

reset [commit]              REF YES   NO      YES

reset --hard [commit]       REF YES   YES     NO

checkout <commit>           HEADYES   YES     YES

File Level

reset [commit] <paths>      NO  YES   NO      YES

checkout [commit] <paths>   NO  YES   YES     NO
```

## Advanced Merging
我们将介绍一些非标准类型的合并，以及如何回退到合并之前

### Merge Conflicts
对于一些复杂的合并冲突，Git 提供一些工具去帮助你即将会发生什么并且如何解决它
首先，请确保你的`working directory`在做一些可能引起合并冲突的操作前是干净的。如果存在正在进行的工作，请先`commit`或是`stash`它。
`git merge --abort`可将状态返回到未合并前的状态，但当使用命令前存在未`stage`或是`commit`的修改时不能完美处理
`$ git merge -Xignore-space-change whitespace`会忽视由于空格引起的冲突
如果我们想获得分支分叉前的共同祖先的分支版本，进行合并的分支1版本，进行合并的分支2版本，可以使用以下命令
```shell
$ git show :1:hello.rb > hello.common.rb
$ git show :2:hello.rb > hello.ours.rb
$ git show :3:hello.rb > hello.theirs.rb
```
`git diff --ours`可以看看我们分支合并引入了什么
`git diff --theirs`可以看看另一个分支合并引入了什么
`git diff --base`可以看看两边分支合并引入了什么
`git checkout`命令也可以使用`--ours`或是`--theirs`选项用于直接引用某一边的提交
### 组合式差异模式
`git log --merge`可以显示任何一边接触了冲突文件的 commit
因为 Git 暂存合并成功的结果，，当你在合并成功冲突状态下运行`git diff`时，只会得到现在还在冲突状态的区别
当对一个合并提交运行`git show`时也会显示冲突信息，或者使用`git log -cc -p`
### 撤销合并
当合并提交出错时，你可以进行修复它们，这取决于你要的结果是什么
如果这个错误的合并只存在于你的本地仓库中，最简单且方便的方法就是移动分支到你想要它指向的地方，如果使用的是`reset`，则在共享的仓库中并不好用，其他人的 commit 会覆盖你的修改
`git revert -m 1 HEAD`会生成一个新提交的选项，提交将会撤销一个已存在提交的所有修改，这个新提交有两个父节点，分别为合并前两次提交的最后一次提交。`-m 1`表示`manline`哪个父节点
如果还原之后再次合并，Git 只会引入被还原的合并之后的修改。所以需要撤销还原原始的合并，然后创建一个新合并提交
### 其他合并
目前为止我们都是通过一个叫`recursive`的合并策略来正常处理两个分支的正常合并，然后还有其他方式将两个分支合并在一起
`git merge --Xours | --Xtheirs`可以在冲突合并时简单地选择一边进行合并
`git merge -s ours <branchname>`可以假装合并过分支，但是合并后的结果会记录下来，这样在有一个分支需要分别在两条分支合并时非常有用，可以在一个分支上假装合并，这样就不会有冲突
### 子树合并
子树合并到思想是你有两个项目，并且其中一个映射到另一个项目的子目录，或者反过来也行。
### Rerere
`reuse recorded resolution`如名字所述，它允许你让 Git 记住解决一个块冲突的方法，当下一次遇到相同冲突时，Git 可以为你自动解决它
- `$ git config --global rerere.enabled true`启用功能
### 子模块
当你想要把它们当做两个独立的项目，同时又想在一个项目中使用另一个。
### 打包
可能你的网络中断了，你又希望将你的提交传输给你的同伴
`git bundle`会将`git push`命令传输的内容打包成一个二进制文件，你可以将它传输给其他人，然后解包
- `git bundle create <文件名> HEAD master`包含了所有重建该仓库`master`分支所需要的数据
- `git clone <文件名>`就像从一个 url 上克隆一样
如果你在打包时没有包含 HEAD 引用，你还需要在命令后指定一个`-b master`或是其他被引入的分支
可以指出需要打包的 commit 区间，可以使用`origin/master..master`或是`master ^origin/master`来获取提交之后进行打包
- `git bundle verify <文件路径>`可以验证是否是一个正常的可验证的包
- `git bundle list-heads <文件路径>`可以查看包中可以导入哪些分支
- `git fetch`或是`git pull`从包中导入提交`$ git fetch ../commits.bundle master:other-master`
### 凭证存储
如果你使用的是 SSH，可以不设置账号密码而使用没有口令的密钥来安全地传输数据，但对于 HTTP 这种方式是不可能的。
但 git 有一个凭证系统来处理这个事情，下面有一些 Git 的选项
- 默认所有都不缓存，每次链接都会询问账号和密码
- `cache`会储存在内存中一段时间，15 分钟后清除
- `store`会将凭证永久保存在磁盘中，永久有效
- `osxkeychain`模式，如果是使用 mac，它会以加密方式存放在磁盘中
通过 Git 的配置来选择上述的一种方式
- `$ git config --global credential.helper cache`

