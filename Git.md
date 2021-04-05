# Git

## 1 Git简介

### 1.1 Git结构

- 工作区：写代码
- 暂存区：临时存储
- 本地库：历史版本

### 1.2 Git和代码托管中心

- 局域网环境下
  - GitLab服务器
- 外网环境下
  - GitHub
  - 码云

### 1.3 本地库和远程库

- 团队内部协作

  - A将自己本地库的代码push到远程库，B从远程库clone代码到自己的本地库，通过push添加代码(加入团队)。A再pull新的代码

- 外部协作

  - C通过fork A创建的远程库，复制为自己的远程库。再clone到本地，添加代码后,push到自己的远程库。C发起pull request请求，A审核通过后进行merge合并操作。

  


## 2 Git命令行

### 2.1 本地库初始化  

选择目标文件夹，右键git bash

  ```bash
  $ git init
  ```

  > Initialized empty Git repository in C:/Users/Administrator/Desktop/打印文件/studtyGit/.git/

- 查看.git文件

  ```bash
  $ ll .git/
  total 7
  -rw-r--r-- 1 Administrator 197121 130  3月 26 09:43 config
  -rw-r--r-- 1 Administrator 197121  73  3月 26 09:43 description
  -rw-r--r-- 1 Administrator 197121  23  3月 26 09:43 HEAD
  drwxr-xr-x 1 Administrator 197121   0  3月 26 09:43 hooks/
  drwxr-xr-x 1 Administrator 197121   0  3月 26 09:43 info/
  drwxr-xr-x 1 Administrator 197121   0  3月 26 09:43 objects/
  drwxr-xr-x 1 Administrator 197121   0  3月 26 09:43 refs/
  
  ```

  .git目录中存放的是本地库相关的子目录和文件，不要删除，也不要随便修改。

### 2.2 设置签名

- 形式：

  - username:
  - email:

- 作用：区分不同开发人员的身份

- 这里设置的签名和登录远程库(代码托管中心)的账号、密码没有任何关系。

- 

- 命令：

  - 项目级别/仓库级别：仅在当前本地库范围内有效

    - git config user.name shishijia
    - git config user.email 123@qq.com
    - 信息保存的位置：

    ```bash
    $ cat .git/config
    [core]
            repositoryformatversion = 0
            filemode = false
            bare = false
            logallrefupdates = true
            symlinks = false
            ignorecase = true
    [user]
            name = shishijia
            email = 123@qq.com
    
    ```

    

  - 系统用户级别：登录当前操作系统的用户范围

    - git config --global user.name shishijia
    - git config --global user.email 123@qq.com
    - 信息保存位置：cd ~（进入根目录）

    ```bash
    $ cat .gitconfig
    [winUpdater]
            recentlySeenVersion = 2.25.0.windows.1
    [user]
            name = shishijia1120
            email = 3319294576@qq.com
    [http]
            postBuffer = 1624288000
            sslVerify = false
    
    ```

    一般设置系统用户即可。

  - 级别优先级：

    - 就近原则：项目级别优先于系统用户级别，二者都有时采用项目级别的签名
    - 如果只有系统用户级别的签名，就以系统用户级别的签名为准
    - 二者都没有不允许


### 2.3 状态、添加、提交

  ```bash
  $ git status
  On branch master
  
  No commits yet

  nothing to commit (create/copy files and use "git add" to track)
  
  ```

  ```bash
  $ vim good.txt
  
  Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
  $ git status
  On branch master
  
  No commits yet
  
  Untracked files:
    (use "git add <file>..." to include in what will be committed)
          good.txt
  
  ```

nothing added to commit but untracked files present (use "git add" to track)


  ```
  
  
  
  ```bash
  Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
  $ git add good.txt
warning: LF will be replaced by CRLF in good.txt.
  The file will have its original line endings in your working directory
  
  ```

  ```bash
  $ git status
  On branch master
  
  No commits yet
  
  Changes to be committed:
    (use "git rm --cached <file>..." to unstage)
          new file:   good.txt
  
  ```



```bash
$ git rm --cached good.txt
rm 'good.txt'

Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        good.txt

nothing added to commit but untracked files present (use "git add" to track)


```



```bash
$ git commit good.txt
warning: LF will be replaced by CRLF in good.txt.
The file will have its original line endings in your working directory
[master (root-commit) 766a774] My first commit new file good.txt
 1 file changed, 3 insertions(+)
 create mode 100644 good.txt

```



```bash
$ git status
On branch master
nothing to commit, working tree clean
```



修改good.txt

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   good.txt

```



```bash
Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
$ git add good.txt
warning: LF will be replaced by CRLF in good.txt.
The file will have its original line endings in your working directory

Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   good.txt
```



```bash
$ git commit -m "My second commit,modify good.t" good.txt
warning: LF will be replaced by CRLF in good.txt.
The file will have its original line endings in your working directory
[master a688797] My second commit,modify good.t
 1 file changed, 1 insertion(+)

```

- **总结**：新建的文件必须先执行git add命令添加到暂存区，不是新建的文件(即已追踪的文件)可以直接git commit到本地库，但是这样做就不可撤销了。

### 2.4 查看历史记录

- git log

```bash
$ git log
commit a688797d1e6182ce40fcd4f5e82e895a2c011b65 (HEAD -> master)
Author: shishijia <123@qq.com>
Date:   Fri Mar 26 16:00:17 2021 +0800

    My second commit,modify good.t

commit 766a774daeb17e8dfde10215581b74d6dc5652f9
Author: shishijia <123@qq.com>
Date:   Fri Mar 26 10:38:08 2021 +0800

    My first commit new file good.txt

```

- git log --pretty=oneline

```bash
$ git log --pretty=oneline
a688797d1e6182ce40fcd4f5e82e895a2c011b65 (HEAD -> master) My second commit,modify good.t
766a774daeb17e8dfde10215581b74d6dc5652f9 My first commit new file good.txt

```

- git log --oneline

```bash
$ git log --oneline
a688797 (HEAD -> master) My second commit,modify good.t
766a774 My first commit new file good.txt

```

- git reflog

```bash
$ git reflog
a688797 (HEAD -> master) HEAD@{0}: commit: My second commit,modify good.t
766a774 HEAD@{1}: commit (initial): My first commit new file good.txt

```

- 多屏显示控制方式：
  - 空格向下翻页
  - b向上翻页
  - q退出

### 2.5  前进后退

- 本质

- 基于索引值操作

  - git reset --hard [局部索引值]

  ```bash
  $ git reflog
  820884e (HEAD -> master) HEAD@{0}: commit: insert eeee
  3963999 HEAD@{1}: commit: insert dddd
  a688797 HEAD@{2}: commit: My second commit,modify good.t
  766a774 HEAD@{3}: commit (initial): My first commit new file good.txt
  
  ```

  ```bash
  $ git reset --hard 3963999
  HEAD is now at 3963999 insert dddd
  
  Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
  $ cat good.txt
  aaaa
  bbbb
  cccc
  UUUU
  dddd
  
  ```

  - 符号：只能后退使用^（一个后退一步）

  ```bash
  $ git log --oneline
  3963999 (HEAD -> master) insert dddd
  a688797 My second commit,modify good.t
  766a774 My first commit new file good.txt
  
  Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
  $ git reflog
  3963999 (HEAD -> master) HEAD@{0}: reset: moving to 3963999
  820884e HEAD@{1}: commit: insert eeee
  3963999 (HEAD -> master) HEAD@{2}: commit: insert dddd
  a688797 HEAD@{3}: commit: My second commit,modify good.t
  766a774 HEAD@{4}: commit (initial): My first commit new file good.txt
  
  Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
  $ git reset --hard HEAD^
  HEAD is now at a688797 My second commit,modify good.t
  
  Administrator@DESKTOP-OIT6AC6 MINGW64 ~/Desktop/打印文件/studtyGit (master)
  $ cat good.txt
  aaaa
  bbbb
  cccc
  UUUU
  
  ```

  注意：git log --oneline 只显示当前版本之前的版本，不能显示之后的版本。git reflog显示完整版本。

  - 使用~符号：只能后退

    git reset --hard~n

### 2.6  reset命令的三个参数对比

- --soft参数
  - 仅仅在本地移动HEAD指针
- --mixed参数
  - 在本地库移动HEAD指针
  - 重置暂存区
- --hard参数
  - 在本地库移动HEAD指针
  - 重置暂存区
  - 重置工作区

### 2.7 删除文件并找回

- 前提：删除前，文件存在时的状态提交到了本地库
- 操作：git reset --hard [指针位置]
  - 删除操作已经提交到本地库：指针位置指向历史记录
  - 删除操作尚未提交到本地库：指针位置使用HEAD



### 2.8 比较文件

- git diff [文件名]
  - 将工作区中的文件和暂存区进行比较
- git diff [本地库中历史版本] [文件名]
  - 将工作区中的文件和本地库历史记录比较一下
- 不带文件名比较多个文件

 

### 2.9 分支

- 分支：在版本控制过程中，使用多条线同时推进多个任务

- 分支的好处

  - 同时并行推进多个功能开发，提高效率
  - 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响，失败的分支删除重新开始即可。

- 创建分支

  git branch [分支名]

- 查看分支

  git branch -v

- 切换分支

  git checkout [分支名]

- 合并分支

  - 第一步：切换到接受修改的分支(被合并，增加新内容)上

    git checkout [被合并分支名]

  - 第二步：执行merge命令

    git merge [有新内容分支名]

- 解决冲突

  - 冲突的表现

- 冲突的解决

  - 第一步：编辑文件，删除特殊符号
  - 第二步：把文件修改到满意的程度，保存退出
  - 第三步：git add [文件名]
  - 第四步：git commit -m "日志信息"
    - 注意：此时commit一定不能带具体文件名



## 3 Git基本原理

### 3.1 哈希

- 

### 3.2 Git保存版本的机制

#### 3.2.1 SVN

#### 3.2.2 Git的文件管理机制

Git把数据看作是小型文件系统的一组快照。每次提交更新时Git都会对当前的全部文件制作一个快照并保存到这个快照的索引。为了高效，如果文件没有修改，Git不再重新存储该文件，而是只保留一个链接指向之前存储的文件。所以Git的工作方式可以称之为快照流。

- Git的提交对象

98ca9

```bash
commit size
	tree 92ec2
 author ssj
 committer ssj
The initial of my project 
```

92ec2

```bash
tree size
blob 5b1d3 README
blob 911e7 LICENSE
blob cba0a test.rb
```

5b1d3

```bash
blob size
xxxxx
```

911e7

```bash
blob size
xxxxx
```

cba0a

```bash
blob size
xxxx
```

- 提交对象及其父对象形成的链条

98ca9 (Snapshot A)

```bash
commit size
tree 92ec2
parent 
author ssj
committer ssj
```

34ac2 (Snapshot B)

```bash
commit size
tree 184ca
parent 98ca9
author ssj
committer ssj
```

f38ab (Snapshot C)

```
commit size
tree 0de24
parent 34ac2
author ssj
committer ssj
```

### 3.3 Git分支管理机制

？？？？



## 4 GitHub

### 4.1 GitHub账号创建

htttps://github.com

### 4.2 创建远程库

- Create Repository
  - Owner
  - Repository name
  - Description
  - Public / Private
  - Initialize this repository with a README:?????
  - Add .gitignore:None
  - Add a license:None

### 4.3 在本地创建远程库地址别名

```bash
$ git remote add origin https://github.com/atguigu2018ybuq/huashan.git
```

### 4.4 推送操作

```bash
$ git push origin master
```

### 4.5 克隆操作

```
$ git clone https://github.com/atguigu2018ybuq/huashan.git
```

clone后包含.git目类，即不需要对本地进行初始化，已初始化完毕。

- 完整的把远程库下载到本地
- 创建origin远程地址别名
- 初始化本地库

### 4.6 邀请成员加入团队



###  

### 4.7  远程库修改的拉取

- pull = fetch+merge
- git fetch [远程库地址别命] [远程分支名]
- git merge [远程库地址别名/远程分支名]
- git pull  [远程库地址别命] [远程分支名]
- 操作比较简单时，可直接使用pull

### 4.8 协同开发时冲突的解决

- 重点
  - 如果不是基于GitHub远程库的最新版所作的修改，不能推送，必须先拉取。
  - 拉取下来， 解决冲突的办法参照分支解决冲突的方法。

### 4.9 跨团队协作操作演示



### 4. 10 SSH免密演示

```bash
$ cd ~
//$ rm -rvf .ssh
$ ssh-keygen -t rsa -C email

$ cd .ssh
$ ls -lf
$cat id_rsa.pub
//复制到github的SSH and GPG keys 中的SSH Keys
```



## 5 Git工作流

### 5.1 分类

#### 5.1.1 集中式工作流








