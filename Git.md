# 1、Git 安装

<ol>
    <li>git官网下载安装程序，下载地址：https://git-scm.com/downloads，默认选项安装。</li>
    <li>安装完成后，在开始菜单里找到“Git”->“Git Bash”，蹦出一个类似命令行窗口的东西，就说明Git安装成功！</li>
</ol>
初始配置,在cmd或者Git Bash中输入：

```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

查看配置：

```shell
git config user.name
git config user.email
git config --list
```

查看版本：

```bash
git --version
git version 2.31.1.windows.1
```



# 2、创建版本库

版本库又名仓库，英文名**repository**，可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

1. 定位到文件夹（目录）中。

   在 ==git bash== 中通过 pwd 查看当前bash所在的目录。

   ```bash
   $ pwd
   /c
   ```

   现在需要切换到F盘的git-store目录中：

   ```bash
   $ cd /f/git-store/
   ```

   这个和用==cmd==的切换不一样，直接输入盘符即可。

   ```shell
   F:
   ```

2. **把该目录变成Git工作区**

   ```bash
   $ git init
   Initialized empty Git repository in F:/git-store/.git/
   ```

   此时会生成一个隐藏文件夹`.git`，这个文件夹就是版本库。

3. 获取文件当前的一个“状态”，即使git可以对该文件进行记录，如果没有这个add，git就不知道把文件恢复成怎样，即每个需要管理的文件都需要用add给它拍一个快照放到暂存区。

   ```bash
   $ git add readme.txt
   ```

   *命令`git add <file>`，可反复多次使用，添加多个文件；*

4. 把文件的“状态”提交到master分支，说明git的版本库已经记住那个时刻文件的样子了。

   ```bash
    git commit -m "写一些简介"
   ```

# 3、文件版本管理命令

1. 仓库状态查询，可查看是否有文件被修改过。

   ```bash
   $ git status
   ```

2. 比较同个文件的变化，即对比该文件的当前状态与最后一次git add状态的不同处。

   ```bash
   $ git diff readme.txt                                # 查询命令
   diff --git a/learn/readme.txt b/learn/readme.txt
   index ee2c1ea..ef51426 100644
   --- a/learn/readme.txt
   +++ b/learn/readme.txt
   @@ -1,2 +1,2 @@
   -Git is a distributed version control system.        # add时文件的内容快照
   +Git is a dis version control system.                # 当前的文件内容。
    Git is free software
   \ No newline at end of file
   ```

3. 放弃近一次的 `git add` 快照。

   ```bash
   git restore --staged readme.txt
   ```

   类似 `git reset`不加 `--hard`,*此时使用 commit 提交时，就不会保存该快照。*

4. 恢复到上次提交（add）的版本，即丢弃修改。

   ```bash
   $ git restore readme.txt
   ```
   两个功能一样：
   ```bash
   $ git checkout -- readme.txt
   ```

5. 查看历史提交的版本

   ```bash
   $ git log
   commit cbe42abe48f639c830df6c0935035ae9dad487fd (HEAD -> master)
   Author: lethe <yan-dream@qq.com>
   Date:   Fri May 14 15:08:33 2021 +0800
   
       append GPL
   …………省略…………
   commit 797b0694b54d691b6a20ea27d0f1ad191f3abd20
   Author: lethe <yan-dream@qq.com>
   Date:   Fri May 14 14:14:51 2021 +0800
   
       wrote a readme file
   ```

   输出简易信息命令：

   ```bash
   $ git log --pretty=oneline
   cbe42abe48f639c830df6c0935035ae9dad487fd (HEAD -> master) append GPL
   f7af7c4ad47c5be78c96f27d50da33d9580b5d31 add distributed
   797b0694b54d691b6a20ea27d0f1ad191f3abd20 wrote a readme file
   ```

   

6. 恢复到指定版本

   **方法1：**

   HEAD 指向当前所在的版本。 上一个版本就是`HEAD^`，上上一个版本就是`HEAD^^`，当然往上100个版本写100个`^`比较容易数不过来，所以写成`HEAD~100`。

   ```bash
   $ git reset --hard HEAD^        # 退到上一个版本，即“add distributed”这个版本。
   HEAD is now at f7af7c4 add distributed
   ```

   此时仓库的内容已变成了add distributed这个快照时的内容了。

   如果是 `$ git reset --hard HEAD`那就是恢复到当前的版本，类似第4条。**但也有区别，第4条只能恢复到最新add的状态，而这个命令是恢复到最新一次commit的状态。**

   **方法2：**

   使用具体的某个版本号来恢复。

   ```bash
   $ git reset --hard cbe42ab
   HEAD is now at cbe42ab append GPL
   ```

   此时恢复到了“append GPL”这个版本。

7. 查看所有历史命令记录

   ```bash
   $ git reflog
   cbe42ab (HEAD -> master) HEAD@{0}: reset: moving to cbe42abe4
   f7af7c4 HEAD@{1}: reset: moving to HEAD^^
   ...（省略）...
   cbe42ab (HEAD -> master) HEAD@{12}: commit: append GPL    
   f7af7c4 HEAD@{13}: commit: add distributed
   797b069 HEAD@{14}: commit: wrote a readme file
   5c5bf41 HEAD@{15}: commit (initial): wrote a readme file
   ```

# 4、工作区与暂存区解释

1. 工作区
   就是在电脑里能看到的目录，`git init`用来初始化的文件夹。

2. 版本库

   工作区有一个隐藏目录`.git`，就是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

   ![示例图](.\Git.assets\0.jpg)

   把文件往Git版本库里添加的时候，是分两步执行的：
   
   第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；
   
   第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。
   
   因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。
   
   可以简单理解为，需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

# 5、管理文件的修改

Git管理的是修改，而不是文件。包括：新增了一行，删除了一行，更改了某些字符，删了一些又加了一些字符，创建一个新文件。

查看当前文件与版本库中最新的版本(即当前HEAD指向的版本)的区别：

```bash
$ git diff HEAD -- readme.txt
```
查看当前文件与上个版本的区别：

```bash
$ git diff HEAD^ -- readme.txt
```



## 5.1 恢复文件/撤销修改

如过在文件readme.txt最后添加了一行文字“123456789”，并用add添加了，此时要恢复文件，步骤为：

1、清空暂存区中的add

​	方法一：

​	`git restore --staged readme.txt`

​	方法二:

​	`git reset HEAD readme.txt`

​	恢复到最新一次commit的HEAD，即相当于清空了暂存区。

2、恢复文件

​	方法一：

​	`git restore readme.txt`

​	方法二：

​	`git checkout -- readme.txt`

## 5.2 删除文件及恢复

1、删除文件

  如果文件夹（工作区）中有一个test.txt文件被手动删除了。

  可以用`git status`查看状态，根据提示删除版本库中的对应文件。

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt
```

看上面第4行的提示，可以用`git add/rm <file>`来删除版本库的文件，即：

`git add test.txt` 或 `git rm test.txt` 两个效果一样，然后commit一下就好。

2、恢复删除的文件

   方法和上面的 恢复文件 一样。

# 6、远程仓库

## 6.1 添加远程仓库

1. 生成密钥

```bash
$ ssh-keygen -t rsa -C "yan-dream@qq.com"      # 之后一直回车（默认）即可。
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/yanhy/.ssh/id_rsa):  # 密钥存放位置。
Created directory '/c/Users/yanhy/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/yanhy/.ssh/id_rsa
Your public key has been saved in /c/Users/yanhy/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:Azz7TBk7gdAryVKkxA52EkxLWqAsliJKsVr5n6Q83UY yan-dream@qq.com
The key's randomart image is:
+---[RSA 3072]----+
|+B+.o.           |
|***+.o..         |
|BX*o .=.o        |
|O.o.+ .+ =       |
|o  ...o E        |
|   . = B o       |
|    + + =        |
|     . .         |
|                 |
+----[SHA256]-----+
```

如果一切顺利的话，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件，这两个就是SSH Key的秘钥对，`id_rsa`是私钥，不能泄露出去，`id_rsa.pub`是公钥，可以放心地告诉任何人。

如果用的是==新系统==，可以用这个命令`$ ssh-keygen -t ed25519 -C "your_email@example.com"`。



2. 在GitHub中添加密钥

<img src="E:\笔记\Git学习\Git.assets\捕获.JPG" align="left" style="zoom:80%;" />

其中title就是给这个密钥取个名字，随便什么都可以。

key输入C:\Users\yanhy\.ssh\id_rsa.pub这个文件的内容。

<img src='.\Git.assets\捕获1.JPG' align='left' style=' width:600px;height:300px'/>



3. 在GitHub中建一个仓库。

   <img src="E:\笔记\Git学习\Git.assets\捕获2.JPG" align="left"/>

   

4. 根据提示绑定远程仓库

<img src="E:\笔记\Git学习\Git.assets\捕获3.JPG" align="left" style="zoom:80%;" />

==https协议绑定：==

```bash
$ git remote add GitHub https://github.com/letheyan/learn_git.git
```

https协议首次推送会弹出一个对话框，需要输入账号和密码。

==SSH协议绑定：==

```bash
$ git remote add GitHub git@github.com:letheyan/learn_git.git
```

推荐用该协议，速度快。当你第一次使用Git的`clone`或者`push`命令连接GitHub时，会得到一个警告：

```
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
```

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入`yes`回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

```
Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
```

这个警告只会出现一次，后面的操作就不会有任何警告了。

如果实在担心有人冒充GitHub服务器，输入`yes`前可以对照[GitHub的RSA Key的指纹信息](https://help.github.com/articles/what-are-github-s-ssh-key-fingerprints/)是否与SSH连接给出的一致。



☆ `git remote add`后面的`GitHub`为本地关联到远程的一个名字，可以随便取，默认为 origin。



## 6.2 推送到远程仓库

```bash
$ git push -u GitHub master
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数（up-to-date），Git不但会把本地的`master`分支内容推送到远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。**之后就可以不用加`-u`参数了。**

命令格式如下：

```bash
git push <远程仓库名> <本地分支名>:<远程分支名>
```

如果本地分支名与远程分支名相同，则可以省略冒号：

```bash
git push <远程仓库名> <本地分支名>
```
即 `$ git push origin master` 相等于 `$ git push origin master:master`。

如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`拉取更新试图合并后，再推送；



## 6.3 查看/删除远程仓库

查看：

```bash
$ git remote -v
GitHub  https://github.com/letheyan/learn_git.git (fetch)
GitHub  https://github.com/letheyan/learn_git.git (push)
```

然后，根据名字删除，比如删除`GitHub`：

```bash
$ git remote rm GitHub
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

## 6.4 克隆远程仓库

1. 先cd到需要放置克隆仓库的目录中

   ```bash
   $ cd /F/git-clone
   ```

2. 在GitHub中获取克隆地址

   <img src="E:\笔记\Git学习\Git.assets\捕获-1621390322428.JPG" align="left"/>

3. 克隆

   `$ git clone git@github.com:letheyan/Python-100-Days.git`

   等待下载完成。

## 6.5 fetch/pull远程分支

1. fetch使用

    ```bash
    $ git fetch origin dev:local       # 从origin仓库的dev分支获取，下载到本地的local分支。
    From gitee.com:lethe00/git-learn
     * [new branch]      dev        -> local
    ```

    语法：`git fetch 远程仓库 远程分支名:本地分支名`，

    注：如果是首次fetch，没有本地分支，则会自动创建，然后下载文件到本地。

    如果不是首次，则`:`后面不用再加本地分支名，如下：

    ```bash
    $ git fetch origin dev               # 从origin仓库的dev分支拉取到当前分支。
    remote: Enumerating objects: 5, done.
    remote: Counting objects: 100% (5/5), done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
    Unpacking objects: 100% (3/3), 292 bytes | 3.00 KiB/s, done.
    From gitee.com:lethe00/git-learn
     * branch            dev        -> FETCH_HEAD
       f275a2a..46f4079  dev        -> origin/dev
    ```

    ==此时本地文件不会改变，需再使用merge合并：==

    ```bash
    $ git merge origin/dev            # 把fetch过来的远程分支和当前分支进行合并。
    Updating f275a2a..46f4079
    Fast-forward
     test.txt | 3 ++-
     1 file changed, 2 insertions(+), 1 deletion(-)
    ```

2. pull使用

   ```bash
   $ git pull origin dev             # 从origin仓库的dev分支拉取到当前分支，并自动进行更新。
   From gitee.com:lethe00/git-learn
    * branch            dev        -> FETCH_HEAD
   Updating e9d287b..46f4079
   Fast-forward
    test.txt | 4 +++-
    1 file changed, 3 insertions(+), 1 deletion(-)
   ```

   注：`git pull --all` `git fetch --all`拉取所有分支。 

# 7、分支

## 7.1 简单使用

1. 查看本地分支

   ```bash
   $ git branch
   * master
   ```
	当前分支前面会标一个*号。
	
	* 查看远程分支：
	
	```bash
	$ git branch -r
	  origin/HEAD -> origin/master    # 已经同步的分支
	  origin/dev
	  origin/master
	```
	
	* 查看本地和远程分支：
	
	```bash
	$ git branch -a
	  local
	* master
	  remotes/origin/HEAD -> origin/master
	  remotes/origin/dev
	  remotes/origin/master
	```
	
	
	
2. 创建/切换分支

   ```bash
   $ git checkout -b dev           # 创建了一个dev分支。
   Switched to a new branch 'dev'
   ```

   ```bash
   $ git branch
   * dev
     master
   ```

   `checkout -b`会创建并切换到`dev`分支。相当于：

   ```bash
   $ git branch dev            # 创建分支
   $ git checkout dev			# 切换分支
   Switched to branch 'dev
   ```

3. 合并分支

   先切换到master分支：

   `$ git checkout master`

   再把dev分支合并到master分支：

   ```bash
   $ git merge dev
   Updating ecfbe11..a05f140
   Fast-forward
    learn/readme.txt | 3 ++-
    1 file changed, 2 insertions(+), 1 deletion(-)
   ```

   `git merge`命令用于合并指定分支到当前分支。合并后，再查看`readme.txt`的内容，就可以看到它和`dev`分支的最新提交是完全一样的。

   注意到上面的`Fast-forward`信息，Git告诉我们，这次合并是“快进模式”，也就是直接把`master`指向`dev`的当前提交，所以合并速度非常快。

   合并图例为：

   <img src="E:\笔记\Git学习\Git.assets\捕获-1621402628992.JPG" align="left" />

   

4. 删除分支

   合并完成后，就可以放心地删除`dev`分支了：

   ```bash
   $ git branch -d dev
   Deleted branch dev (was a05f140).
   ```
	有些分支还没合并会提示无法删除，需要 -D 参数，即 `git branch -D 分支名`。
	
	* 删除远程分支`git push 远程仓库 --delete [branchname]`

---
注：最新版本的Git提供了新的`git switch`命令来切换分支：

==创建==并切换到新的`dev`分支，可以使用：

```bash
$ git switch -c dev
```

直接==切换==到已有的`master`分支，可以使用：

```bash
$ git switch master
```
使用新的`git switch`命令，比`git checkout`要更容易理解。

---



## 7.2 合并冲突问题解决

如果分支合并有冲突，无法合并成功,必须手动解决冲突后再提交。

```bash
$ git merge feature1
Auto-merging readme.txt
CONFLICT (content): Merge conflict in readme.txt
Automatic merge failed; fix conflicts and then commit the result.
```

`git status`可以查看冲突的文件，可以直接查看readme.txt的内容，进行手动修改:

```bash
Git is a distributed version control system.
Git is free software distributed under the GPL.
Git has a mutable index called stage.
Git tracks changes of files.
<<<<<<< HEAD
Creating a new branch is quick & simple.
=======
Creating a new branch is quick AND simple.
>>>>>>> feature1
```

Git用`<<<<<<<`，`=======`，`>>>>>>>`标记出不同分支的内容。修改后，再进行提交：

```bash
$ git add readme.txt 
$ git commit -m "conflict fixed"
[master cf810e4] conflict fixed
```



用带参数`--graph`的`git log`也可以看到分支的合并情况：

```bash
$ git log --graph --pretty=oneline --abbrev-commit
*   0244cb7 (HEAD -> master) conflict fixed
|\
| * e8527f7 (feature1) And simple
* | 0a196e4 & simple
|/
* a05f140 (dev) branch test
* ecfbe11 (GitHub/master) add test.txt
* 522a8f7 git tracks changes
* 234a684 understand how stage works
* cbe42ab append GPL
* f7af7c4 add distributed
* 797b069 wrote a readme file
* 5c5bf41 wrote a readme file
```

合并成功后，删除分支：

```bash
$ git branch -d feature1
Deleted branch feature1 (was e8527f7).
```

## 7.3 保留分支信息的合并

通常，合并分支时，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

```bash
yanhy@DESKTOP-24C35NP MINGW64 /f/git-store/learn (master)  # 当前在master分支中

$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 learn/readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

查看分支历史为：

```bash
$ git log --graph --pretty=oneline --abbrev-commit
*   ab30094 (HEAD -> master) merge with no-ff
|\
| * 1bcf08c (dev) add merage
|/
*   0244cb7 conflict fixed
…………
```

图例为：

<img src="E:\笔记\Git学习\Git.assets\捕获-1621402328490.JPG" align="left"/>

注：`git rebase 分支名/节点哈希值`也可用来合并，且只有一条记录线。



## 7.4 临时保存

不想add提交当前快照，但需要临时保存，使在以后可以用来恢复当前的文件状态。

```bash
$ git stash
Saved working directory and index state WIP on dev: 1bcf08c add merage
```

此时文件会变成最新一次add的状态，编辑的内容会消失，但可以用下面的命令进行恢复。

1. `git stash [save message]`
    保存，`save`为可选项，`message`为本次保存的注释。

2. `git stash list`
    所有保存的记录列表

3. `git stash pop stash@{num}`  ☆☆☆
    恢复，`num`是可选项，通过`git stash list`可查看具体值。**只能恢复一次**,恢复的同时把stash内容也删了。

    如果恢复最新一次的保存，可直接使用`git stash pop`。

4. `git stash apply stash@{num}`
    恢复，`num`是可选项，通过`git stash list`可查看具体值。**可恢复多次**。

5. `git stash drop stash@{num}`
    删除某个保存，`num`是可选项，通过`git stash list`可查看具体值

6. `git stash clear`
    删除所有保存

## 7.5 bug分支合并

​	如果有两个分支 master 和 dev，它们都有同样的一个bug。

1. 先从 master 分一支 issue-101 分支来进行bug修复。

    ```bash
    $ git checkout master
    Switched to branch 'master'
    Your branch is ahead of 'origin/master' by 6 commits.
      (use "git push" to publish your local commits)

    $ git checkout -b issue-101
    Switched to a new branch 'issue-101'
    ```

2. 在分支 issue-101 修复问题后，进行提交：

   ```bash
   $ git add readme.txt 
   $ git commit -m "fix bug 101"
   [issue-101 91ee39b] fix bug 101
    1 file changed, 1 insertion(+), 1 deletion(-)
   ```


3. 把分支 issue-101 与 master 进行合并，并删除分支issue-101：

   ```bash
   $ git switch master
   Switched to branch 'master'
   Your branch is ahead of 'origin/master' by 6 commits.
     (use "git push" to publish your local commits)
   
   $ git merge --no-ff -m "merged bug fix 101" issue-101
   Merge made by the 'recursive' strategy.
    readme.txt | 2 +-
    1 file changed, 1 insertion(+), 1 deletion(-)
    
   $ git branch -d issue-101
   Deleted branch issue-101 (was 91ee39b).
   ```

4. 再修复 dev 分支相同bug

   ```bash
   $ git cherry-pick 91ee39b      # 91ee39b为第二步commit时的版本号。
   [dev 11a0f04] fix bug 101
    Date: Wed May 19 14:48:06 2021 +0800
    1 file changed, 1 insertion(+), 1 deletion(-)
   ```

   Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支

## 7.6 关联远程分支

```bash
$ git checkout -b dev origin/dev
```

说明：创建一个本地分支dev，并关联远程仓库origin中的dev分支。

## 7.7 查看本地分支和远程分支的追踪情况

```bash
$ git remote show gitee                          # 显示本地与远程gitee仓库的情况。
* remote gitee
  Fetch URL: git@gitee.com:lethe00/git-learn.git
  Push  URL: git@gitee.com:lethe00/git-learn.git
  HEAD branch: master
  Remote branches:
    dev                    tracked
    master                 tracked
    refs/remotes/gitee/tra stale (use 'git remote prune' to remove)  # 远程分支已被删除的分支，
  Local refs configured for 'git push':
    dev    pushes to dev    (fast-forwardable)
    master pushes to master (up to date)
```

远程分支已被删除的分支，根据提示可以使用 git remote prune 来同步删除这些分支，如下：

```bash
$ git remote prune gitee
Pruning gitee
URL: git@gitee.com:lethe00/git-learn.git
 * [pruned] gitee/tra
```

# 8、标签管理

1. 创建标签 git tag <name>

   ```bash
   $ git tag v1.0
   ```
   命令`git tag <tagname>`用于新建一个标签，默认为HEAD，也可以指定一个 commit id，例如：
   
   ```bash
   $ git tag v0.9 f52c633
   ```
   
   
   
2. 查看标签

   ```bash
   $ git tag
   V1.0
   ```

3. 标签不是按时间顺序列出，而是按字母排序的。可以用`git show <tagname>`查看标签信息

   ```bash
   $ git show V0.9
   commit cbaeb66b0a712c4e86c7092269a0d215ac70db90 (tag: V0.9)
   Author: lethe <yan-dream@qq.com>
   Date:   Wed May 19 14:39:57 2021 +0800
   
       dev has bug
   
   ………………
   ```

4. 创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：

   ```bash
   $ git tag -a v0.1 -m "第一个版本" 5c5bf41
   ```

   ```bash
   $ git show V0.1
   tag v0.1
   Tagger: lethe <yan-dream@qq.com>
   Date:   Wed May 19 16:16:25 2021 +0800
   
   第一个版本
   
   commit 5c5bf4156865b40986583f9dfc5806a2287a3642 (tag: v0.1)
   Author: lethe <yan-dream@qq.com>
   Date:   Fri May 14 14:10:38 2021 +0800
   ……………………
   ```

1. 推送标签到远程

   a. 推送某个标签 `git push 仓库名 标签名`，例如：`git push origin v1.0`

   b. 推送所有标签 `git push origin --tags`

6. 删除标签

   a. 删除本地标签

    ```bash
    git tag -d <tagname>
    ```
   
   b. 删除远程标签
   
   ```bash
   $ git push origin :refs/tags/v0.9
   To github.com:michaelliao/learngit.git
    - [deleted]         v0.9
   ```

# 9、Git自定义

## 9.1 忽略文件编写

1. 在工作目录下新建一个`.gitignore`文件

   <img src=".\Git.assets\捕获-1621564115229.JPG" align="left" />

2. 编写忽略文件

   * 忽略里面的`.idea`文件夹，则如下：

     <img src=".\Git.assets\捕获-1621564312226.JPG" align="left" />

   * `!`表示不忽略指定的文件/文件夹，如下：

     ` !test.txt`
     
   * 在指定文件夹里不忽略指定的文件，如下

        ```bash
        # 忽略scaffolds文件夹内的所有内容，但不忽略里面的draft.md文件。
        scaffolds/*
        !scaffolds/draft.md
        ```
     注意：这里必须在文件夹后面加上/*，否则是无法实现想要的效果的。

   * `*`表示匹配任意字符；

   * `?`表示匹配一个字符；

   * `[]`表示匹配中括号内的单个字符：
     - 可以使用`-`来表示连贯的字符，比如`0-9`，`a-z`，`A-Z`等，`[0-9]`表示匹配从0到9的单个字符。
     - 可以使用`^`来表示除外，比如`[^0-9]`表示除0到9之外的单个字符。
