# 搭建git 服务器仓库

## 参考资料

> [在阿里云上搭建自己的git服务器 - 西北逍遥 - 博客园](<https://www.cnblogs.com/herd/p/7063091.html>)

**1. 安装git**

首先安装git，一般而言，现在的服务器已经内置了git安装包，我们只需要执行简单的安装命令即可安装。比如：

```
$ yum install git # centos
$ apt-get install git # ubuntu
```

上面是直接用root登陆服务器进行操作，也是为了演示方便。

git和mysql不一样，mysql在安装时，得安装mysql-server，即mysql服务器，git是分布式的，每一个安装了git的电脑，既是客户端，也是服务器，git与git之间可以相互通信，而我们所谓的git服务器，实际上和我们自己的电脑没有什么本质上的差别。但是，我们为了更有效的管理项目，都采取中心化的管理方式，因此创建一个“git服务器”，作为其他所有人提交代码的最终终端。

**2.创建git用户及权限**

我们当然不允许直接使用root来进行通信交互了，所以，我们创建一个git用户来作为今后提交代码的用户。

```
$ adduser git
```

执行这条命令之后，你发现在/home目录下多了一个git目录，按理来说，现在，你的系统中多了这个git用户，并且家目录在/home/git。但是，我们并不希望这个用户通过ssh连接到服务器上面去，所以，我们要禁止这个用户使用ssh连接上去进行操作。我们通过编辑一个权限文件来处理：

```
$ vi /etc/passwd
```

找到类似于

```
git:x:1001:1001:,,,:/home/git:/bin/bash
```

这样的行，你看到那个末尾的/bin/bash，就是允许ssh连接操作的权限，我们把它改为/user/bin/git-shell，结果如下：

```
git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
```

这样处理好，git就不能ssh连上去了（实际上是可以的，只不过会闪退）。

我们还得给git分配一个密码，执行：

```
$ passwd git 123456(你的密码)
```

这个密码用在你后面提交代码的时候使用。

**3.公钥(这一个步骤不是必须的，其目的为了免密码提交)**

这个是git里面比较特殊的一步操作，通信的时候，客户端与服务器需要一个证书进行验证。操作方法很简单，首先在你自己的电脑上（ubuntu）生成自己的一个公钥：

```
$ cd ~
$ ssh-keygen -t rsa
```

这时你自己电脑上就有一个公钥了，但是在哪里呢？在.ssh目录下，.开头的文件夹都是隐藏的，但是可以cd进去。

```
$ cd .ssh
$ vi id_rsa.pub
```

这样就能看到你的公钥了，把所有的内容复制下来。接下来，我们去回服务器上面操作。

```
$ cd /home/git/
$ mkdir .ssh
$ cd .ssh
$ vi authorized_keys
```

如果是裸机，服务器上面/home/git目录下应该没有.ssh目录，所以我们自己创建，打开（自动创建）authorized_keys之后，把刚才复制下来的公钥黏贴进去，ok了，保存退出。

使用证书，主要是为了无需密码就可以提交代码，具体请看《[使用SSH证书远程登陆你的服务器](http://www.tangshuang.net/1697.html)》。

**4.初始化一个git仓库**

我习惯把这类东西丢到/var下去，所以，我们在/var下面创建一个git目录

```
$ cd /var
$ mkdir git
$ chown -R git:git git
$chmod 777 git
$ cd git
```

接下来，我们用git命令初始化一个仓库：

```
$ git init --bare arepoforyourproject.git
```

初始化完成之后，这个空的仓库就OK了。

这里有一个细节，就是.git目录必须要有可读写权限，因为当我们在push的时候，是使用git用户推送到服务器上面去，会有一个写入的过程，如果不赋予可写权限，push就会失败。

**千万要注意将当前查看当前文件的权限以及所属是否为git用户，否则会报类似下面的错误**

```bash
Counting objects: 142, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (130/130), done.
remote: fatal: Unable to create temporary file '/var/git/lnavs.git/./objects/pack/tmp_pack_XXXXXX': Permission denied
error: pack-objects died of signal 13
error: failed to push some refs to 'git@www.ineweyer.cn:/var/git/lnavs.git'
```

**5.克隆试试**

回到你本地的电脑上，我们通过克隆来试试仓库是否可以使用：

```
$ git clone git@10.0.0.121:/var/git/arepoforyourproject.git
```

然后会提示你输入git的密码，输入进去，然后会再提示你克隆了一个空白的版本库。这说明服务器已经OK了。

**6.多用户和权限管理**

如果团队很小，把每个人的公钥收集起来放到服务器的`/home/git/.ssh/authorized_keys`文件里就是可行的。如果团队有几百号人，就没法这么玩了，这时，可以用[Gitosis](https://github.com/res0nat0r/gitosis)来管理公钥。