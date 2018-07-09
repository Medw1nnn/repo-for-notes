## git简介

​	

## 创建版本库

第一步，创建一个空目录： 

```
$ mkdir learngit
$ cd learngit
$ pwd
```

pwd命令用于显示当前目录 

第二步，通过`git init`命令把这个目录变成Git可以管理的仓库 

如果你没有看到`.git`目录，那是因为这个目录默认是隐藏的，用`ls -ah`命令就可以看见 

## 把文件添加到版本库

文件一定要放到`learngit`目录下（子目录也行） 

第一步，用命令`git add`告诉Git，把文件添加到仓库： 

```
$ git add readme.txt
```

可反复多次使用，添加多个文件 

第二步，用命令`git commit`告诉Git，把文件提交到仓库： 

```
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

`-m`后面输入的是本次提交的说明，可以输入任意内容，当然最好是有意义的，这样你就能从历史记录里方便地找到改动记录 

`git commit`命令执行成功后会告诉你，`1 file changed`：1个文件被改动（我们新添加的readme.txt文件）；`2 insertions`：插入了两行内容（readme.txt有两行内容）。 

- 为什么Git添加文件需要`add`，`commit`一共两步呢？因为`commit`可以一次提交很多文件，所以你可以多次`add`不同的文件，比如：

```
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

## 版本回退

每当觉得文件修改到一定程度的时候，就可以“保存一个快照”，这个快照在Git中被称为`commit`。一旦把文件改乱了，或者误删了文件，可以从最近的一个`commit`恢复

在Git中，我们用`git log`命令查看 历史记录

eg：

```
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:06:15 2018 +0800

    append GPL

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 21:03:36 2018 +0800

    add distributed

commit eaadf4e385e865d25c48e7ca9c8395c3f7dfaef0
Author: Michael Liao <askxuefeng@gmail.com>
Date:   Fri May 18 20:59:18 2018 +0800

    wrote a readme file
```

如果嫌输出信息太多，可以试试加上`--pretty=oneline`参数 

- 每提交一个新版本，实际上Git就会把它们自动串成一条时间线。如果使用可视化工具查看Git历史，就可以更清楚地看到提交历史的时间线

- 在Git中，用`HEAD`表示当前版本 上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，往上100个版本写成`HEAD~100`。 

- 回退到上一个版本 用`git reset`命令 

  ```
  $ git reset --hard HEAD^
  HEAD is now at e475afc add distributed
  ```

- 若要回退到更加新的版本也可用reset，用commit id 的前几位

  ```
  $ git reset --hard 1094a
  HEAD is now at 83b0afe append GPL
  ```

  不知道commit id：Git提供了一个命令`git reflog`用来记录你的每一次命令： 

  ```
  $ git reflog
  e475afc HEAD@{1}: reset: moving to HEAD^
  1094adb (HEAD -> master) HEAD@{2}: commit: append GPL
  e475afc HEAD@{3}: commit: add distributed
  eaadf4e HEAD@{4}: commit (initial): wrote a readme file
  ```

  可知`append GPL`的commit id是`1094adb` 

## 工作区和暂存区

#### 工作区（Working Directory）

就是你在电脑里能看到的目录

#### 版本库（Repository）

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。 

![git-repo](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384907702917346729e9afbf4127b6dfbae9207af016000/0) 

把文件往Git版本库里添加的时候，是分两步执行的：

第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

#### 可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改 。

- #### git diff 和 git diff --cached 的理解

```
git diff    #是工作区(work dict)和暂存区(stage)的比较
git diff --cached    #是暂存区(stage)和分支(master)的比较
```

- 每次修改，如果不用`git add`到暂存区，那就不会加入到`commit`中。 

## 撤销修改

- git checkout -- filename可以丢弃工作区的修改 

命令`git checkout -- readme.txt`意思就是，把`readme.txt`文件在工作区的修改全部撤销，这里有两种情况：

1. 一种是`readme.txt`自修改后还没有被放到暂存区，现在，撤销修改就回到和版本库一模一样的状态；
2. 一种是`readme.txt`已经添加到暂存区后，又作了修改，现在，撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次`git commit`或`git add`时的状态。

- `git checkout -- file`命令中的`--`很重要，没有`--`，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到`git checkout`命令。 

- `git reset`命令既可以回退版本，也可以把暂存区的修改回退到工作区。当我们用`HEAD`时，表示最新的版本。 

  用命令git reset HEAD <file>可以把暂存区的修改撤销掉（unstage），重新放回工作区： 

  ```
  $ git reset HEAD readme.txt
  Unstaged changes after reset:
  M    readme.txt
  ```

### 小结

场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。

场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。

场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考[版本回退](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013744142037508cf42e51debf49668810645e02887691000)一节，不过前提是没有推送到远程库。

## 删除文件

- 用`rm`命令删除文件

  ```
  $ rm test.txt
  ```

`git status`命令会立刻告诉你哪些文件被删除了，此时有两个选择

1. 确实要从版本库中删除该文件，那就用命令`git rm`删掉 ，并且`git commit` 

2. 删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本

   ```
   $ git checkout -- test.txt
   ```

   `git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。 

   ### 小结

   命令`git rm`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失**最近一次提交后你修改的内容**。

# 远程库

## 添加远程库

情景：你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人通过该仓库来协作。 

- 根据GitHub的提示，在本地的`learngit`仓库下运行命令：

```
$ git remote add origin git@github.com:Medw1nnn/testrepo.git
```

​	添加后，远程库的名字就是`origin` 

- 下一步，就可以把本地库的所有内容推送到远程库上： 

  ```
  $ git push -u origin master
  ```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。

由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令：

```
$ git push origin master
```

把本地`master`分支的最新修改推送至GitHub

### 小结

要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改.

## 从远程库克隆

- 命令`git clone`克隆一个本地库：

```
$ git clone git@github.com:Medw1nnn/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 3
Receiving objects: 100% (3/3), done.
```

- 要查看远程库的信息，用`git remote`： 

  ```
  $ git remote
  origin
  ```

  或者，用`git remote -v`显示更详细的信息：

  ```
  $ git remote -v
  origin  git@github.com:michaelliao/learngit.git (fetch)
  origin  git@github.com:michaelliao/learngit.git (push)
  ```

  上面显示了可以抓取和推送的`origin`的地址。如果没有推送权限，就看不到push的地址。

# 分支管理

## 创建与合并分支

### 分支创建

创建`dev`分支，然后切换到`dev`分支：

```
$ git checkout -b dev
Switched to a new branch 'dev'
```

`（git checkout`命令加上`-b`参数表示创建并切换，相当于以下两条命令：）

```
$ git branch dev
$ git checkout dev
Switched to branch 'dev'
```

然后，用`git branch`命令查看当前分支：

```
$ git branch
* dev
  master
```

`git branch`命令会列出所有分支，当前分支前面会标一个`*`号。

现在，`dev`分支的工作完成，我们就可以切换回`master`分支：

```
$ git checkout master
Switched to branch 'master'
```

再查看readme.txt文件，刚才添加的内容不见了。因为那个提交是在`dev`分支上，而`master`分支此刻的提交点并没有变 

![git-br-on-master](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384908892295909f96758654469cad60dc50edfa9abd000/0) 

### 分支合并

现在，我们把`dev`分支的工作成果合并到`master`分支上：

```
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

合并后，再查看readme.txt的内容，就可以看到，和`dev`分支的最新提交是完全一样的。 

### 分支删除

合并完成后，就可以放心地删除`dev`分支了：

```
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

- 因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在`master`分支上工作效果是一样的，但过程更安全。 

### 小结

Git鼓励大量使用分支：

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`

创建+切换分支：`git checkout -b <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`



## 解决冲突

两个分支各自都有新的提交，这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突 

解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。 

用`git log --graph`命令可以看到分支合并图 

## 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息 

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。 

- 合并时使用`--no-ff`参数，表示禁用`Fast forward` 

  ```
  $ git merge --no-ff -m "merge with no-ff" dev
  Merge made by the 'recursive' strategy.
   readme.txt | 1 +
   1 file changed, 1 insertion(+)
  ```

  （因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。 ）

- 合并后，可用`git log`看看分支历史 

不使用`Fast forward`模式，merge后就像这样： ![git-no-ff-mode](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384909222841acf964ec9e6a4629a35a7a30588281bb000/0) 

#### 在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，`master`分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在`dev`分支上，也就是说，`dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本；

你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

![git-br-policy](https://cdn.liaoxuefeng.com/cdn/files/attachments/001384909239390d355eb07d9d64305b6322aaf4edac1e3000/0) 

### 小结

Git分支十分强大，在团队开发中应该充分应用。

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

## bug分支

每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除

若当前正在`dev`上进行的工作还没有提交 ，但工作只进行到一半，还没法提交，可`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作 

```
$ git stash
Saved working directory and index state WIP on dev: f52c633 add merge
```

### 恢复工作区

用`git stash list`命令看看：

```
$ git stash list
stash@{0}: WIP on dev: f52c633 add merge
```

工作现场还在，Git把stash内容存在某个地方了，但是需要恢复一下

有两个办法：

一是用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；

另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

- 若有多个stash，恢复指定的stash用命令：

  ```
  $ git stash apply stash@{0}
  ```

## feature分支

开发一个新feature，最好新建一个分支； 

如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。 

## 推送分支

就是把该分支上的所有本地提交推送到远程库。推送时，要指定本地分支 

```
$ git push origin master
```

如果要推送其他分支，比如`dev`，就改成：

```
$ git push origin dev
```

- `master`分支是主分支，因此要时刻与远程同步；

- `dev`分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；

- bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；

- feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

  ### 抓取分支 git pull

  多人协作时，大家都会往`master`和`dev`分支上推送各自的修改 

  - 从远程库克隆，默认只能看到本地的master分支

  要在`dev`分支上开发，就必须创建远程`origin`的`dev`分支到本地，于是用这个命令创建本地`dev`分支：

  ```
  $ git checkout -b dev origin/dev
  ```

  - 碰巧其他人也对同样的文件作了修改，并试图推送 则会冲突，Git已经提示我们，先用`git pull`把最新的提交从`origin/dev`抓下来，然后，在本地合并，**解决冲突**，再推送 

    若失败，原因是没有指定本地`dev`分支与远程`origin/dev`分支的链接，根据提示，设置`dev`和`origin/dev`的链接：

    ```
    $ git branch --set-upstream-to=origin/dev dev
    Branch 'dev' set up to track remote branch 'dev' from 'origin'.
    ```

    再pull ，成功，但是合并有冲突，则按上面方法手动解决，提交，再push

**因此，多人协作的工作模式通常是这样**：

1. 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！

如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。

这就是多人协作的工作模式，一旦熟悉了，就非常简单。

## Rebase

即把分叉的提交变成直线 

```
$ git rebase
```

- rebase操作前后，最终的提交内容是一致的，但是，我们本地的commit修改内容已经变化了 

- rebase操作的特点：把分叉的提交历史“整理”成一条直线，看上去更直观。缺点是本地的分叉提交已经被修改过了 

- rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

# 标签管理

## 创建标签

在Git中打标签（版本号）非常简单，首先，切换到需要打标签的分支上：

然后，敲命令`git tag <name>`就可以**打一个新标签**：

```
$ git tag v1.0
```

可以用命令`git tag`**查看所有标签**：

```
$ git tag
v1.0
```

- 有时候，如果忘了打标签 ，方法是log找到历史提交的commit id，然后打上就可以了 

  ```
  $ git log --pretty=oneline --abbrev-commit
  ```

比方说要对`add merge`这次提交打标签，它对应的commit id是`f52c633`，敲入命令：

```
$ git tag v0.9 f52c633
```

- 可以用`git show <tagname>`查看标签信息 

- 还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

  ```
  $ git tag -a v0.1 -m "version 0.1 released" 1094adb
  ```

- 标签**总是和某个commit挂钩**。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签 

## 操作标签

### 删除

如果标签打错了，也可以删除：

```
$ git tag -d v0.1
Deleted tag 'v0.1' (was f15b0dd)
```

如果要推送某个标签到远程，使用命令

git push origin <tagname> 

或者，一次性推送全部尚未推送到远程的本地标签：

```
$ git push origin --tags
```

如果要删除已推送到远程的标签，先从本地删除：

```
$ git tag -d v0.9
```

然后，从远程删除 。删除命令也是push 

```
$ git push origin :refs/tags/v0.9
```

# 自定义Git

## 忽略特殊文件

比如保存了数据库密码的配置文件不能提交，或每次git status显示`Untracked files` 

方法：在Git工作区的根目录下创建一个特殊的`.gitignore`文件，然后把要忽略的文件名填进去，Git就会自动忽略这些文件 

不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。所有配置文件可以直接在线浏览：<https://github.com/github/gitignore> 

### 忽略文件的原则：

1. 忽略操作系统自动生成的文件，比如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，也就是如果一个文件是通过另一个文件自动生成的，那自动生成的文件就没必要放进版本库，比如Java编译产生的`.class`文件；
3. 忽略你自己的带有敏感信息的配置文件，比如存放口令的配置文件。

- 检验`.gitignore`的标准是`git status`命令是不是说`working directory clean` 

- 想添加被忽略的文件App.class，可以用`-f`强制添加到Git：

```
$ git add -f App.class
```

​	或者发现，可能是`.gitignore`写得有问题，需要找出来到底哪个规则写错了，可以用`git check-ignore`命令检查：

```
$ git check-ignore -v App.class
```

- `.gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做**版本管理**！ 

## 设置别名

如用`st`表示`status`：

```
$ git config --global alias.st status
```

当然还有别的命令可以简写，很多人都用`co`表示`checkout`，`ci`表示`commit`，`br`表示`branch`：

```
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
```

- `--global`参数是全局参数，也就是这些命令在这台电脑的所有Git仓库下都有用 

  

  又如配置一个`git last`，让其显示最后一次提交信息：

  ```
  $ git config --global alias.last 'log -1'
  ```

  这样，用`git last`就能显示最近一次的提交：

  ```
  $ git last
  commit adca45d317e6d8a4b23f9811c3d7b7f0f180bfe2
  Merge: bd6ae48 291bea8
  Author: Michael Liao <askxuefeng@gmail.com>
  Date:   Thu Aug 22 22:49:22 2013 +0800
  
      merge & fix hello.py
  ```

### 别名配置文件

配置Git的时候，加上`--global`是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用 

每个仓库的Git配置文件都放在`.git/config`文件中

```
$ cat .git/config 
...
```

别名就在`[alias]`后面，要删除别名，直接把对应的行删掉即可 

而当前用户的Git配置文件放在用户主目录下的一个隐藏文件`.gitconfig`中：

```
$ cat .gitconfig
...
```

配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。 

## 搭建git服务器

...