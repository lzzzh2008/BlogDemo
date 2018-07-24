---
title: git命令总结
date: 2018-04-25 16:50:09
tags: git
---
# git命令总结
## 创建版本库 
通过`git init`命令来初始化

通过`git add <fileName> `来把工作区的指定文件添加到暂存区，`git add .`把所有改动添加到暂存区。

`git commit -m 'XXX'`提交，XXX为提交的备注信息，必填
<!-- more -->

`git status`可以查看当前工作区的状态，告诉我们那些文件修改过
```
$ git status
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#    modified:   readme.txt
#
no changes added to commit (use "git add" and/or "git commit -a")
```
>如果工作区的文件修改过但是不知道哪里做了修改，可以使用`git diff`来查看改动
>`git diff HEAD --<fileName>`来查看工作区特定文件和版本库中最新版本的区别

## 版本回退

`git log` 查看所有的提交日志 通过`git log --pretty=oneline`来简化显示

```
$ git log
commit c1c072c42226dfe36ea2e38a3de74a7437a0e0c7 (HEAD -> master, origin/master)
Author: zhaozenghui <zhaozenghui@wrdtech.com>
Date:   Wed Apr 25 13:40:46 2018 +0800

    delete ae

commit 7e755b1263bdbb500fb4a5cff39d91b665b4723c
Author: zhaozenghui <zhaozenghui@wrdtech.com>
Date:   Wed Apr 25 13:37:13 2018 +0800

    create jsDemo
```
>其中的`commit`后面的字符是表示每一次commit的ID，可以用来回退版本
>`(HEAD -> master, origin/master)`中的HEAD是当前版本的指针，`HEAD^`是上一个版本，`HEAD^^`是上上一个版本，如果回退更多版本就使用`~`，比如回退5个版本`HEAD~5`

使用`git reset`命令回退版本

```
$ git reset --hard HEAD^
HEAD is now at 7e755b1 create jsDemo
```
也可以直接使用commit id来回退版本
```
$ git reset --hard c1c0
HEAD is now at c1c072c delete ae
```

>这里的`c1c0`就是之前的commit ID，但是如果我们不记得commit ID怎么办，就可以使用`git reflog`来查看历史命令记录

```
$ git reflog
c1c072c (HEAD -> master, origin/master) HEAD@{0}: reset: moving to c1c0
7e755b1 HEAD@{1}: reset: moving to HEAD^
c1c072c (HEAD -> master, origin/master) HEAD@{2}: reset: moving to c1c0
7e755b1 HEAD@{3}: reset: moving to HEAD^
c1c072c (HEAD -> master, origin/master) HEAD@{4}: reset: moving to c1c0
7e755b1 HEAD@{5}: reset: moving to HEAD^
c1c072c (HEAD -> master, origin/master) HEAD@{6}: commit: delete ae
7e755b1 HEAD@{7}: commit (initial): create jsDemo
```
## 撤销修改
1、如果想要撤销工作区的修改

    `git checkout -- fileName`

2、如果要撤销在暂存区的修改，也就是已经`git add`到暂存区的内容

    `git reset HEAD fieName`把文件撤回到工作区，再执行工作区的撤销即可。
## 删除文件
`git rm fileName` 如果想要直接删掉直接`git commit`即可

如果删错了，想要还原`git checkout -- fileName`即可

## 远程仓库
关联远程仓库 `git remote add origin git@server-name:path/repo-name.git`

第一次推送master分支所有内容`git push -u origin master`，下一次在推送的时候就不用加`-u`了

克隆远程仓库 `git clone`

## 创建合并分支
查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

未完待续……