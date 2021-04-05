# GitHub入门与实践完整版







## 3 使用GitHub的前期准备

### 3.2 实际动手使用

- 创建仓库

  点击New repository：

  - Repository name
  - Description
  - Public、Private
  - Initialize this repository with a README
    - 打钩的话：随后GitHub会自动初始化仓库并设置README文件，让用户可以立刻clone这个仓库。
    - 如果相向GitHub添加手中已有的Git仓库，建议不要勾选，直接手动push。
  - Add .gitignore
    - 通过它可以在初始化时自动生成.gitignore文件。这个设定会帮我们把不需要在Git仓库中进行版本控制的文件记录在.gitignore文件中，省去了每次根据框架进行设置的麻烦。下拉菜单中包含了主要的语言及框架，选择今后将要使用的即可。
  - Add a license

  点击Create repository，完成仓库的创建。

- 连接仓库

  - README.md
    - README.md在初始化时已经生成好了。README.md文件的内容会自动显示在仓库的首页当中。因此，人们一般会在这个文件中标明本仓库所包含的软件的概要、使用流程、许可协议等信息。如果使用Markdown语法进行描述，还可以添加标记，提高可读性。

  - GitHub Flavored Markdown

  

- 公开代码

  - clone已有仓库

    ```bash
    ## ssh形式
    $ git clone git@github.com:xxxx/Hello-World.git 
    .....
    
    $ cd Hello-World
    ```

    这里会要求输入GitHub上设置的公开密钥的密码。认证成功后，仓库便会被clone至仓库名后的目录中。将想要公开的代码提交至这个仓库再push到GitHub的仓库中，代码便会被公开。

  - 编写代码

  - 提交

  ```bash
  $ git add hello.php
  $ git commit -m "Add hello world script by php"
  ```

  ```bash
  $ git log
  commit d23b909caad5d4.....
  Author:ssj <email.com>
  Date:  ...
  
  	Add hello world script by php
  ```

  - 进行push

  之后只要执行push，GitHub上的仓库就会被更新

  ```bash
  $ git push
  ```

  

## 4 通过实际操作学习Git

### 4.1 基本操作

- git init----初始化仓库

  - 执行了git init命令的目录下就会生成.git目类。这个.git目录里存储着管理当前目录内容所需的仓库数据。

  - 在Git中，称这个目录的内容称为“附属于该仓库的工作树”。文件的编辑等操作在工作树中进行，然后记录到仓库中，以此管理文件的历史快照。如果想把文件恢复到原先的状态，可以从仓库中调取之前的快照，在工作树中打开。开发者可以通过这种方式获取以往的文件。

- git status-----查看仓库的状态

- git add-----向暂存区中添加文件

  - 如果只是用Git仓库的工作树创建了文件，那么该文件并不会被记入Git仓库的版本管理对象。
  - 要想让文件成为Git仓库的管理对象，就需要用git add命令将其加入暂存区(Stage或者Index)中。暂存区是提交之前的一个临时区域。

- git commit-----保存仓库的历史记录

  