##  Git笔记

<u>这两天把廖雪峰的git教程重新看了一遍，下面的笔记是一些总结。 2020.5.2</u>

####  1 Git简介

Git 是目前世界上最先进的分布式版本控制系统。

##### 1.1 Git的诞生

Linus（1991年创建了开源的Linux）花了两周时间用C写了一个分布式版本控制系统，也就是Git。一个月之内，Linux系统的源码已经由Git管理了。

#####  1.2 集中式VS分布式

分布式版本控制系统与集中式版本控制系统相比，首先根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样工作的时候就不需要联网了。

#### 2 安装Git

#### 3 创建版本库

首先在一个合适的地方，创建一个空目录：

```python
mkdir learngit

cd learngit

pwd
```

然后通过 `git init` 命令把这个目录变成Git可以管理的仓库：

```python
git init
```

添加文件到Git仓库，分两步：

```python
git add <file>
git commit -m <message>
```

#### 4 时光机穿梭

`git status` 命令可以让我们时刻掌握仓库当前的状态。

如果你忘记了一个文件修改了什么内容，可以用 `git diff <file>`查看。

##### 4.1 版本回退

`git log` 命令可以显示从最近到最远的提交日志，如果嫌输出信息太多可以加上

`--pretty=oneline`参数。

回退版本用 `git reset --hard HEAD^` 命令，如果回退了还想回来，可以在命令行中顺着找需要版本的 `commit id` ，用 `git reset --hard 1094a` （例）即可回到未来的某个版本。

如果命令行中找不到的话，可以用 `git reflog` 命令查看命令历史也ok。

##### 4.2 工作区和暂存区

`git add` 命令实际上就是把要提交的所有修改放到暂存区（stage）。

`git commit` 命令则可以一次性把暂存区的所有修改提交到分支（master）。

##### 4.3 管理修改

`git diff HEAD -- <file>` 明令可以查看工作区和版本库最新版本的区别。

##### 4.4 撤销修改

`git checkout -- <file>` 可以丢弃工作区的修改。

`git reset HEAD <file>` 可以把暂存区的修改撤销掉，重新放回工作区。

如果不慎提交到了分支，可以用4.1中版本回退命令。

##### 4.5 删除文件

删除文件分为本地删除和版本库删除两种。

其中版本库删除可以用如下命令：

```python
git rm <file>
git commit -m <message>
```

当然如果删错了的话，可以用 `git checkout -- <file>` 恢复一下子。

#### 5 远程仓库

##### 5.1 添加远程库

在本地仓库下运行命令：

`git remote add origin git@github.com:<username:path>/<repo>.git`

添加后，远程库的名字就是origin，这是Git默认的叫法，也可以叫成别的，但是origin这个名字一看就知道是远程库。

关联后，使用命令 `git push -u origin master` 即可第一次推送master分支的所有内容，此后，每次本地提交不必加 `-u` 参数。

##### 5.2 从远程库克隆

`git clone git@github.com:<username:path>/<repo>.git`

Git支持多种协议，包括https，但ssh协议速度最快。

#### 6 分支管理

##### 6.1 创建与合并分支

在Git里，HEAD严格来说并不是指向提交，而是指向master，master才是指向提交的。所以，HEAD指向的就是当前分支。

创建分支 `git checkout -b <branch>`

上面命令相当于如下两条命令：

```python
git branch <branch>
git checkout <branch>
```

查看当前分支的命令是 `git branch`

`git merge` 命令用于合并指定分支到当前分支。

删除分支 `git branch -d dev`

##### 6.2 解决冲突

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

`git log --graph --pretty=oneline --abbrev-commit` 命令可以看到分支合并图。

##### 6.3 分支管理策略

合并分支时，加上 `--no-ff` 参数就可以用普通模式合并，合并的历史有分支，能看出来曾经做过合并，而 `fast forward` 合并就看不出来曾经做过合并。

示例： `git merge --no-ff -m <message> <branch>`

##### 6.4 Bug分支

`git stash` 命令可以把当前工作现场储藏起来，等以后恢复现场后继续工作。

查看工作现场的命令是： `git stash list`

恢复工作现场有两个办法：

```python
git stash apply #恢复后stash内容并不删除，需要用git stash drop来删除
git stash pop #恢复的同时把stash内容也删了
```

Git提供了一个 `cherry-pick` 命令，让我们能复制一个特定的提交到当前分支。

如 `git cherry-pick 4c805e2`

##### 6.5 Feature分支

开发一个新feature，最好新建一个分支。

如果要丢弃一个没有被合并过的分支，可以通过 `git branch -D <name>` 强行删除。

##### 6.6 多人协作

查看远程库命令 `git remote (-v)`

本地推送分支失败，可以先用 `git pull` 抓取远程的新提交。

在本地创建和远程分支对应的分支用 `git checkout -b <branch> origin/<branch>`，本地和远程分支的名称最好一致。

建立本地分支和远程分支的链接关系用 `git branch --set-upstream-to <branch> origin/<branch>`

##### 6.7 Rebase

