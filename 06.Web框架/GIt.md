# Git命令总结
git命令众多，但是常用的就那么几个，本文章总结我练习时，常用（必用）的命令
## 起步
`git init` 初始化仓库

## 状态
`git status`查看当前的状态
`git status -s` 查看缩写的状态（看起来更简洁清晰）

`git log` 查看日志
`git log --oneline` 把日志呈现在一行（看起来更简洁清晰）
`git log --oneline --graph`以图形的方式呈现log

`git diff`查看修改的内容

## 基本操作
`git add <file>` 添加某个文件到暂存区（工作区-->暂存区）
`git add . `添加**所有文件**到暂存区

暂存区里的文件，需要全部提交到版本库里，才能进行版本控制
`git commit -m "提交说明"`提交到版本库

有时候嫌弃这个过程繁琐，可以把add 和commit用一行语句实现，一步到位（注意，只有对已追踪文件的修改，才能用。新文件还是老老实实操作吧！）
`git commit -a -m"提交说明"` 添加的同时，提交到版本库中

`git rm <file>`删除版本库中的某个文件
如果误删了，那就把版本库中的拿出来
`git checkout <file>`
## 后悔药
#### 文件的撤销
`git restore <file>`撤销修改

这里有两种情况：
- 改了，但还在工作区（没add）
- add到暂存区了，还没提交(commit)
都可以使用这一个命令

#### 版本的回退
`git reset` 回退命令
`git reset --hard HEAD^`回退到上N个版本（N=^的个数）
`git reset --hard HEAD~N`也可以直接写N（N是个整数）
但是这样做不是长久之计，用版本号更保险
`git reset --hard 版本号`回退到指定版本
这个版本号怎么获取呢？
用上面提到的`git log --oneline`直接获取
## 分支管理
实际开发中，一般有个生产版本（master），还有个开发版本(dev)
#### 基本操作
`git branch` 查看当前分支
`git branch 分支名称` 创建分支
`git switch 分支名称`切换分支

`git branch switch -c` 创建切换二合一
#### 分支合并
`git merge 分支名`当前分支合并制定分支

如果有冲突怎么办？
找到冲突文件，手动修改然后用`git commit -a -m"提交说明"`进行提交，系统会提示冲突已经解决。

`git merge --no-ff`非快速合并，作为一次新的提交合并，保留分支合并信息

## 远程仓库
说git不说github，那就是耍流氓

首先拿到一个SHH key，看看用户目录下有没有.ssh文件夹，如果有，再看看这个目录下有没有id_rsa和id_rsa.pub这两个文件，如果没有，则：
`ssh-keygen -t rsa -C "youremail@example.com"`
然后把`id_rsa.pub`的内容，添加到远程仓库账号里，才有资格推送东西

如果推送到一个空白的远程仓库，直接：
`git remote add 远仓名 地址`与远程仓库进行关联
可以关联多个仓库，只要名称不同即可，比如同时推送到github和gitee

`git remote -v`查看关联远程仓库信息

关联完成后，则可向远程仓库推送内容：
`git push 远仓名 分支名` 把你想推送的分支推送过去

如果拉取一个已有内容的远程仓库，直接：
`git clone 地址`

## 多人协作

先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，解决冲突，再推送`git push`



如果你的小伙伴，也clone了远程仓库的项目，则他只有一个master分支
如果想再拥有dev分支，则需在本地创建分支的同时加上远程分支名称，如：
`git switch -c dev github/dev`

但是这样还不够，你会发现这个分支的内容无法推送到远程仓库，这是因为此分支还没有和远程的dev建立链接，用下面这个语句：
`git branch --set-upstream-to=github/dev dev`即可建立链接

补充：如果有时突然接到一个BUG，不得不停下手中的工作，则需要**保留现场**
`git stash`保留现场，再去处理BUG
处理完事了怎么办呢，恢复现场呗！
`git stash pop` 把最近的一个现场恢复了。
`git stash apply 现场id`可以指定恢复哪个现场
这个现场id用`git stash list` 查看

如果刚刚解决的解决的BUG已经合并到merge上了，但是现在dev上还是存在这个BUG，怎么同步呢？
用`git cherry-pick <commit>`即可复制无BUG内容到当前分支

#### 以上是我在学习过程中，认为是最常见的命令，更多细节，还需不断实践，参考官方文档。