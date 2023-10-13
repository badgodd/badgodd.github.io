---
title: 'git使用中 error: pathspec ‘xxx‘ did not match any file(s) known to git 报错解决方法'
date: 2022-01-03 20:11:04
tags: 
- Git
categories:
- Git
---

## 一、问题描述

项目上想合并代码，以自己的分支master作为主分支，要合并分支study，结果
在 git merge study 的时候报如下错误
其实在 git checkout 的时候也会出现这个错误

```
error: pathspec 'origin/XXX' did not match any file(s) known to git.
发现是本地git没有识别到远程git仓库的分支
```

## 二、解决办法

1、首先看下所有分支 是否有新分支

```
git branch -a
```

2、如果没看到，那么执行以下操作，这步是获取所有分支

```
git fetch
```


执行完会看到这样提示

```
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
Unpacking objects: 100% (4/4), 1.06 KiB | 90.00 KiB/s, done.
From codeup.aliyun.com:5eeb0689892c58bb7c394ab5/pxb/pxb-fronted
 * [new branch]      XXX -> origin/XXX

```



3、切换到远程分支:

git checkout origin/xxx

提示：

```
Note: switching to 'origin/xxx'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at dc877cd XXX

```

4、现在可以看到自己的分支是一串数字字母，这时新建并切换到分支

```
git checkout -b xxx
```

5、现在需要跟远程的分支进行关联

```
git branch -u origin/xxx
```

6、这时我们执行git pull来看看什么反馈:

```
Already up-to-date.
```

