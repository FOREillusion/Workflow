git命令大全


# 前言
最近在学习 Git 命令，发现网上的命令要么排版不清晰，要么不是很全。于是整理出了这篇 Git 命令大全！相信这份 Git 命令，一定值得你收藏。文章内容较多，可以在右侧目录直接找自己需要的部分查看。文章会持续更新，欢迎大家点赞，收藏！

这里还有一个偷懒的小技巧 hh，如果你不想记住那么多 Git 命令，你可以看看我的这篇文章：VScode 结合 git 的全面使用流程，再也不用记住 git 的命令了！

VsCode 已经封装好了 Git 命令，我们只要学会怎么使用就好了，是不是很方便呢！

如果你想学习 Linux 命令，我的另外一篇文章也能帮到你：Linux 命令大全（排版清晰！！持续更新！！非常详细）

## 1.Git 安装配置
在使用 Git 前我们需要先安装 Git。Git 目前支持 Linux/Unix、Solaris、Mac 和 Windows 平台上运行。

Git 各平台安装包下载地址为：http://git-scm.com/downloads

### 1.1Linux 平台上安装
Git 的工作需要调用 curl，zlib，openssl，expat，libiconv 等库的代码，所以需要先安装这些依赖工具。

在有 yum 的系统上（比如 Fedora）或者有 apt-get 的系统上（比如 Debian 体系），可以用下面的命令安装：

各 Linux 系统可以使用其安装包管理工具（apt-get、yum 等）进行安装：

Debian/Ubuntu
Debian/Ubuntu Git 安装命令为：

$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev

$ apt-get install git

$ git --version
git version 1.8.1.2
Centos/RedHat
如果你使用的系统是 Centos/RedHat 安装命令为：

$ yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel

$ yum -y install git-core

$ git --version
git version 1.7.1
源码安装
我们也可以在官网下载源码包来安装，最新源码包下载地址：https://git-scm.com/download

安装指定系统的依赖包：

########## Centos/RedHat ##########
$ yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel

########## Debian/Ubuntu ##########
$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev
解压安装下载的源码包：

$ tar -zxf git-1.7.2.2.tar.gz
$ cd git-1.7.2.2
$ make prefix=/usr/local all
$ sudo make prefix=/usr/local install
### 1.2 Windows 平台上安装
在 Windows 平台上安装 Git 同样轻松，有个叫做 msysGit 的项目提供了安装包，可以到 GitHub 的页面上下载 exe 安装文件并运行：

安装包下载地址：https://gitforwindows.org/



完成安装之后，就可以使用命令行的 git 工具（已经自带了 ssh 客户端）了，另外还有一个图形界面的 Git 项目管理工具。

在开始菜单里找到 "Git"->“Git Bash”，会弹出 Git 命令窗口，你可以在该窗口进行 Git 操作。

### 1.3Mac 平台上安装
在 Mac 平台上安装 Git 最容易的当属使用图形化的 Git 安装工具，下载地址为：

http://sourceforge.net/projects/git-osx-installer/

安装界面如下所示：



### 1.4 Git 配置
Git 提供了一个叫做 git config 的工具，专门用来配置或读取相应的工作环境变量。

这些环境变量，决定了 Git 在各个环节的具体工作方式和行为。这些变量可以存放在以下三个不同的地方：

/etc/gitconfig 文件：系统中对所有用户都普遍适用的配置。若使用 git config 时用 --system 选项，读写的就是这个文件。
~/.gitconfig 文件：用户目录下的配置文件只适用于该用户。若使用 git config 时用 --global 选项，读写的就是这个文件。
当前项目的 Git 目录中的配置文件（也就是工作目录中的 .git/config 文件）：这里的配置仅仅针对当前项目有效。每一个级别的配置都会覆盖上层的相同配置，所以 .git/config 里的配置会覆盖 /etc/gitconfig 中的同名变量。
在 Windows 系统上，Git 会找寻用户主目录下的 .gitconfig 文件。主目录即 $HOME 变量指定的目录，一般都是 C:\Documents and Settings$USER。

此外，Git 还会尝试找寻 /etc/gitconfig 文件，只不过看当初 Git 装在什么目录，就以此作为根目录来定位。

用户信息
配置个人的用户名称和电子邮件地址：

$ git config --global user.name "Viper"
$ git config --global user.email test@Viper.com
如果用了 –global 选项，那么更改的配置文件就是位于你用户主目录下的那个，以后你所有的项目都会默认使用这里配置的用户信息。

如果要在某个特定的项目中使用其他名字或者电邮，只要去掉 --global 选项重新配置即可，新的设定保存在当前项目的 .git/config 文件里。

文本编辑器
设置 Git 默认使用的文本编辑器, 一般可能会是 Vi 或者 Vim。如果你有其他偏好，比如 Emacs 的话，可以重新设置：:

$ git config --global core.editor emacs
差异分析工具
还有一个比较常用的是，在解决合并冲突时使用哪种差异分析工具。比如要改用 vimdiff 的话：

$ git config --global merge.tool vimdiff
Git 可以理解 kdiff3，tkdiff，meld，xxdiff，emerge，vimdiff，gvimdiff，ecmerge，和 opendiff 等合并工具的输出信息。

当然，你也可以指定使用自己开发的工具，具体怎么做可以参阅第七章。

查看配置信息
要检查已有的配置信息，可以使用 git config --list 命令：

$ git config --list
http.postbuffer=2M
user.name=Viper
user.email=test@Viper.com
有时候会看到重复的变量名，那就说明它们来自不同的配置文件（比如 /etc/gitconfig 和 ~/.gitconfig），不过最终 Git 实际采用的是最后一个。

这些配置我们也可以在 ~/.gitconfig 或 /etc/gitconfig 看到，如下所示：

vim ~/.gitconfig
显示内容如下所示：

[http]
    postBuffer = 2M
[user]
    name = Viper
    email = test@Viper.com
也可以直接查阅某个环境变量的设定，只要把特定的名字跟在后面即可，像这样：

$ git config user.name
Viper
## 2.Git 创建仓库
本章节我们将为大家介绍如何创建一个 Git 仓库。

你可以使用一个已经存在的目录作为 Git 仓库。

git init
Git 使用 git init 命令来初始化一个 Git 仓库，Git 的很多命令都需要在 Git 的仓库中运行，所以 git init 是使用 Git 的第一个命令。

在执行完成 git init 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变（不像 SVN 会在每个子目录生成 .svn 目录，Git 只在仓库的根目录生成 .git 目录）。

使用方法
使用当前目录作为 Git 仓库，我们只需使它初始化。

git init
该命令执行完后会在当前目录生成一个 .git 目录。

使用我们指定目录作为 Git 仓库。

git init newrepo
初始化后，会在 newrepo 目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

$ git add *.c
$ git add README
$ git commit -m '初始化项目版本'
以上命令将目录下以 .c 结尾及 README 文件提交到仓库中。

git clone
我们使用 git clone 从现有 Git 仓库中拷贝项目（类似 svn checkout）。

克隆仓库的命令格式为：

git clone <repo>
如果我们需要克隆到指定的目录，可以使用以下命令格式：

git clone <repo> <directory>
参数说明：

**repo:**Git 仓库。
**directory:** 本地目录。
比如，要克隆 Ruby 语言的 Git 代码仓库 Grit，可以用下面的命令：

$ git clone git://github.com/schacon/grit.git
执行该命令后，会在当前目录下创建一个名为 grit 的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录。

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：

$ git clone git://github.com/schacon/grit.git mygrit
## 3. Git 基本操作
Git 的工作就是创建和保存你项目的快照及与之后的快照进行对比。本章将对有关创建与提交你的项目快照的命令作介绍。

### 3.1 获取与创建项目命令
git init
用 git init 在目录中创建新的 Git 仓库。 你可以在任何时候、任何目录中这么做，完全是本地化的。

在目录中执行 git init，就可以创建一个 Git 仓库了。比如我们创建 Viper 项目：

$ mkdir Viper
$ cd Viper/
$ git init
Initialized empty Git repository in /Users/tianqixin/www/Viper/.git/
在 /www/Viper/.git/ 目录初始化空 Git 仓库完毕。
现在你可以看到在你的项目中生成了 .git 这个子目录。 这就是你的 Git 仓库了，所有有关你的此项目的快照数据都存放在这里。

ls -a
.    ..    .git
git clone
使用 git clone 拷贝一个 Git 仓库到本地，让自己能够查看该项目，或者进行修改。

如果你需要与他人合作一个项目，或者想要复制一个项目，看看代码，你就可以克隆那个项目。 执行命令：

git clone [url]
[url] 为你想要复制的项目，就可以了。

例如我们克隆 Github 上的项目：

$ git clone git@github.com:schacon/simplegit.git
Cloning into 'simplegit'...
remote: Counting objects: 13, done.
remote: Total 13 (delta 0), reused 0 (delta 0), pack-reused 13
Receiving objects: 100% (13/13), done.
Resolving deltas: 100% (2/2), done.
Checking connectivity... done.
克隆完成后，在当前目录下会生成一个 simplegit 目录：

$ cd simplegit/
$ ls
README   Rakefile lib
上述操作将复制该项目的全部记录。

$ ls -a
.        ..       .git     README   Rakefile lib
$ cd .git
$ ls
HEAD        description info        packed-refs
branches    hooks       logs        refs
config      index       objects
默认情况下，Git 会按照你提供的 URL 所指示的项目的名称创建你的本地项目目录。 通常就是该 URL 最后一个 / 之后的项目名称。如果你想要一个不一样的名字， 你可以在该命令后加上你想要的名称。

### 3.2 基本快照
Git 的工作就是创建和保存你的项目的快照及与之后的快照进行对比。本章将对有关创建与提交你的项目的快照的命令作介绍。

git add
git add 命令可将该文件添加到缓存，如我们添加以下两个文件：

$ touch README
$ touch hello.php
$ ls
README        hello.php
$ git status -s
?? README
?? hello.php
$
git status 命令用于查看项目的当前状态。

接下来我们执行 git add 命令来添加文件：

$ git add README hello.php
现在我们再执行 git status，就可以看到这两个文件已经加上去了。

$ git status -s
A  README
A  hello.php
$
新项目中，添加所有文件很普遍，我们可以使用 git add . 命令来添加当前项目的所有文件。

现在我们修改 README 文件：

$ vim README
在 README 添加以下内容：# Viper Git 测试，然后保存退出。

再执行一下 git status：

$ git status -s
AM README
A  hello.php
“AM” 状态的意思是，这个文件在我们将它添加到缓存之后又有改动。改动后我们再执行 git add 命令将其添加到缓存中：

$ git add .
$ git status -s
A  README
A  hello.php
当你要将你的修改包含在即将提交的快照里的时候，需要执行 git add。

git status
git status 以查看在你上次提交之后是否有修改。

我演示该命令的时候加了 -s 参数，以获得简短的结果输出。如果没加该参数会详细输出内容：

$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

    new file:   README
    new file:   hello.php
git diff
执行 git diff 来查看执行 git status 的结果的详细信息。

git diff 命令显示已写入缓存与已修改但尚未写入缓存的改动的区别。git diff 有两个主要的应用场景。

尚未缓存的改动：git diff
查看已缓存的改动： git diff --cached
查看已缓存的与未缓存的所有改动：git diff HEAD
显示摘要而非整个 diff：git diff --stat
在 hello.php 文件中输入以下内容：

<?php
echo '菜鸟教程：www.Runoob.com';
?>
$ git status -s
A  README
AM hello.php
$ git diff
diff --git a/hello.php b/hello.php
index e69de29..69b5711 100644
--- a/hello.php
+++ b/hello.php
@@ -0,0 +1,3 @@
+<?php
+echo '菜鸟教程：www.Runoob.com';
+?>
git status 显示你上次提交更新后的更改或者写入缓存的改动， 而 git diff 一行一行地显示这些改动具体是啥。

接下来我们来查看下 git diff --cached 的执行效果：

$ git add hello.php 
$ git status -s
A  README
A  hello.php
$ git diff --cached
diff --git a/README b/README
new file mode 100644
index 0000000..8f87495
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+# Viper Git 测试
diff --git a/hello.php b/hello.php
new file mode 100644
index 0000000..69b5711
--- /dev/null
+++ b/hello.php
@@ -0,0 +1,3 @@
+<?php
+echo '菜鸟教程：www.Runoob.com';
+?>
git commit
使用 git add 命令将想要快照的内容写入缓存区， 而执行 git commit 将缓存区内容添加到仓库中。

Git 为你的每一个提交都记录你的名字与电子邮箱地址，所以第一步需要配置用户名和邮箱地址。

$ git config --global user.name 'Viper'
$ git config --global user.email test@Viper.com
接下来我们写入缓存，并提交对 hello.php 的所有改动。在首个例子中，我们使用 -m 选项以在命令行中提供提交注释。

$ git add hello.php
$ git status -s
A  README
A  hello.php
$ git commit -m '第一次版本提交'
[master (root-commit) d32cf1f] 第一次版本提交
 2 files changed, 4 insertions(+)
 create mode 100644 README
 create mode 100644 hello.php
现在我们已经记录了快照。如果我们再执行 git status:

$ git status
# On branch master
nothing to commit (working directory clean)
以上输出说明我们在最近一次提交之后，没有做任何改动，是一个 "working directory clean：干净的工作目录"。

如果你没有设置 -m 选项，Git 会尝试为你打开一个编辑器以填写提交信息。 如果 Git 在你对它的配置中找不到相关信息，默认会打开 vim。屏幕会像这样：

```  
  # Please enter the commit message for your changes. Lines starting
  # with '#' will be ignored, and an empty message aborts the commit.
  # On branch master
  # Changes to be committed:
  #   (use "git reset HEAD <file>..." to unstage)
  #
  # modified:   hello.php
  #
```
~
".git/COMMIT_EDITMSG" 9L, 257C
如果你觉得 git add 提交缓存的流程太过繁琐，Git 也允许你用 -a 选项跳过这一步。命令格式如下：

git commit -a
我们先修改 hello.php 文件为以下内容：

<?php
echo '菜鸟教程：www.Runoob.com';
echo '菜鸟教程：www.Runoob.com';
?>
再执行以下命令：

git commit -am '修改 hello.php 文件'
[master 71ee2cb] 修改 hello.php 文件
 1 file changed, 1 insertion(+)
git reset HEAD
git reset HEAD 命令用于取消已缓存的内容。

我们先改动文件 README 文件，内容如下：

# Viper Git 测试
# 菜鸟教程
hello.php 文件修改为：

<?php
echo '菜鸟教程：www.Runoob.com';
echo '菜鸟教程：www.Runoob.com';
echo '菜鸟教程：www.Runoob.com';
?>
现在两个文件修改后，都提交到了缓存区，我们现在要取消其中一个的缓存，操作如下：

$ git status -s
 M README
 M hello.php
$ git add .
$ git status -s
M  README
M  hello.php
$ git reset HEAD hello.php 
Unstaged changes after reset:
M    hello.php
$ git status -s
M  README
 M hello.php
现在你执行 git commit，只会将 README 文件的改动提交，而 hello.php 是没有的。

$ git commit -m '修改'
[master f50cfda] 修改
 1 file changed, 1 insertion(+)
$ git status -s
 M hello.php
可以看到 hello.php 文件的修改并未提交。

这时我们可以使用以下命令将 hello.php 的修改提交：

$ git commit -am '修改 hello.php 文件'
[master 760f74d] 修改 hello.php 文件
 1 file changed, 1 insertion(+)
$ git status
On branch master
nothing to commit, working directory clean
简而言之，执行 git reset HEAD 以取消之前 git add 添加，但不希望包含在下一提交快照中的缓存。

git rm
如果只是简单地从工作目录中手工删除文件，运行 git status 时就会在 Changes not staged for commit 的提示。

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作

git rm <file>
如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f

git rm -f <file>
如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 –cached 选项即可

git rm --cached <file>
如我们删除 hello.php 文件：

$ git rm hello.php 
rm 'hello.php'
$ ls
README
不从工作区中删除文件：

$ git rm --cached README 
rm 'README'
$ ls
README
可以递归删除，即如果后面跟的是一个目录做为参数，则会递归删除整个目录中的所有子目录和文件：

git rm –r *
进入某个目录中，执行此语句，会删除该目录下的所有文件和子目录。

git mv
git mv 命令用于移动或重命名一个文件、目录、软连接。

我们先把刚移除的 README 添加回来：

$ git add README
然后对其重名:

$ git mv README  README.md
$ ls
README.md
4. 再按照作用重新整理一下命令
（这部分命令和上面的有重复，排版的方式不一样）

一、Git 配置相关
如果你首次使用 Git，那刚开始首先是需要配置各种身份信息的，这样当你提交相关任务的时候，别人才能知道这个 commit 是谁提交的。

Git 最小配置
1、配置全局账户，也就是该账户对所有的 Git 仓库都有效

git config --global user.name '你的账户名称'
git config --global user.email '你的 Email'
2、配置局部账户，也就是该账户只对当前 Git 仓库有效

git config --local user.name '你的账户名称' 
git config --local user.email '你的 Email'
注意，不同点就是一个参数是 global（全局），一个是 local(本地)

3、查看相关配置情况

配置了之后，显然有时候是需要查看我们当前配置的相关情况的，可以使用如下命令

1、查看 global 类型的配置情况

git config --global --list
2、查看某个仓库下的配置情况

git config --local --list
二、本地基本操作
1、基本操作
1、查看变更情况

git status
2、查看当前工作在哪个分支上

git branch -v
3、切换到指定分支

git checkout 指定分支的名称
4、把当前目录及其子目录下所有变更都加入到暂存区

git add . // 注意，add 后面是一个 '.'；
5、把仓库内所有变更都假如到暂存区

git add -A
6、把指定文件添加到暂存区

git add 文件1 文件2 ... 文件n
7、创建正式的 commit，也就是把当前的数据提交上去

git commit
2、比较差异
1、比较某文件工作区和暂存区的差异

git diff 某文件
2、比较某文件暂存区和 HEAD 的差异

git diff --cache 某文件
3、比较工作区和暂存区的所有差异

git diff
4、比较暂存区和 HEAD 的所有差异

git diff --cache
3、暂存区与工作区之间回滚
1、把工作区指定文件恢复成和暂存区一样

git checkout 文件1 文件2 ... 文件n
2、把暂存区指定文件回复和 HEAD 一样

git reset 文件1 文件2 ... 文件n
3、把暂存区和工作区所有文件恢复成和 HEAD 一样

git reset --hard
4、用 difftool 比较任意两个 commit 的差异

git difftool commit1 commit2
注意，从工作区回滚到暂存区则用 checkout ，否则用 reset

4、其他
查看哪些文件没有被 Git 管控

git ls-files --others
三、加塞临时任务处理
1、把未处理完的变更先保存到 stash 中

git stash
2、临时任务处理完后继续之前的工作

git stash pop // pop 相当于栈的出栈和入栈一样，把之前的任务弹出来
或者
git stash apply // 和 pop 不同的是， apply 相当于从栈顶把任务取出来，但是不过从栈中把任务移除
3、查看所有的 stash

git stash list
4、取回某次 stash 的变更

git stash pop stash @{数字n}
四、修改个人分支历史
我们的仓库的内容每次变更执行 commit 的时候，都会生成一个新的 commit，不过有时候，我们不想产生新的 commit，而是想要通过修改之前的 commit 来变更仓库的内容，那么就可以使用如下命令了

1、修改最后一次 commit

1、在工作区中修改文件
2、git add
3、git commit --amend
2|、修改中间的 commit(假设代号为 X)

1. git rebase -i X前面的一个 commit 的 id
2. 在工作区修改文件
3. git add
4. git rebase --contiue
五、查看变更日志等
1、当前分支各个 commit 用一行显示

git log --online
2、显示最近的 n 个 commit

git log -n
3、用图示显示所有的分支历史

git log --online --graph --all
4、查看涉及到某文件变更的所有 commit

git log 某文件
5、某文件各行最后修改对应的 commit 以及作者

git blame 某文件
六、分支与标签
1、创建新分支
基于当前分支创建新分支

git branch 新分支
基于指定分支创建新分支

git branch 新分支 已有分支
基于某个 commit 创建分支

git branch 新分支 某个 commit 的id
创建分支并且切换到该分支

git chechout -b 新分支
2、列出分支
列出本地分支

git branch -v
列出本地和远端分支

git branch -av
列出远端所有分支

git branch -rv
列出名称符号某样式的远端分支

git branch -rv -l '某样式'
3、删除分支
安全删除本地某分支

git branch -d 要删除的分支
强行删除本地分支

git branch -D 要删除的分支
删除已合并到 master 分支的所有本地分支

git branch --merged master | grep -v '^\*\| master' | xargs -n 1 git branch -d
删除远端 origin 已不存在的所有本地分支

git remote prune origin
4、打标签
从 commit 打上标签

git tag 标签名 commit 的id
七、两分支之间的集成
1、把 A 分支合入到当前分支，且为 merge 创建 commit

git merge A分支
2、把 A 分支合入到 B 分支，且为 Merge 创建 commit

git merge A分支 B分支
3、把当前分支基于 B 分支做 rebase，以便把 B 分支合入到当前分支

git rebase B分支
4、把 A 分支基于 B 分支做 rebase，以便把 B 分支合入到 A 分支

git rebase B分支 A分支
5、用 mergetool 解决冲突

git mergetool
八、和远端交互
1、列出所有 remote

git remote -v
2、增加 remote

git remote add url地址
3、删除 remote

git remote remove remote的名称
4、改变 remote 的 name

git remote rename 旧名称 新名称
5、把远端所有分支和标签的变更都拉到本地

git fetch remote
6、把远端分支的变更拉倒本地，且 merge 到本地分支

git pull remote名称 分支名
7、把本地分支 push 到远端

git push remote名称 分支名
8、删除远端分支

git push remote --delete 远端分支名
或者
git push remote:远端分支名
9、向远端提交指定标签

git push remote 标签名
10、向远端提交所有标签

git push remote --tags
九、常用回滚操作系统
未执行 commit 之前
1、如果文件修改了，但是还没有添加到暂存区，也就是还没有执行 git add 操作，则可以使用 checkout 来回滚

git chechout -- filename
2、如果已经添加到暂存区，则可以用 reset 来撤销

git reset HEAD filename
执行了 commit 之后
1、使用 revert 来撤销某次提交

git revert commitID
// 注：git revert 命令是撤销某次操作，而在此次操作之前和之后的提交记录都会保留
2、使用 reset 直接回滚到某个版本

git  reset --hard  commit_id
// --hard – 强制将缓存区和工作目录都同步到你指定的提交
这个操作不可逆，会把 commit_id 之后的全部回滚

5. Git 分支管理
几乎每一种版本控制系统都以某种形式支持分支。使用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作。

有人把 Git 的分支模型称为必杀技特性，而正是因为它，将 Git 从版本控制系统家族里区分出来。

创建分支命令：

git branch (branchname)
切换分支命令:

git checkout (branchname)
当你切换分支的时候，Git 会用该分支的最后提交的快照替换你的工作目录的内容， 所以多个分支不需要多个目录。

合并分支命令:

git merge
你可以多次合并到统一分支， 也可以选择在合并之后直接删除被并入的分支。

开始前我们先创建一个测试目录：

$ mkdir gitdemo
$ cd gitdemo/
$ git init
Initialized empty Git repository...
$ touch README
$ git add README
$ git commit -m '第一次版本提交'
[master (root-commit) 3b58100] 第一次版本提交
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README
5.1 列出分支
列出分支基本命令：

git branch
没有参数时，git branch 会列出你在本地的分支。

$ git branch
* master
此例的意思就是，我们有一个叫做 master 的分支，并且该分支是当前分支。

当你执行 git init 的时候，默认情况下 Git 就会为你创建 master 分支。

如果我们要手动创建一个分支。执行 git branch (branchname) 即可。

$ git branch testing
$ git branch
* master
  testing
现在我们可以看到，有了一个新分支 testing。

当你以此方式在上次提交更新之后创建了新分支，如果后来又有更新提交， 然后又切换到了 testing 分支，Git 将还原你的工作目录到你创建分支时候的样子。

接下来我们将演示如何切换分支，我们用 git checkout (branch) 切换到我们要修改的分支。

$ ls
README
$ echo 'Viper.com' > test.txt
$ git add .
$ git commit -m 'add test.txt'
[master 3e92c19] add test.txt
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt
$ ls
README        test.txt
$ git checkout testing
Switched to branch 'testing'
$ ls
README
当我们切换到 testing 分支的时候，我们添加的新文件 test.txt 被移除了。切换回 master 分支的时候，它们有重新出现了。

$ git checkout master
Switched to branch 'master'
$ ls
README        test.txt
我们也可以使用 git checkout -b (branchname) 命令来创建新分支并立即切换到该分支下，从而在该分支中操作。

$ git checkout -b newtest
Switched to a new branch 'newtest'
$ git rm test.txt 
rm 'test.txt'
$ ls
README
$ touch Viper.php
$ git add .
$ git commit -am 'removed test.txt、add Viper.php'
[newtest c1501a2] removed test.txt、add Viper.php
 2 files changed, 1 deletion(-)
 create mode 100644 Viper.php
 delete mode 100644 test.txt
$ ls
README        Viper.php
$ git checkout master
Switched to branch 'master'
$ ls
README        test.txt
如你所见，我们创建了一个分支，在该分支的上移除了一些文件 test.txt，并添加了 Viper.php 文件，然后切换回我们的主分支，删除的 test.txt 文件又回来了，且新增加的 Viper.php 不存在主分支中。

使用分支将工作切分开来，从而让我们能够在不同开发环境中做事，并来回切换。

5.2 删除分支
删除分支命令：

git branch -d (branchname)
例如我们要删除 testing 分支：

$ git branch
* master
  testing
$ git branch -d testing
Deleted branch testing (was 85fc7e7).
$ git branch
* master
5.3 分支合并
一旦某分支有了独立内容，你终究会希望将它合并回到你的主分支。 你可以使用以下命令将任何分支合并到当前分支中去：

git merge
$ git branch
* master
  newtest
$ ls
README        test.txt
$ git merge newtest
Updating 3e92c19..c1501a2
Fast-forward
 Viper.php | 0
 test.txt   | 1 -
 2 files changed, 1 deletion(-)
 create mode 100644 Viper.php
 delete mode 100644 test.txt
$ ls
README        Viper.php
以上实例中我们将 newtest 分支合并到主分支去，test.txt 文件被删除。

合并完后就可以删除分支:

$ git branch -d newtest
Deleted branch newtest (was c1501a2).
删除后， 就只剩下 master 分支了：

$ git branch
* master
5.4 合并冲突
合并并不仅仅是简单的文件添加、移除的操作，Git 也会合并修改。

$ git branch
* master
$ cat Viper.php
首先，我们创建一个叫做 change_site 的分支，切换过去，我们将 Viper.php 内容改为:

<?php
echo 'Viper';
?>
创建 change_site 分支：

$ git checkout -b change_site
Switched to a new branch 'change_site'
$ vim Viper.php
$ head -3 Viper.php
<?php
echo 'Viper';
?>
$ git commit -am 'changed the Viper.php'
[change_site 7774248] changed the Viper.php
 1 file changed, 3 insertions(+)
将修改的内容提交到 change_site 分支中。 现在，假如切换回 master 分支我们可以看内容恢复到我们修改前的 (空文件，没有代码)，我们再次修改 Viper.php 文件。

$ git checkout master
Switched to branch 'master'
$ cat Viper.php
$ vim Viper.php    # 修改内容如下
$ cat Viper.php
<?php
echo 1;
?>
$ git diff
diff --git a/Viper.php b/Viper.php
index e69de29..ac60739 100644
--- a/Viper.php
+++ b/Viper.php
@@ -0,0 +1,3 @@
+<?php
+echo 1;
+?>
$ git commit -am '修改代码'
[master c68142b] 修改代码
 1 file changed, 3 insertions(+)
现在这些改变已经记录到我的 “master” 分支了。接下来我们将 “change_site” 分支合并过来。

$ git merge change_site
Auto-merging Viper.php
CONFLICT (content): Merge conflict in Viper.php
Automatic merge failed; fix conflicts and then commit the result.

$ cat Viper.php     # 代开文件，看到冲突内容
<?php
<<<<<<< HEAD
echo 1;
=======
echo 'Viper';
>>>>>>> change_site
?>
我们将前一个分支合并到 master 分支，一个合并冲突就出现了，接下来我们需要手动去修改它。

$ vim Viper.php 
$ cat Viper.php
<?php
echo 1;
echo 'Viper';
?>
$ git diff
diff --cc Viper.php
index ac60739,b63d7d7..0000000
--- a/Viper.php
+++ b/Viper.php
@@@ -1,3 -1,3 +1,4 @@@
  <?php
 +echo 1;
+ echo 'Viper';
  ?>
在 Git 中，我们可以用 git add 要告诉 Git 文件冲突已经解决

$ git status -s
UU Viper.php
$ git add Viper.php
$ git status -s
M  Viper.php
$ git commit
[master 88afe0e] Merge branch 'change_site'
现在我们成功解决了合并中的冲突，并提交了结果。

6. Git 查看提交历史
在使用 Git 提交了若干更新之后，又或者克隆了某个项目，想回顾下提交历史，我们可以使用 git log 命令查看。

针对我们前一章节的操作，使用 git log 命令列出历史提交记录如下：

$ git log
commit d5e9fc2c811e0ca2b2d28506ef7dc14171a207d9 (HEAD -> master)
Merge: c68142b 7774248
Author: Viper <test@Viper.com>
Date:   Fri May 3 15:55:58 2019 +0800

    Merge branch 'change_site'

commit c68142b562c260c3071754623b08e2657b4c6d5b
Author: Viper <test@Viper.com>
Date:   Fri May 3 15:52:12 2019 +0800

    修改代码

commit 777424832e714cf65d3be79b50a4717aea51ab69 (change_site)
Author: Viper <test@Viper.com>
Date:   Fri May 3 15:49:26 2019 +0800

    changed the Viper.php

commit c1501a244676ff55e7cccac1ecac0e18cbf6cb00
Author: Viper <test@Viper.com>
Date:   Fri May 3 15:35:32 2019 +0800
我们可以用 --oneline 选项来查看历史记录的简洁的版本。

$ git log --oneline
$ git log --oneline
d5e9fc2 (HEAD -> master) Merge branch 'change_site'
c68142b 修改代码
7774248 (change_site) changed the Viper.php
c1501a2 removed test.txt、add Viper.php
3e92c19 add test.txt
3b58100 第一次版本提交
这告诉我们的是，此项目的开发历史。

我们还可以用 --graph 选项，查看历史中什么时候出现了分支、合并。以下为相同的命令，开启了拓扑图选项：

*   d5e9fc2 (HEAD -> master) Merge branch 'change_site'
|\  
| * 7774248 (change_site) changed the Viper.php
* | c68142b 修改代码
|/  
* c1501a2 removed test.txt、add Viper.php
* 3e92c19 add test.txt
* 3b58100 第一次版本提交
现在我们可以更清楚明了地看到何时工作分叉、又何时归并。

你也可以用 –reverse 参数来逆向显示所有日志。

$ git log --reverse --oneline
3b58100 第一次版本提交
3e92c19 add test.txt
c1501a2 removed test.txt、add Viper.php
7774248 (change_site) changed the Viper.php
c68142b 修改代码
d5e9fc2 (HEAD -> master) Merge branch 'change_site'
如果只想查找指定用户的提交日志可以使用命令：git log --author , 例如，比方说我们要找 Git 源码中 Linus 提交的部分：

$ git log --author=Linus --oneline -5
81b50f3 Move 'builtin-*' into a 'builtin/' subdirectory
3bb7256 make "index-pack" a built-in
377d027 make "git pack-redundant" a built-in
b532581 make "git unpack-file" a built-in
112dd51 make "mktag" a built-in
如果你要指定日期，可以执行几个选项：–since 和 --before，但是你也可以用 --until 和 --after。

例如，如果我要看 Git 项目中三周前且在四月十八日之后的所有提交，我可以执行这个（我还用了 --no-merges 选项以隐藏合并提交）：

$ git log --oneline --before={3.weeks.ago} --after={2010-04-18} --no-merges
5469e2d Git 1.7.1-rc2
d43427d Documentation/remote-helpers: Fix typos and improve language
272a36b Fixup: Second argument may be any arbitrary string
b6c8d2d Documentation/remote-helpers: Add invocation section
5ce4f4e Documentation/urls: Rewrite to accomodate transport::address
00b84e9 Documentation/remote-helpers: Rewrite description
03aa87e Documentation: Describe other situations where -z affects git diff
77bc694 rebase-interactive: silence warning when no commits rewritten
636db2c t3301: add tests to use --format="%N"
7. Git 标签
如果你达到一个重要的阶段，并希望永远记住那个特别的提交快照，你可以使用 git tag 给它打上标签。

比如说，我们想为我们的 Viper 项目发布一个 "1.0" 版本。 我们可以用 git tag -a v1.0 命令给最新一次提交打上（HEAD）"v1.0" 的标签。

-a 选项意为 "创建一个带注解的标签"。 不用 -a 选项也可以执行的，但它不会记录这标签是啥时候打的，谁打的，也不会让你添加个标签的注解。 我推荐一直创建带注解的标签。

$ git tag -a v1.0
当你执行 git tag -a 命令时，Git 会打开你的编辑器，让你写一句标签注解，就像你给提交写注解一样。

现在，注意当我们执行 git log --decorate 时，我们可以看到我们的标签了：

*   d5e9fc2 (HEAD -> master) Merge branch 'change_site'
|\  
| * 7774248 (change_site) changed the Viper.php
* | c68142b 修改代码
|/  
* c1501a2 removed test.txt、add Viper.php
* 3e92c19 add test.txt
* 3b58100 第一次版本提交
如果我们忘了给某个提交打标签，又将它发布了，我们可以给它追加标签。

例如，假设我们发布了提交 85fc7e7(上面实例最后一行)，但是那时候忘了给它打标签。 我们现在也可以：

$ git tag -a v0.9 85fc7e7
$ git log --oneline --decorate --graph
*   d5e9fc2 (HEAD -> master) Merge branch 'change_site'
|\  
| * 7774248 (change_site) changed the Viper.php
* | c68142b 修改代码
|/  
* c1501a2 removed test.txt、add Viper.php
* 3e92c19 add test.txt
* 3b58100 (tag: v0.9) 第一次版本提交
如果我们要查看所有标签可以使用以下命令：

$ git tag
v0.9
v1.0
指定标签信息命令：

git tag -a <tagname> -m "Viper.com标签"
PGP 签名标签命令：

git tag -s <tagname> -m "Viper.com标签"
标签介绍
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。

所以，标签也是版本库的一个快照。

Git 的标签虽然是版本库的快照，但其实它就是指向某个 commit 的指针（跟分支很像对不对？但是分支可以移动，标签不能移动），所以，创建和删除标签都是瞬间完成的。

Git 有 commit，为什么还要引入 tag？

“请把上周一的那个版本打包发布，commit 号是 6a5819e…”

“一串乱七八糟的数字不好找！”

如果换一个办法：

“请把上周一的那个版本打包发布，版本号是 v1.2”

“好的，按照 tag v1.2 查找 commit 就行！”

所以，tag 就是一个让人容易记住的有意义的名字，它跟某个 commit 绑在一起。

同大多数 VCS 一样，Git 也可以对某一时间点上的版本打上标签。人们在发布某个软件版本（比如 v1.0 等等）的时候，经常这么做。

本节我们一起来学习如何列出所有可用的标签，如何新建标签，以及各种不同类型标签之间的差别。

整合步骤
1 新建标签
Git 使用的标签有两种类型：轻量级的（lightweight）和含附注的（annotated）。

轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。

而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。

一般我们都建议使用含附注型的标签，以便保留相关信息；

当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题。

2 创建标签
[root@Git git]# git tag v1.0
3 查看已有标签
[root@Git git]# git tag
v1.0
[root@Git git]# git tag v1.1
[root@Git git]# git tag
v1.0
v1.1
4 删除标签
[root@Git git]# git tag -d v1.1
Deleted tag ‘v1.1’ (was 91388f0)
[root@Git git]# git tag
v1.0
5 查看此版本所修改的内容
[root@Git git]# git show v1.0
commit 91388f0883903ac9014e006611944f6688170ef4
Author: "syaving" <"819044347@qq.com">
Date: Fri Dec 16 02:32:05 2016 +0800
commit dir
diff –git a/readme b/readme
index 7a3d711..bfecb47 100644
— a/readme
+++ b/readme
@@ -1,2 +1,3 @@
text
hello git
+use commit
[root@Git git]# git log –oneline
91388f0 commit dir
e435fe8 add readme
2525062 add readme
8. Git 远程仓库 (Github)
Git 并不像 SVN 那样有个中心服务器。

目前我们使用到的 Git 命令都是在本地执行，如果你想通过 Git 分享你的代码或者与其他开发人员合作。 你就需要将数据放到一台其他开发人员能够连接的服务器上。

8.1 添加远程库
要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用, 命令格式如下：

git remote add [shortname] [url]
本例以 Github 为例作为远程仓库，如果你没有 Github 可以在官网 https://github.com / 注册。

由于你的本地 Git 仓库和 GitHub 仓库之间的传输是通过 SSH 加密的，所以我们需要配置验证信息：

使用以下命令生成 SSH Key：

$ ssh-keygen -t rsa -C "youremail@example.com"
后面的 your_email@youremail.com 改为你在 Github 上注册的邮箱，之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在 ~/ 下生成 .ssh 文件夹，进去，打开 id_rsa.pub，复制里面的 key。

回到 github 上，进入 Account => Settings（账户配置）。



左边选择 SSH and GPG keys，然后点击 New SSH key 按钮, title 设置标题，可以随便填，粘贴在你电脑上生成的 key。



添加成功后界面如下所示



为了验证是否成功，输入以下命令：

$ ssh -T git@github.com
Hi tianqixin! You've successfully authenticated, but GitHub does not provide shell access.
以下命令说明我们已成功连上 Github。

之后登录后点击 "New repository" 如下图所示：



之后在在 Repository name 填入 Viper-git-test(远程仓库名) ，其他保持默认设置，点击 "Create repository" 按钮，就成功地创建了一个新的 Git 仓库：



创建成功后，显示如下信息：



以上信息告诉我们可以从这个仓库克隆出新的仓库，也可以把本地仓库的内容推送到 GitHub 仓库。

现在，我们根据 GitHub 的提示，在本地的仓库下运行命令：

$ mkdir Viper-git-test                     # 创建测试目录
$ cd Viper-git-test/                       # 进入测试目录
$ echo "# 菜鸟教程 Git 测试" >> README.md     # 创建 README.md 文件并写入内容
$ ls                                        # 查看目录下的文件
README
$ git init                                  # 初始化
$ git add README.md                         # 添加文件
$ git commit -m "添加 README.md 文件"        # 提交并备注信息
[master (root-commit) 0205aab] 添加 README.md 文件
 1 file changed, 1 insertion(+)
 create mode 100644 README.md

# 提交到 Github
$ git remote add origin git@github.com:tianqixin/Viper-git-test.git
$ git push -u origin master
以下命令请根据你在 Github 成功创建新仓库的地方复制，而不是根据我提供的命令，因为我们的 Github 用户名不一样，仓库名也不一样。

接下来我们返回 Github 创建的仓库，就可以看到文件已上传到 Github 上：



8.2 查看当前的远程库
要查看当前配置有哪些远程仓库，可以用命令：

git remote
实例
$ git remote
origin
$ git remote -v
origin    git@github.com:tianqixin/Viper-git-test.git (fetch)
origin    git@github.com:tianqixin/Viper-git-test.git (push)
执行时加上 -v 参数，你还可以看到每个别名的实际链接地址。

8.3 提取远程仓库
Git 有两个命令用来提取远程仓库的更新。

1、从远程仓库下载新分支与数据：

git fetch
该命令执行完后需要执行 git merge 远程分支到你所在的分支。

2、从远端仓库提取数据并尝试合并到当前分支：

git merge
该命令就是在执行 git fetch 之后紧接着执行 git merge 远程分支到你所在的任意分支。

假设你配置好了一个远程仓库，并且你想要提取更新的数据，你可以首先执行 git fetch [alias] 告诉 Git 去获取它有你没有的数据，然后你可以执行 git merge [alias]/[branch] 以将服务器上的任何更新（假设有人这时候推送到服务器了）合并到你的当前分支。

接下来我们在 Github 上点击 "README.md" 并在线修改它:



然后我们在本地更新修改。

$ git fetch origin
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From github.com:tianqixin/Viper-git-test
   0205aab..febd8ed  master     -> origin/master
以上信息 "0205aab…febd8ed master -> origin/master" 说明 master 分支已被更新，我们可以使用以下命令将更新同步到本地：

$ git merge origin/master
Updating 0205aab..febd8ed
Fast-forward
 README.md | 1 +
 1 file changed, 1 insertion(+)
查看 README.md 文件内容：

$ cat README.md 
# 菜鸟教程 Git 测试
## 第一次修改内容
8.4 推送到远程仓库
推送你的新分支与数据到某个远端仓库命令:

git push [alias] [branch]
以上命令将你的 [branch] 分支推送成为 [alias] 远程仓库上的 [branch] 分支，实例如下。

$ touch Viper-test.txt      # 添加文件
$ git add Viper-test.txt 
$ git commit -m "添加到远程"
master 69e702d] 添加到远程
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 Viper-test.txt

$ git push origin master    # 推送到 Github
重新回到我们的 Github 仓库，可以看到文件已经提交上来了：



8.5 删除远程仓库
删除远程仓库你可以使用命令：

git remote rm [别名]
实例
$ git remote -v
origin    git@github.com:tianqixin/Viper-git-test.git (fetch)
origin    git@github.com:tianqixin/Viper-git-test.git (push)

# 添加仓库 origin2
$ git remote add origin2 git@github.com:tianqixin/Viper-git-test.git

$ git remote -v
origin    git@github.com:tianqixin/Viper-git-test.git (fetch)
origin    git@github.com:tianqixin/Viper-git-test.git (push)
origin2    git@github.com:tianqixin/Viper-git-test.git (fetch)
origin2    git@github.com:tianqixin/Viper-git-test.git (push)

# 删除仓库 origin2
$ git remote rm origin2
$ git remote -v
origin    git@github.com:tianqixin/Viper-git-test.git (fetch)
origin    git@github.com:tianqixin/Viper-git-test.git (push)
执行 git fetch origin master 时，它的意思是从名为 origin 的远程上拉取名为 master 的分支到本地分支 origin/master 中。既然是拉取代码，当然需要同时指定远程名与分支名，所以分开写。
执行 git merge origin/master 时，它的意思是合并名为 origin/master 的分支到当前所在分支。既然是分支的合并，当然就与远程名没有直接的关系，所以没有出现远程名。需要指定的是被合并的分支。
执行 git push origin master 时，它的意思是推送本地的 master 分支到远程 origin，涉及到远程以及分支，当然也得分开写了。
还可以一次性拉取多个分支的代码：git fetch origin master stable oldstable；
也还可以一次性合并多个分支的代码：git merge origin/master hotfix-2275 hotfix-2276 hotfix-2290；
常见问题

ssh 访问 gitHub 出错如下：

$ ssh -T git@github.com
The authenticity of host 'github.com (140.82.118.4)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? 
Host key verification failed.
解决办法：（将 GitHub 添加到信任主机列表后，可以成功访问）

$ ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
# github.com:22 SSH-2.0-babeld-d45c1532

$ ssh -T git@github.com
Warning: Permanently added the RSA host key for IP address '140.82.118.4' to the list of known hosts.
Hi earthnorth! You've successfully authenticated, but GitHub does not provide shell access.
9. Git Gitee
大家都知道国内访问 Github 速度比较慢，很影响我们的使用。

如果你希望体验到 Git 飞一般的速度，可以使用国内的 Git 托管服务——Gitee（gitee.com）。

Gitee 提供免费的 Git 仓库，还集成了代码质量检测、项目演示等功能。对于团队协作开发，Gitee 还提供了项目管理、代码托管、文档管理的服务，5 人以下小团队免费。

接下来我们学习一下如何使用 Gitee。

由于我们的本地 Git 仓库和 Gitee 仓库之间的传输是通过 SSH 加密的，所以我们需要配置验证信息。

1、我们先在 Gitee 上注册账号并登录后，然后上传自己的 SSH 公钥。

我们在 Git Github 章节已经生成了自己的 SSH 公钥，所以我们只需要将用户主目录下的 ~/.ssh/id_rsa.pub 文件的内容粘贴 Gitee 上。

选择右上角用户头像 -> 设置，然后选择 “SSH 公钥”，填写一个便于识别的标题，然后把用户主目录下的 .ssh/id_rsa.pub 文件的内容粘贴进去：





成功添加后如下图所示：



接下来我们创建一个项目。

点击右上角的 + 号，新建仓库：



然后添加仓库信息：



创建成功后看到如下信息：



接下来我们看下连接信息：



项目名称最好与本地库保持一致。

然后，我们在本地库上使用命令 git remote add 把它和 Gitee 的远程库关联：

git remote add origin git@gitee.com:imnoob/Viper-test.git
之后，就可以正常地用 git push 和 git pull 推送了！

如果在使用命令 git remote add 时报错：

git remote add origin git@gitee.com:imnoob/Viper-test.git
fatal: remote origin already exists.
这说明本地库已经关联了一个名叫 origin 的远程库，此时，可以先用 git remote -v 查看远程库信息：

git remote -v
origin    git@github.com:tianqixin/Viper.git (fetch)
origin    git@github.com:tianqixin/Viper.git (push)
可以看到，本地库已经关联了 origin 的远程库，并且，该远程库指向 GitHub。

我们可以删除已有的 GitHub 远程库：

git remote rm origin
再关联 Gitee 的远程库（注意路径中需要填写正确的用户名）：

git remote add origin git@gitee.com:imnoob/Viper-test.git
此时，我们再查看远程库信息：

git remote -v
origin    git@gitee.com:imnoob/Viper-test.git (fetch)
origin    git@gitee.com:imnoob/Viper-test.git (push)
现在可以看到，origin 已经被关联到 Gitee 的远程库了。

通过 git push 命令就可以把本地库推送到 Gitee 上。

有的小伙伴又要问了，一个本地库能不能既关联 GitHub，又关联 Gitee 呢？

答案是肯定的，因为 git 本身是分布式版本控制系统，可以同步到另外一个远程库，当然也可以同步到另外两个远程库。

使用多个远程库时，我们要注意，git 给远程库起的默认名称是 origin，如果有多个远程库，我们需要用不同的名称来标识不同的远程库。

仍然以 Viper-test 本地库为例，我们先删除已关联的名为 origin 的远程库：

git remote rm origin
然后，先关联 GitHub 的远程库：

git remote add github git@github.com:tianqixin/Viper-git-test.git
注意，远程库的名称叫 github，不叫 origin 了。

接着，再关联 Gitee 的远程库：

git remote add gitee git@gitee.com:imnoob/Viper-test.git
同样注意，远程库的名称叫 gitee，不叫 origin。

现在，我们用 git remote -v 查看远程库信息，可以看到两个远程库：

git remote -v
gitee    git@gitee.com:imnoob/Viper-test.git (fetch)
gitee    git@gitee.com:imnoob/Viper-test.git (push)
github    git@github.com:tianqixin/Viper.git (fetch)
github    git@github.com:tianqixin/Viper.git (push)
如果要推送到 GitHub，使用命令：

git push github master
如果要推送到 Gitee，使用命令：

git push gitee master
这样一来，我们的本地库就可以同时与多个远程库互相同步：



10.Git 服务器搭建
上一章节中我们远程仓库使用了 Github，Github 公开的项目是免费的，2019 年开始 Github 私有存储库也可以无限制使用。

这当然我们也可以自己搭建一台 Git 服务器作为私有仓库使用。

接下来我们将以 Centos 为例搭建 Git 服务器。

1、安装 Git
$ yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel perl-devel
$ yum install git
接下来我们 创建一个 git 用户组和用户，用来运行 git 服务：

$ groupadd git
$ useradd git -g git
2、创建证书登录
收集所有需要登录的用户的公钥，公钥位于 id_rsa.pub 文件中，把我们的公钥导入到 / home/git/.ssh/authorized_keys 文件里，一行一个。

如果没有该文件创建它：

$ cd /home/git/
$ mkdir .ssh
$ chmod 755 .ssh
$ touch .ssh/authorized_keys
$ chmod 644 .ssh/authorized_keys
3、初始化 Git 仓库
首先我们选定一个目录作为 Git 仓库，假定是 / home/gitrepo/Viper.git，在 / home/gitrepo 目录下输入命令：

$ cd /home
$ mkdir gitrepo
$ chown git:git gitrepo/
$ cd gitrepo

$ git init --bare Viper.git
Initialized empty Git repository in /home/gitrepo/Viper.git/
以上命令 Git 创建一个空仓库，服务器上的 Git 仓库通常都以. git 结尾。然后，把仓库所属用户改为 git：

$ chown -R git:git Viper.git
4、克隆仓库
$ git clone git@192.168.45.4:/home/gitrepo/Viper.git
Cloning into 'Viper'...
warning: You appear to have cloned an empty repository.
Checking connectivity... done.
192.168.45.4 为 Git 所在服务器 ip ，你需要将其修改为你自己的 Git 服务 ip。

这样我们的 Git 服务器安装就完成。

总结
这篇 Git 命令并不是特别的全，不过日常使用也基本能够满足了。如果有什么错误，麻烦各位在评论区指正，看见后会修改的。感觉文章不错的话点个收藏，关注我，持续更新不同高质量文章！

全文完
本文由 简悦 SimpRead 优化，用以提升阅读体验
使用了 全新的简悦词法分析引擎 beta，点击查看详细说明
前言
1.Git 安装配置
1…1Linux 平台上安装
Debian/Ubuntu
Centos/RedHat
源码安装
1.2 Windows 平台上安装
1.3Mac 平台上安装
1.4 Git 配置
用户信息
文本编辑器
差异分析工具
查看配置信息
2.Git 创建仓库
git init
使用方法
git clone
3. Git 基本操作
3.1 获取与创建项目命令
git init
git clone
3.2 基本快照
git add
git status
git diff
git commit
git reset HEAD
git rm
git mv
4. 再按照作用重新整理一下命令
一、Git 配置相关
二、本地基本操作
4、其他
三、加塞临时任务处理
四、修改个人分支历史
五、查看变更日志等
六、分支与标签
七、两分支之间的集成
八、和远端交互
九、常用回滚操作系统
5. Git 分支管理
5.1 列出分支
5.2 删除分支
5.3 分支合并
5.4 合并冲突
6. Git 查看提交历史
7. Git 标签
标签介绍
整合步骤
1 新建标签
2 创建标签
3 查看已有标签
4 删除标签
5 查看此版本所修改的内容
8. Git 远程仓库 (Github)
8.1 添加远程库
8.2 查看当前的远程库
实例
8.3 提取远程仓库
8.4 推送到远程仓库
8.5 删除远程仓库
实例
9. Git Gitee
10.Git 服务器搭建
1、安装 Git
2、创建证书登录
3、初始化 Git 仓库
4、克隆仓库
总结
共计：32030 个字符，汉字：9161，段落：1102，英文单词：3932