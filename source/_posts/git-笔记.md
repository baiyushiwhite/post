title: git 笔记
date: 2015-08-28 15:25:03
categories: [工具]
tags: [工具]
---
## 1.svn和git的差别
- Git是一款分布式的版本控制软件，svn是集中式的版本控制软件。
- git可以进行本地的版本控制，而且不影响中心服务器中的代码。而svn不行，只要commit，更改就会提交到服务器。
- 因为git每次commit的时候保存的是快照，而svn保存的是差异比较，所以git分支切换非常快 
## 2.git状态
![git状态图](/img/git/git.jpg)
## 3.git常用指令
- 基本指令
git init 初始化新仓库
git clone [url] 克隆仓库
git status 检查状态
git log 查看历史
git diff 查看差异

- Modified状态快照：
 - git add file 文件进行跟踪/将修改添加到暂存 --> staged。
 - git checkout -- file 取消对文件的修改 --> unmodified
- staged状态快照：
 - git commit –m “xxx”  提交staged快照 --> unmodified
 - git reset HEAD file 取消暂存（即add后的文件）--> modified

- 分支操作
git checkout 切换分支
git checkout –b 新建并且换到新分支
git branch 查看branch
git branch -r 查看远程分支
git branch -d 删除分支
git push origin:[name] 删除远程分支，推送一个空分支到远程分支，其实就相当于删除远程分支
git merge [branch] 合并分支

- 远程操作
git remote show 查看远程库
git remote add origin [url] 添加远程分支
git push [远程仓库名如origin] [分支名master] 将本地分支推送到服务器上去
git fetch 获取服务器内容但不合并
git pull 本地与服务器端同步

- 撤销
git reset [--soft | --mixed | --hard] [<commit>]
1）使用参数--hard，如git reset --hard <commit>
①替换引用的指向。引用指向新的提交ID。
②替换暂存区。替换后，暂存区的内容和引用指向的目录树一致。
③替换工作区。替换后，工作区的内容变得和暂存区一致，也和HEAD所指向的目录树内容相同。
2）使用参数--soft，如 git reset --soft <commit>
只更改引用的指向，不改变暂存区和工作区。
git reset --soft HEAD^
git commit -e -F ''
相当于git commit --amend
3）使用参数--mixed或者不使用参数（默认为--mixed），如 git reset <commit>
更改引用的指向及重置暂存区，但是不改变工作区。相当于将之前用git add 命令更新到暂存区的内容撤出暂存区
解决冲突：
编辑冲突文件后提交。

参考：
http://git-scm.com/docs/git-reset
http://www.cnblogs.com/craftor/archive/2012/11/04/2754140.html