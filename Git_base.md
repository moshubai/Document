# 常用 Git 命令使用指南
Git 作为一种广泛应用的版本管理和协同开发工具，已在各种开发团队中广泛展开，其中针对某些大型协作项目，已经有很多分支及 commit 管理的工具涌现，这里，我们只针对 Git 的一些原生操作进行规范和讨论。

以下示例代码以本项目 __git@github.com:moshubai/documents.git__ 的 github 地址为举例对象。Windows 平台如果安装 Git Bash 之类的 shell，所需执行的命令行基本上和其他平台都一样。

## 初始化你的 Git
刚安装完 Git 工具后，需要进行一些简单的配置，之后我们才能愉快的玩耍。在终端窗口做一个全局配置：
```
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR@EMAIL.ADDRESS"
```
配置之后，以后你的每一次 commit 都会添加这些信息，可供自己或者别人查询这些 commit 的作者。这两行命令行执行后，将会在 Git 的本账户的全局配置文件，就是 `~/.gitconfig` 中添加如下内容：
```
[user]
  name = YOUR NAME
  email = YOUR@EMAIL.ADDRESS
```
所以说，你也可以不选择执行这两个命令，直接按以上格式编辑该文件，效果也是一样。

Git 的配置文件中，其所配置的选项其实是层叠的，在每个项目中，都有一个隐藏目录，在那里的配置文件中，相同模块内容将优先于 `.gitconfig`。例如，我要在某个项目中换一个名字和工作邮件，编辑该项目下 `.git/config` 文件，将所对应的模块内容写进去，那在这个项目中，commit 后面跟的名字和邮件将会更新。

## 秘钥的配置和使用
我们会以 ssh 方式与远程的仓库进行通讯，为避免每次都要输入密码，我们会在本机通过 RSA 算法生成秘钥，把公共秘钥交给远程机器，这样我们就能通过秘钥匹配来自动认证了。

在获取秘钥前，先去看看你有没有 `~/.ssh` 目录，没有的话，那应该就还没有生成过秘钥，那我们开始工作（有 `$` 符号提示的地方是我们要输入的命令）：

⑴ 生成秘钥，采用 RSA 算法，长度为 4096 字节，也可以用 1024 或者 2048 的参数，越长越安全嘛。
```
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
此时，终端会提示 `Generating public/private rsa key pair.`，秘钥的长度越长，生成时间也越久。

⑵ 生成完后，终端再次提示以下内容，采用默认的路径和文件存放你的秘钥就好，直接回车确认。
```
Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
```

⑶ 此处需要你输入一个“安全掩码”(secure passphrase)，这个你随意输，貌似需要 4 位以上。为什么需要这个掩码呢，有兴趣可以延伸阅读一下这篇文章：["Working with SSH key passphrases"](https://help.github.com/articles/working-with-ssh-key-passphrases)。当然，你也可以什么都不输入，直接回车。
```
Enter passphrase (empty for no passphrase): [Type a passphrase]
Enter same passphrase again: [Type passphrase again]
```

⑷ 此时应该会有一串称之为 "key fingerprint" 的字符串显示出来，说明秘钥已成功是生成。接下来将这个新鲜出炉的秘钥添加到 `ssh-agent` 之中，先开启它：
```
$ eval "$(ssh-agent -s)"
```
执行成功后，会有类似 `Agent pid 59566` 的结果返回。

⑸ 添加秘钥：
```
$ ssh-add ~/.ssh/id_rsa
```

⑹ 好了，现在你可以执行以下命令将公共秘钥复制到剪贴板里：
```
# Mac Terminal
$ pbcopy < ~/.ssh/id_rsa.pub
```
```
# Windows Git Bash
$ clip < ~/.ssh/id_rsa.pub
```

⑺ 可以与远程仓库地址做一下通讯测试，看看秘钥是否生效了，下面是与 github 服务器通讯的示例：
```
ssh -T git@github.com
```
如返回 success 信息说明设置成功了。基本工作都好了，这下可以开工了！

## 从远程拉取现有项目到本地
在本地项目目录里执行如下命令：
```
git clone git@github.com:moshubai/documents.git
```
默认状况下，项目目录中会出现一个 `documents` 目录，如要指定自己的目录，可以这么写：
```
git clone git@github.com:moshubai/documents.git mydoc
```
这样，项目代码将会 clone 到 `mydoc` 目录下。

## 将本地项目添加到远程初始化的空仓库中
有很多时候，我们会事先在本地搭建好项目的初始化代码，这时想要推送到远程仓库，比如 Github 或者 Bitbucket 等，需要执行如下的一系列命令：

⑴ 初始化本地项目，完成后 Git 会在本地项目的目录中新建一个隐藏目录 `.git` 作为其仓库
```
git init
```

⑵ 将本地文件全部迁入：
```
git add --all
git commit -m 'your commit message'
```

⑶ 添加远程目标仓库，并将本地源码推送出去
```
git remote add origin git@github.com:moshubai/documents.git
git push -u origin master
```

## 分支操作
git 工具一个迷人的地方就是可以快速的操作和切换分支，合理利用其分支功能，可以使得协同开发模式如鱼得水。

⑴ 基本分支操作
```
# 获取本地分支
git branch
```
```
# 获取本地和远程所有分支
git branch --all
```
```
# 删除本地分支
git branch -d branch_name
```
```
# 删除远程分支（注意该命令的特殊性，分支名称前需要加冒号）
git push origin :branch_name
```
```
# 分支切换
git checkout branch_name
```

⑵ 创建新分支
```
# 通常做法
git branch new_branch_name
git checkout new_branch_name
```
```
# 一步到位的命令，推荐！
git checkout -b new_branch_name
```

⑶ 获取远程的其他分支。通常我们 clone 下一个项目后，本地只有一个 master 分支，如何才能使用远程上的其他开发分支呢？
```
git checkout -t origin remote_branch_name
```

## 整理合并你的 commit 后再推送到主分支
通常情况下，我们规定不要在 master 分支上直接做开发，而是会根据项目需求在其他开发分支进行操作。当我们完成一个功能后，需要将代码合并到 master 分支，如无冲突，我们用 `git merge` 命令能很轻松的进行这个分支的合并。但实际情况是，我们如果用 `git log` 命令查看 master 分支的 commit 时会发现，可能有太多的开发分支上的 commit 也一起合并了进来。其实对于 master 分支来讲，我们不太关注某个具体开发细节的 commit，那是开发分支自己的事情。如何避免该问题，我们需要一些技巧，让这些开发 commit 在 master 分支上合并成一个。

⑴ 按照常规完成合并操作
```
# 目前在 master 分支上
git merge dev_branch
```

⑵ 如无冲突，进行下一步关键操作
```
git reset origin/master
```

⑶ 通过与远程分支的自动比对，新的或修改过的文件重新变成未迁入状态，此时重新进行迁入工作
```
git add --all
git commit 'dev_branch: some module finished.'
```

通过以上的操作，我们就能在 master 分支上很清晰的看到各个开发分支合并的状况。

## 优化你的 git 命令
使用命令行方式可以快速有效的操作 git 工具，但其实经过配置，我们还能更快速地操作这些命令，我们可以将以下代码复制到你 git 工具的配置文件中，就是那个 `~/.gitconfig`：
```
[alias]
  co = checkout
  st = status
  ci = commit
  br = branch
```
配置完成后，我们就可以执行 `git st` 来代替 `git status`，`git ci` 代替 `git commit` 了。

如果是同一台电脑切换不同的git账户，请点击 [《同一台电脑Git的多账户切换》](Git_Account_switch.md)。
