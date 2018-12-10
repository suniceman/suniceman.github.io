---
layout: post
title: Git 常用命令总结
subtitle: How to use git?
date: 2017-04-15 12:00:00
author: Silence
header-img: ""
# header-style: text
catalog: true
header-bg-css: "linear-gradient(to right, #404040, #404040);"
catalog: true
tags:
  - git
---

## 前言
在工作中需要对代码进行版本控制， 公司使用git进行版本控制， 花费了两天时间对git进行了学习， 现整理一些常用的git命令。

## Git常用命令

1. `git log`  查看提交历史记录
   * `git reflog`  查看命令历史

1. 回退
   1. 在工作区修改（指的是已经被追踪的文件）了但是还没add, `git checkout -- 文件名` 或 `git stash` 和  `git stash clear`
   1. 已经添加add,但是还没有commit, `git reset HEAD` 文件名(这样就会把暂存区的文件返回到工作区,接着使用步骤1的方法撤销即可)
   1. 已经添加暂存，也commit了
     * 使用`git log`查看提交历史记录，找到你提交错误的那次，然后找到它的钱一次的commit id
       `git reset --hard commitId`
   1. 已经push了，但是想要修改刚提交的commit信息
     场景：提交了一个commit信息，也push 到远程了，但是此时想要修改提交的message信息，必须是最近一次的  `git commit --amend` 此时出现窗口修改commit message信息, `git push origin 分知名 -f`
   1. 已经push了，而且不小心commit和push好几次，但是你现在没有任何改动了，此时就无法直接用`git rebase`了，但是想把commit提交合并成一次, `git log` 查找该分支的第一次提交commitId
   `git reset --soft commitId`  (--soft 和 --hard的区别就是 --soft是软回退，会将你这次commitId之后的提交全部回退到暂存区中，--hard会将你这次commitId之后的提交全部清除掉)

1. 提交前做的事情，完成一项任务之后
```git
    git add 文件
    git commit -m "fsd"
    git fetch
    git rebase origin/develop
    解决冲突。。。。（git rebase --continue）
    git push -f origin 分支名 ()
    解决
```

1. git 缓存的常用方法
    1. git stash
    1. git stash pop
    1. git stash list
    1. git stash apply stashId //从列表中获取，但是该stash还存在与list中
    1. git stash drop stashId //从列表中删除
    1. git stash clear  //删除列表

1. 将git log中的某一次提交合并的一个分支上，
    使用场景：例如我把一次提交合并到分支a了，然后将分支a（基于develop创建的）请求合并到develop，但是此时你发现要把该分支的合并倒master
    1. 创建新的分支b(基于master分支创建)
    1. 找到你那次提交的commitId号
    1. git cherry-pick commitId（在b分支下执行）
    1. git push 到远程分支上
    1. 发送merge 请求到master

    1. 更改分知名(用于没开issue号,但是想先做)
       * `git branch -M hotfix-job-sync-blocked-member(旧分支名)(空格)hotfix-649(新分知名)`