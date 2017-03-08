## git pro

git checkout 主要有3种功能

- 1.切换分支
- 2.放弃对某个文件的修改(这个指的是在工作区的文件而非暂存区文件) git checkout -- filename
- 3.新建分支 git checkout -b branchname 
- 4.撤销工作区未add过的修改（不会删除untracked的文件）git co . 

git reset 主要的功能 

- 1.取消merge，git reset --hard ORIG_HEAD，这样会退回到merge前的代码。
- 2.取消commit git reset HEAD~<num> 取消num次的提交
- 3.将文件从暂存区移除 git reset HEAD filename
<br /> 
<br /> <br /> <br /> <br /> <br /> <br /> <br /> <br /> <br /> 

#### git HEAD原理讲解
http://www.tuicool.com/articles/A3Mn6f

    $ cat .git/HEAD
    ref: refs/heads/master

    $ cat .git/refs/heads/master
    8a3761ad615f61cdc784338ce62b35fc6bc694b0

做完一次commit之后

    $ cat .git/HEAD
    ref: refs/heads/master

    $ cat .git/refs/heads/master
    579181aee3ab63e36af5ab23887b1f7c24fea596


切换到新分支

    $ cat .git/HEAD
    ref: refs/heads/testHead

在不同的分支上查看HEAD文件，可以看到里面存储的内容是指向当前分支的最新的commitID
同时，在我们切换分支的时候，HEAD也会指向当前分支最新的commitID。
（如果新的分支是从原分支fork而来，那么新分支和原分支的HEAD是同一个。
或许整个git就是一个大的文件管理系统，里面存储着各个状态点的commitID，
而切换分支和提交的步骤其实都是根据HEAD的指向哪个commitID来确认我们需要的文件的状态？）

#### 进阶——HEAD懂不懂，看你怎么用

checkout命令用法如下：

    1. git checkout [-q] [<commit>] [--] <paths> ...
    2. git checkout [<branch>]
    3. git checkout [-m] [ [-b | -- orphan ] <new_branch>]  [start_point] 

用法2比用法1的区别在于，用法1包含了路径。为了避免路径和引用（或提交ID）同名而发生冲突，
可以在<paths>前用两个连续的连字符作为分隔。用法1的<commit>是可选项，如果省略，则相当于从暂存区进行检出。
<commit>既可以是某一个具体的commit hash值，也可以是某个分支名称，tag名称。不论分支也好，tag也好，它们本质上对应的都是一个commit hash值。

我们可以在master分支上面修改或者建立文件，提交之后，记录commitID。然后切换到test分支，可以使用
git checkout commitID filename 来将master分支的文件修改带到test分支。

><font color=#ff0000 size=2>**总结：第1种用法（包含< paths>的用法）不会改变HEAD头指针，主要使用于指定版本的文件覆盖工作区中对应的文件。如果省略<commit>，则会用暂存区的文件覆盖工作区中的文件(这也是为什么使用git co . 可以清除当前工作区中未add的文件)，否则用指定提交中的文件覆盖暂存区和工作区中的对应文件。**</font>
####熟悉的checkout，陌生的用法
- 1. git branch &#60; branch> &#60; start point>

以某个commit创建新分支。 在通常情况下，我们都会在当前分支的基础上，创建新分支。比如git branch new_branch

也许你不知道，我们还可以基于当前分支的某一次commit来创建分支。请看！

    $ git br newBranch 03af1e1154c8c2c
    ...
    $ git co newBranch
    ...
    $ git log
最后运行git log就可以看到newBranch中只有03af1e1154c8c2c这次提交之前的代码了。

- 2. git checkout -B &#60; branch>  强制创建新分支
- 3. git checkout --orphan &#60; branch>
  <br />
  假如你的某个分支上，积累了无数次的提交，你也懒得去打理，打印出的log也让你无力吐槽，那么这个命令将是你的神器，它会基于当前所在分支新建一个赤裸裸的分支，没有任何的提交历史，但是当前分支的内容一一俱全。新建的分支，严格意义上说，还不是一个分支，因为HEAD指向的引用中没有commit值，只有在进行一次提交后，它才算得上真正的分支。
- 4 . git checkout --merge &#60; branch><br />
   这个命令适用于在切换分支的时候，将当前分支修改的内容一起打包带走，同步到切换的分支下。<br />
    有两个需要注意的问题。<br>
    第一，如果当前分支和切换分支间的内容不同的话，容易造成冲突。<br>
    第二，切换到新分支后，当前分支修改过的内容就丢失了。<br>
    所以这个命令，**慎用！**
- 5 .git checkout -p &#60; branch><br />
   这个命令可以用来打补丁。这个命令主要用来比较两个分支间的差异内容，并提供交互式的界面来选择进一步的操作。这个命令不仅可以比较两个分支间的差异，还可以比较单个文件的差异哦！<br>
   ![](http://images.cnitblog.com/blog/161906/201305/07141455-b378c1f7d54a46c984844665606589df.jpg)

#### git寻根——^和~的区别
http://www.cnblogs.com/hutaoer/archive/2013/05/14/3078191.html

http://mux.alimama.com/posts/799


git log --graph



我:
先git stash ,然后git pull,然后git stash pop，然后在AndroidStudio里面的Version Control里面调节冲突
pull的时候加上--rebase
更新的时候要先切换到develope分支，然后stash，然后去pull,然后stash pop，执行合并操作，然后新建分支，提交

解决完冲突之后要执行git rebase --continue

在使用git pull命令的时候，可以使用--rebase参数，即git pull --rebase,这里表示把你的本地当前分支里的每个提交(commit)取消掉，并且把它们临时 保存为补丁(patch)(这些补丁放到".git/rebase"目录中),然后把本地当前分支更新 为最新的"origin"分支，最后把保存的这些补丁应用到本地当前分支上。


查看未执行push操作的commit的操作
git log master ^origin/master则可以查看未传送提交的详细信息

git的remote分支缓存
git remote update origin --prune

git reset HEAD~1只能撤销commit操作

- 1.add,commit之后执行git rebase -i HEAD~2  
- 2.会弹出一个编辑页面来，然后你把最后一笔提交前面的pick 修改成s   保存退出（保存退出时按ESC或者Ctrl+c会切换到输入命令行,：wq是退出保存，q是退出不保存）  
- 3.:然后又会弹出一个编辑页面，你把最后一条的commit message屏蔽掉（也就是加#），保存退出  
- 4.最后就可以git push -f origin yourbranch
