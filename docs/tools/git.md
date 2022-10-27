---
layout: default
title: git
parent: tools
last_modified_date: 2022年 10月 17日 星期一 14:17:31 CST

---

> 系统学习教材： 精通git

## git rm

工作区    暂存区    提交区，理解为每个区都存放了文件，同时每个文件相应的状态也被存放，状态有 new file / deleted / modified 等



例如：一个tracked的文件 a.txt，在暂存区有他的存放。

    只在工作区删除 a.txt 文件，工作区 a.txt 的状态就变成了`deleted`，与暂存区的a.txt的状态不同，（此时gitstatus为：*Changes not staged for commit*）故需要`git add a.txt` ，来将该文件状态放入暂存区。（此时git status为：*Changes to be committed*）

    而 `git rm a.txt` 命令就是直接将暂存区和工作区的 a.txt 都删除，相当于 

这两个命令一起 `rm a.txt  && git add a.txt`

## 怎么看懂 git diff的输出内容

`git diff` 默认查看的是 暂存区与工作目录之间的差异，暂存区为源文件（---），工作目录为目标文件（+++）

`git diff --staged` 查看的是上一次的提交（---）和暂存区（+++）之间的差异，

[[git] 看懂git diff，及其输出信息 - 一人庸 - 博客园](https://www.cnblogs.com/Vayne-N/p/9086103.html)



## git commit -am

`-a` 命令 可以直接将**已经被跟踪**的但未被暂存的文件直接提交。

但是不能直接提交未被跟踪的文件。

## 删除所有未跟踪的文件

[git删除未跟踪文件_uhippo的博客-CSDN博客](https://blog.csdn.net/uhippo/article/details/46365737)

## 添加远程仓库

`git remote add <shortname> <url>` 

该命令可以为本地 `git init` 的仓库添加远程仓库(如 URL--https://github.com/w-Mike/myleetcode )，并设定一个shortname来指代这个URL。

`git clone` 会默认是使用 origin作为远程仓库URL的shortname

`git branch -a` --- 可以展示所有分支包括远程分支

`git remote ` ---- 显示添加的远程仓库的 shortname

`git fetch <remote-name>` 会拉取添加的远程仓库的<remote-name>的所有分支，git fetch是以仓库为单位的。

`git push <remote-name> <remote-branch>` 克隆的仓库会有默认push的远程分支，一般就是 main--> main. 

    如果在你 push 之前，已经有其他人push到远程分支了，这时候远程分支的commit就会领先你的本地仓库的分支，这时候就不能push了，必须要把别人已经push的工作拉取回来，将自己的分支合并，才能push。 

> 那问题是，如果我合并后，导致结果与拉取来的远程分支的内容不一样，这样再push就往远程分支又添加了一次commit吗

`git remote show <remote-name>` ---

 显示

- 该远程仓库的URL

- git pull的配置，显示git pull会和本地仓库的哪一个分支合并

- git push的配置，显示git push会自动推送到那一条远程分支



## git fetch & git pull

https://zhuanlan.zhihu.com/p/123370920

## gitignore

示例

```cpp
bin/: 忽略当前路径下的bin文件夹，该文件夹下的所有内容都会被忽略，不忽略 bin 文件
/bin: 忽略根目录下的bin文件
/*.c: 忽略 cat.c，不忽略 build/cat.c
debug/*.obj: 忽略 debug/io.obj，不忽略 debug/common/io.obj 和 tools/debug/io.obj
**/foo: 忽略/foo, a/foo, a/b/foo等
a/**/b: 忽略a/b, a/x/b, a/x/y/b等
!/bin/run.sh: 不忽略 bin 目录下的 run.sh 文件
*.log: 忽略所有 .log 文件
config.php: 忽略当前路径的 config.php 文件
```

## Q&A

1. 如果主机A，在本地工作区修改了文件后，commit后`git push` ， 主机B也进行了`git commit` 但是没有push，这时候如果主机B进行 `git fetch`，或者执行`git push`会发生什么如何进行merge

2. 一个本地 `git init`的仓库，如何添加 remote？

3. `remote`和远程分支的区分
