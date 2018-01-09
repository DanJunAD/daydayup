
# 基本概念
* git：是一个分布式版本控制系统
* 版本控制：记录一个或若干文件内容变化，以便将来查阅特定版本修订情况。通俗来讲，就是对某个文件的内容进行多次修改＋提交后，可以回溯到之前的状态。
* 集中化的版本控制：为了能让不同系统上的开发者协同工作，集中化的版本控制系统应运而生。所谓的集中化，就是有一个集中管理的服务器，用来保存所有文件的修订版本，然后协同工作的人们都通过客户端连到这台服务器，从这台服务器下载最新的文件或者提交文件。但是   这样做存在一个很严重的缺点－－服务器的单点故障。一旦服务器发生故障，将会影响到所有人的工作，而且一旦服务器的磁盘发生故障，将会有丢失数据的风险。
* 分布式的版本控制：客户端把代码仓库完整的镜像下来，这样即使服务器发生故障，也能用任何一个镜像出来的本地仓库进行恢复

# git 工作过程
* git内文件的三种状态：已提交（committed），已修改(modified)，已暂存（staged）。
* 文件流转的三个工作区域：工作区，暂存区，本地仓库。
* 工作过程：
    * 文件在工作区被修改后，状态变为modified。
    * 对已修改的文件进行快照并保存到暂存区，文件状态变成staged。
    * 提交更新，把暂存区的文件快照转存到本地仓库，文件状态变成committed。

[](../img/git/git.png)

# git 常用指令
## 配置
```
// 配置全局用户信息
$ git config --global user.name "xxx"
$ git config --global user.email "xxx@xxx.com"

// 配置默认编辑器
$ git config --global core.editor sublime

// 查看配置信息
$ git config --list
$ git config user.name
$ git config user.name
```
## 创建本地仓库
```
// 在当前目录进行初始化
$ git init

// 新建一个目录并初始化
$ git init [project-name]

// 下载一个项目和它的整个代码历史
$ git clone [url]
```
## 分支
```
// 列出所有本地分支
$ git branch

// 列出所有远程分支
$ git branch -r

// 基于当前分支，新建一个分支，但依然停留在当前分支
$ git branch [branch-name]

// 基于当前分支，新建一个分支，并切到新分支
$ git checkout -b [branch-name]

// 切换分支
$ git checkout [branch-name]

// 切换到上一个分支
$ git checkout -

// 合并指定分支到当前分支
$ git merge [branch]

// 删除本地分支
$ git branch -d [branch-name]

// 删除远程分支
$ git push origin --delete [branch-name]
$ git push origin
// push一个空分支到远程分支，相当于删除远程分支
$ git push origin :[branchName]
```
## 文件状态变动
```
// 查看文件状态
$ git status

// 将文件添加到暂存区
$ git add [filename]
$ git add .

// 将暂存区的文件提交到本地仓库中
$ git commit -m "update"

// 删除文件（从暂存区中移除，移除的必然是已跟踪的文件），-cached表示只是把文件从跟踪名单中移除，仍然把文件保留在当前工作目录中
$ git rm [filename]
```
## 查看记录
```
// 查看未暂存的内容
$ git diff

// 查看已暂存的内容
$ git diff --staged

// 查看历史提交
$ git log

// 显示指定文件是什么人在什么时间修改过
$ git blame [file]
```
## 远程仓库
```
// 下载远程仓库的所有改动
$ git fetch [remote]

// 增加一个新的远程仓库，并命名
$ git remote add [shortname] [url]

// 取回远程仓库的变化，并与本地分支合并
$ git pull [remote] [branch]

// 上传本地指定分支到远程仓库
$ git push [remote] [branch]
```

## 撤销
```
// 撤销对文件的修改
$ git checkout [file]

// 取消暂存
$ git reset HEAD [file]

// 将指定文件回退到指定版本
$ git checkout [old commit hash] [filename]

// 将工作区的指定文件退回到指定分支
$ git checkout [branch-name] [filename]

// 重置当前分支的HEAD为指定commit，同时重置暂存区和工作区，与指定commit一致
$ git reset --hard [commit]
```
## 版本管理
```
// 重置暂存区与工作区，与上一次commit保持一致
$ git reset --hard HEAD^

// 查看所有版本号
$ git reflog

// 退回到指定版本
$ git reset --hard [版本号]
```
