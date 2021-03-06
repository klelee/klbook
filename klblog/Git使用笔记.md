着廖雪峰的教程简单学习了一点点Git的相关只是，做一个小小的记录！

## 创建版本库

```bash
mkdir <repository name>    ## 建立仓库
cd <repository name>     ## 必须先进入，然后初始化
git init    ## 初始化该仓库
```

建立并初始化目录后，在当前目录下会生成一个`.git`的文件夹，在Windows端会显示，Linux端点开头文件为隐藏文件

```bash
git add <file name>    ## 向缓冲区添加一个文件
git commit -m "当次上传说明（建议填写且有意义）"    ## 提交到仓库
```

## 时光机穿梭

`git status`  返回当前仓库的状态

`git diff`  查看本地文件和仓库中的文件的difference

### 版本回退

`git log`  可以查看从最近到最远的提交日志，可以添加参数`--pretty=oneline` 使每条信息只显示一行

版本回退首先要明白回退到哪个版本，在git中用HEAD表示当前的版本，所以上一个版本就是HEAD^，上上一个版本就是HEAD^^，往后就不会这么写了，写成HEAD~100

`git reset --hard HEAD^`  表示回退到上一个版本

那么回退到上一个版本之后后悔了怎么办呢？

`git reset --haed <之前的版本的commit号（输入前几位能区分就行）>`  就可以后悔了

### 工作区和暂存区

#### 工作区（working Directory）

电脑里能看到的目录，就是当前文件夹

#### 版本库（Repository）

就是工作区的`.git`文件夹。这个不算是工作区，是版本库。

Git的版本库有很多东西，最重要的是stage（或者叫index）的**暂存区**，还有第一个分支master以及指向该分支的指针HEAD。

当使用git add的时候,实际上是给文件一个指针存到stage里面去

然后使用commit的时候,再把该指针提交到分支当中去

### 管理修改

git管理的是一次次的修改,所以每一个文件,逢修改必添加,若第一次修改之后添加过,而后再修改,未添加就提交,那么提交的只是第一次的修改

### 撤销修改

1. 撤销工作区的修改,也就是恢复到最后一次add或者commit的版本

```
git restore <file name>
git checkout -- <file name>
效果相同
```

注:restore是git 2.23版本新增的命令,用来代替身兼多职的git checkout.功能是一样的

2. 当需要回退暂存区时(此时文件已经add到暂存区,还未进行commit)

```
git restore --staged<file name>
git reset HEAD<file name>
```

### 删除文件

先删除本地文件

`rm <file name>` 此时`git status` 晓得你在工作区已经删除了此文件,他会给出两个选择:

1. 从版本库彻底删除

   `git rm <file name>`

2. 误删,需要恢复到本地工作区

   `git checkout --<file name>`  (过时)

   `git restore <file name>` 

## 远程仓库

配置SSH

在用户目录下` ssh-keygen -t rsa -C "klelee@outlook.com"`所有选项回车默认

然后在用户目录下会生成.ssh文件夹,里面会有id_rsa and id_rsa.pub

其中id_rsa.pub中就是公钥,另外一个问价夹中是私钥

### 添加远程仓库

在github或者gitee建立新的仓库

#### 将本地仓库推送到远程仓库

```
git remote add origin https://gitee.com/klelee/learngit.git
git push -u origin master
```

上面推送的命令中u参数,表示不但上传了所有的文件,还将本地的master分支与远程的master分支进行了绑定

之后本地进行修改之后,可以通过

`git push origin master`进行推送

#### 删除远程库

想要删除一个远程库可以使用git remote rm <name>命令.使用前可以查看远程库的信息:

`git remote -v`

这样子的删除,只是删除了本地库和远程库之间的联系,本地库和远程库本身都还是存在的,要删除真正的远程库,需要登陆到远程库进行删除.

## 分支管理

1. 创建新的分支并切换到新分支

   ` git cheackout -b dev ` 

   git checkout 加上-b命令表示，创建并切换到新分支，相当于两条命令

   ```
   git branch dev
   git checkout dev
   ```

然后可以用git branch来查看当前分支：

`git branch`它会列出所有的分支，并在当前分支前面用星号标示

---

这样，之后的所有操作就是在dev分支上进行了

---

当在新分支上的工作结束时，要将新的分支合并到master分支上

切换回到master分支`git checkout master`

合并两个分支`git merge dev`

合并之后删除新的分支`git branch -d dev`

#### switch

git在新版本中提供了新的切换分支的方式

`git switch -c dev` 可以创建并切换到dev分支

分支之间的切换`git switch dev`

### 解决冲突

假设在新的分支`feature1`上做了修改并添加提交，然后回到master分支，此时git还会提示我们当前的master比远程的master分支要超前一个提交，此时在master上修改。然后，master和新的分支feature1都有了新的提交，这种情况下git无法快速合并，若试图把各自的修改合并起来就会产生冲突。

所以，在遇到这种情况的时候就需要在文件中手动修改了。

### 分支管理策略

#### 强制禁用 Fast forword模式

在禁用fast froword的情况下，git在merge的时候就会生成一个新的commit，这样，在分支历史上就可以看出分支信息。

```
git switch -c dev
## 操作
git add <file>
git commit -m "reason"
git switch master
git merge --no-ff -m "merge with no-ff" dev ## 禁用fast forword的合并
```

在上面合并中，使用-m参数做上传说明使用为no-ff上传本身就带有一次commit

### bug分支

假设你在工作中突然接到命令需要修改一个其他的bug，此时？？？

首先，需要将现在手头的工作放下：`git stash` 可以将当前的进度保存

然后你就可以去你需要修改bug的分支，创建一个新的分支去修改bug了，加入你的bug在master分支上：

```
git switch master
git switch -c dev
## 修改bug
## bug修改完成
git switch master
git merge --no-ff -m "fixed bug" dev
git branch -d dev
```

然后返回到你之前工作的分支，用`git stash list`可以查看你保存的工作进度，

使用：

```
（1） 
git stash apply ## 可以恢复你的工作进度，但是不会删除内存上存储的你的进度，需要使用
git stash drop  ## 释放存在内存上你的进度
（2）
git stash pop  ## 可以直接从内存中提取出你的工作进度
```

反思：既然master分支上出现了bug，那么早期的dev分支的代码应该也有bug才对，那么早期dev分支的bug怎么办呢？

这里只需要把刚刚在master修改bug的commit复制到dev，只复制他的修改。这里git提供了一个cherry-pick命令，可以复制一个特定的提交到当前分支

### feature分支

开发新功能需要开辟的新的分支

在新分支中，对于已经添加并提交的修改，要删除需要强制删除使用参数-D

### 多人协作

#### 推送分支

`git push origin master`  主分支，时刻与远程同步

`git push origin dev`  开发分支，团队需要在dev分支上面工作，因此需要远程同步

bug分支，只用于修复bug不用同步

feature分支要不要推送取决于新功能你一个人能都开发的了

#### 抓取分支

`git pull` 多人协作时，当你和你的伙伴同时对一个文件进行修改，你的伙伴先push，那么你就得，先将最新的修改pull下来，在本地进行合并，如果合并有冲突，先解决冲突，然后进行提交

## 标签管理

### 创建标签

`git tag <tag name>` 创建一个新标签，在当前分支的最近一次提交上

`git tag <tag name> <commit code>`  创建一个新标签，在当前分支的制定commit上

`git tag` 查看所有标签

`git show <tag name>` 查看标签信息

### 操作标签

如果一个标签打错了，可以删除`git tag -d <tag name>`

推送某个标签到远程`git push origin <tag name>`

或者一次性将所有标签都推送到远程`git push origin --tags`

但是如果标签已经推送到远程再想要删除，就得先删除本地标签，然后在删除远程标签`git push origin :refs/tags/<tag name>`







