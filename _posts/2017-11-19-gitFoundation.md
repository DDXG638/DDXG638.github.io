---
layout:     post
title:      "git学习笔记"
subtitle:   "刚刚入门，请多指教"
date:       2017-11-13 12:00:00
author:     "ddxg"
header-img: "img/home-bg-o.jpg"
header-mask: 0.3
catalog:    true
tags:
    - 版本控制
    - git
---


## **git学习笔记**

之前很少接触git，对git的命令不熟悉，每次上线的时候都是挺怕的，特别是分支合并方面。这里我记录常用到的命令。


###**1.创建版本库**

```
git init
```
这个命令会创建一个空的仓库，目录里面会多出一个.git的目录，这个目录是Git来跟踪管理版本库的，不要更改里面的文件，会破坏git仓库的结构的。

###**2.从远程库克隆**

```
git clone git@github.com:DDXG638/DDXG638.github.io.git
```
克隆完毕后就可以看见拉下来的项目了
当修改了项目中的文件后，可以使用git status命令来查看修改的文件。我修改了index.html文件，加了一行111的注释文本，执行命令后就会显示修改的文件
![这里写图片描述](http://img.blog.csdn.net/20171119204030538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

使用git add命令将修改的文件添加到暂存区

```
git add index.html
```
添加后使用git commit命令将修改的文件提交到仓库

```
git commit -m "这里写提交时附加的信息文本"
```
###**3.版本回退**
可以使用git log命令查看提交的历史记录
![这里写图片描述](http://img.blog.csdn.net/20171119205340919?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
图中红框框出来的是提交的版本号，最上面的是最新的版本，就是刚刚修改index.html文件后提交的版本。

可以使用git reset命令进行版本回退。在Git中，用HEAD表示当前版本，也就是最新的提交，上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。现在回到上一个版本：

```
git reset --hard HEAD^
```
![这里写图片描述](http://img.blog.csdn.net/20171119210926960?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
版本回到了版本id为ce789d开头的版本，即修改index.html文件之前的版本，工作区的代码也回到了上一个版本。
上面的命令也可以使用改为：git reset --hard ce789d这中id的形式。如果后悔回退了，也是可以回去的，只要知道提交id，就可以在版本之间跳来跳去。

###**4.工作区和暂存区**
![这里写图片描述](http://img.blog.csdn.net/20171119202612058?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
1.工作区：就是你在电脑里能看到的目录
2.版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

把文件往Git版本库里添加的时候，是分两步执行的：
第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区；
第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支。
可以这样理解，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

###**5.撤销修改**
1.还没有add操作的撤回

```
git checkout -- fileName
```
意思是让这文件回到最近一次git commit或git add时的状态。

2.文件已经add到暂存区了，但是还没有connmit

```
git reset HEAD fileName
```
把暂存区的修改撤销掉（unstage），重新放回工作区。git reset命令既可以回退版本，也可以把暂存区的修改回退到工作区，HEAD表示最新的版本。然后再用git checkout操作将工作区的修改撤回。

###**6.创建与合并分支**
第2步克隆下来的是master分支，即主分支。
![这里写图片描述](http://img.blog.csdn.net/20171119192528913?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
这个HEAD指向的就是当前分支。一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点。每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长。

创建一个dev分支然后切换到dev分支：

```
git branch dev
git checkout dev
```
![这里写图片描述](http://img.blog.csdn.net/20171119214503065?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
可以使用git branch命令来查看当前处在什么分支中，使用git branch --all命令查看所有的分支，当前分支的前面会有一个*号。git checkout命令后面没有--的话就是切换分支的命令，有--的话就是撤回的操作。
当在dev分支修改文件，然后add，然后commit了，切换回master分支后的状态为：
![这里写图片描述](http://img.blog.csdn.net/20171119215124986?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
可以发现dev分支走在了master分支的前面，而刚刚在dev分支上所做的修改在master分支上是没有的。这是就需要将dev当的修改合并到master分支上：

```
git merge dev
```
git merge命令用于合并指定分支到当前分支，这是master的内容和dev分支的最新提交是完全一样的。
确定合并成功后就可以将dev分支删掉了：

```
git branch -d dev
```

###**7.解决冲突**
多人开发的时候很可能会遇到合并冲突，假如有人修改了dev分支的某个文的的某一行并且提交commit了，而master分支中也有人修改了与dev分支同一个文件的同一行，也进行提交commit了，在dev想master合并的时候就会产生冲突。
![这里写图片描述](http://img.blog.csdn.net/20171119221402089?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
上图就说明存在冲突，需要手动解决冲突。直接打开README.md文件，Git用<<<<<<<，=======，>>>>>>>标记出不同分支的内容，需要我们手动选择，然后在add。。。commit。。。
![这里写图片描述](http://img.blog.csdn.net/20171119221643482?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
最后状态变成了这样：
![这里写图片描述](http://img.blog.csdn.net/20171119222129927?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
可以使用`git log --graph --pretty=oneline --abbrev-commit`
查看状态：
![这里写图片描述](http://img.blog.csdn.net/20171119222425360?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQWxpZ3VhZ3Vh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

###**8.生成diff文件**
```
git diff > a.diff
```

可以指定生成某个文件夹下的diff
```
git diff src/front > a.diff
```




















































































