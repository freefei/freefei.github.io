---
layout: post
title: git pull --rebase
category: Git
date: 2015-08-20
---

当本地commit一个提交和远端服务器中的代码有冲突(别人也改了相同的文件)时可以在pull 中加 –rebase。加上 rebase 的意思是：

git pull --rebase

1、把本地 repo. 从上次 pull 之后的变更暂存起來

2、恢复到上次 pull 时的状态

3、合并远端的变更到本地

4、最后再合并刚刚暂存下來的本地变更

<!-- more -->

合并前：

{% highlight text%}
      D---E master
     /
A---B---C---F origin/master

使用 merge 合并后：

      D--------E  
     /          \
A---B---C---F----G   master, origin/master

{% endhighlight%}

如果是 rebase 的方式，就不會有 G 合并点：

A---B---C---F---D'---E'   master, origin/master

注意到，其中 D’, E’ 的 commit SHA 序号跟本來 D, E 是不同的，应为算是砍掉重新 commit 了。

rebase vs. merge

rebase 跟 merge 类似，出现 conflict 会暂停 rebase 动作，需要你手动修复后，然后才可以继续动作。这也是 rebase 比 merge 复杂一点的地方：merge 如果发生 conflict，你只需要解决冲突一次，然后commit 出去就完成了。而 rebase 的 conflict 可能会发生在上述步骤 4 的每一次重新套用上，所以可能需要解决冲突好几次 (rebase 时所谓的解决冲突，其实是直接修改你之前的变更內容，所以上图中变成 D’ 跟 E’ )。

**配置**

如果想要把 rebase 当做 git pull 的默认值，可以在 .git/config 加上

[branch "master"]
  remote = origin
  merge = refs/heads/master
  rebase = true

也可以直接加到 ~/.gitconfig 让所有的 tracked branches 都自动套用这个设定：

[branch]  
  autosetuprebase = always

