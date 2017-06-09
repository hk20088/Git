# Git学习笔记

## 安装Git
Windows下要使用很多Linux/Unix的工具时，需要Cygwin这样的模拟环境，Git也一样。不过，有高人已经把模拟环境和Git都打包好了，名叫msysgit，
只需要下载一个单独的exe安装程序安装即可。

msysgit是Windows版的Git，从 https://git-for-windows.github.io 下载，然后按默认选项安装即可。

安装完成后，在开始菜单里找到“Git Bash”，蹦出一个类似命令行窗口的东西，就说明Git安装成功！

安装完成后，还需要最后一步设置，在命令行输入以下命令设置用户和邮箱：
```
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
注意：`git config`命令的`--global`参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，当然也可以对某个仓库指定不同的用户名和Email地址。

## 在本地创建版本库
版本库又名仓库，英文名repository，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”。

所以，创建一个版本库非常简单：
- 第一步，选择一个合适的地方，创建一个空目录，比如 *gitlearn*（如果是windows系统，为了避免遇到各种莫名其妙的问题，最好使用纯英文目录  
- 第二步，使用`git brash`切换到刚创建的目录，再使用命令`git init`把这个目录变成Git可以管理的仓库  
- 第三步，目录下多了一个`.git`目录，版本库创建完成（注意`.git`是隐藏目录，不要随意修改）  

然后，把文件添加到版本库里：
- 第一步，把文件（例如文件名为 *readme.txt*）放到 *gitlearn* 目录下（或者子目录下）   
- 第二步，执行命令`git add`，告诉Git，将要把文件添加到仓库   
```
$ git add readme.txt
```
- 第三步，执行命令`git commit`，把文件提交到仓库
```
$ git commit -m "create a file readme.txt"
```
注意：
- 参数 `-m`后面跟的是注释，这个可以方便查日志时找到修改记录。不要嫌麻烦，养成写注释的好习惯
- 可以 add 多个文件，一次性 commit

## 版本管理
```
$ git status    #查看工作区状态
$ git diff      #对比修改的东西
$ git log       #查看提交历史
$ git reflog    #查看命令历史
$ git reset --hard HEAD^   #查看上一个版本
$ git reset --hard [commitid]   #回退到任何一个版本，其中commitid可以通过 git reflog 查看
$ git rm [file] #删除版本库的文件，并用 git commit 提交
$ git checkout -- [file]   #用版本库的文件替换工作区的文件
```

注意：
- `git add`命令是把文件从工作区放到暂存区（Stage）
- `git commit`命令是把文件从暂存区提交到分支（master是创建版本库时，Git自动创建的主分支）

如果修改了内容，想要撤消，分以下几种：
- 场景1：修改了工作区文件内容，还没有 `git add`，使用命令 `$ git checkout -- [file]` 将版本库的最新文件替换工作区的文件，相当于撤消工作区的修改。 
- 场景2：修改了文件内容，并且使用命令 `git add` 添加到暂存区，分两步撤消。 第一使用命令 `$ git reset HEAD [file]` 回到场景1，第二步按场景1操作  
- 场景3：修改了文件内容，并且使用命令 `git commit` 提交到版本库，使用版本回退，退到以前的版本即可  
- 场景4：修改了文件内容，并且已经提交，并推送到远程库，那就没办法了...  

## 远程仓库
GitHub可以提供Git托管服务，但是本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：

首先，在本地创建SSH Key。在用户主目录下，查看是否已经存在 `.ssh` 目录，如果有，再看看这个目录下是否有 `id_rsa`和 `id_rsa.pub`这两个文件，如果已经有了，说明 SSH Key已经创建。如果没有，打开shell（Windows下打开 Git Bash），创建SSH Key：
```
$ ssh-keygen -t rsa -C "email@example.com"
```
邮件地址换成自己的邮件地址，然后一路回车，使用默认值即可。 最后，即可以主目录找到 `.ssh`目录  
第二步，登录 GitHub，点击 `Setting -> SSH and GPG keys -> New SSH key` 添加公钥（将id_rsa.pub文件的内容复制过来即可，Title 可随意填写）  

为什么GitHub需要SSH Key呢？因为GitHub需要识别出你推送的提交确实是你推送的，而不是别人冒充的，而Git支持SSH协议，所以，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送。

当然，GitHub允许你添加多个Key。假定你有若干电脑，你一会儿在公司提交，一会儿在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

在GitHub上免费托管的Git仓库，任何人都可以看到（但只有你自己才能改）。所以，不要把敏感信息放进去。

如果你不想让别人看到Git库，有两个办法，一个是交点保护费，让GitHub把公开的仓库变成私有的，这样别人就看不见了（不可读更不可写）。另一个办法是自己动手，搭一个私人Git服务器。


### 添加远程库

在GitHub上通过`New Repository`创建一个远程库(**例如创建的库名是：gitlearn**)。（*如果在GitHub创建仓库时，顺带创建了README.md文件，需要先把 README.md文件克隆到本地，这样才能交本地提交后的文件push到GitHub的仓库里*）

远程库创建完成后，需要将本地库和远程库作关联，在本地库下运行命令：
```
$ git remote add origin git@github.com:hk20088/gitlearn.git  #关联本地库和远程库（ssh协议）
$ git remote add origin https://github.com/hk20088/gitlearn.git  #关联本地库和远程库（https协议）
$ git push -u origin master          #将本地库推送到远程库
$ git remote -v                      #查看远程库信息
$ git remote remove [name]           #删除远程库
```

注意：
- 添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的。  
- 使用`git push`命令将内容推送到远程，实际上是把 mster 分支推送到远程库。使用 `-u`参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。  

### SSH警告

当第一次使用Git的clone或者push命令连接GitHub时，会得到一个警告：
```
The authenticity of host 'github.com (xx.xx.xx.xx)' can't be established.
RSA key fingerprint is xx.xx.xx.xx.xx.
Are you sure you want to continue connecting (yes/no)?
```

这是因为Git使用SSH连接，而SSH连接在第一次验证GitHub服务器的Key时，需要你确认GitHub的Key的指纹信息是否真的来自GitHub的服务器，输入yes回车即可。

Git会输出一个警告，告诉你已经把GitHub的Key添加到本机的一个信任列表里了：

Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
这个警告只会出现一次，后面的操作就不会有任何警告了。


### 从远程库克隆

如果先有了远程库，需要将远程库的内容下载到本地开发，使用命令：
```
$ git clone git@github.com:hk20088/gitlearn.git
```
注意：  
- Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。  
- 所以，还可以用 https://github.com/hk20088/gitlearn.git 这样的地址进行推送或克隆。  
- 但是，https速度信息论是，而且每次推送都必须输入口令。 不过在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。

## 分支管理
```
$ git checkout -b dev       #创建并切换分支 dev
$ git checkout -b origin/dev  #创建远程分支到本地
$ git branch                #查看分支
$ git branch --set-upstream dev origin/dev    #将本地分支与远程分支建立关联关系
$ git checkout master       #切换分支，从其它要支切换到master 分支
$ git merge dev             #合并分支，将dev分支的内容合并到master分支
$ git merge --no-ff -m "merge with no-ff" dev   #合并分支，--no-ff表示禁用 faster forward 模式。这样，从分支历史上就可以看出分支信息
$ git branch -d dev         #删除分支
$ git branch -D dev         #强制删除还没有合并的分支

$ git push origin dev       #推送分支，将dev分支推送到远程
$ git pull                  #从远程抓取分支
$ git log --graph           #查看分支合并图

$ git stash                 #隐藏工作现场
$ git stash list            #查看隐藏的工作现场列表
$ git stash pop             #恢复工作现场

```

### 分支策略

在实际开发中，我们应该按照几个基本原则进行分支管理：

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；

那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

团队成员每个人都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并就可以了。

所以，团队合作的分支看起来就像这样： 

![branch](/Git/branch.png)  


## 标签管理
```
$ git tab [tab_name]        #打标签
$ git tab [tab_name] [commitId]  #在commitId这个版本下打标签
$ git tag -a v0.1 -m "version 0.1 released" 3628164    #打带说明的标签。-a 指定标签名；-m 指定说明文字
$ git tab         #查看所有标签
$ git show [tab_name]    #查看某个标签的信息
$ git tab -d [tab_name}  #删除标签
$ git push origin [tab_name]    #推送本地标签到远程
$ git push origin --tags        #一次性推送所有标签到远程
$ git push origin :refs/tags/[tab_name]    #删除远程标签
```

## 自定义Git
### 配置别名
```
$ git config --global alias.st status   # 用git st 表示 git status
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```
每个仓库的配置文件都在 `.git/config` 中   

### 搭建Git服务器
搭建Git服务器需要准备一台运行Linux的机器，强烈推荐用Ubuntu或Debian，这样，通过几条简单的apt命令就可以完成安装。

假设你已经有sudo权限的用户账号，下面，正式开始安装。

第一步，安装git：
```
$ sudo apt-get install git
```
第二步，创建一个git用户，用来运行git服务：
```
$ sudo adduser git
```
第三步，创建证书登录：

收集所有需要登录的用户的公钥，就是他们自己的id_rsa.pub文件，把所有公钥导入到/home/git/.ssh/authorized_keys文件里，一行一个。

第四步，初始化Git仓库：

先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
```
$ sudo git init --bare sample.git
```
Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。然后，把owner改为git：
```
$ sudo chown -R git:git sample.git
```
第五步，禁用shell登录：

出于安全考虑，第二步创建的git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下面的一行：

```
git:x:1001:1001:,,,:/home/git:/bin/bash  
改为：  
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell   
```

这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。

第六步，克隆远程仓库：

现在，可以通过git clone命令克隆远程仓库了，在各自的电脑上运行：
```
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.
```

剩下的推送就简单了。

### 管理公钥

如果团队很小，把每个人的公钥收集起来放到服务器的/home/git/.ssh/authorized_keys文件里就是可行的。如果团队人数较多，可以使用 Gitosis 来管理公钥。




