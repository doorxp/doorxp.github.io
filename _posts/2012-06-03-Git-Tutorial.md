---
layout: post
title: "Git Tutorial"
categroy: git
---

Git是一个版本控制系统，linux内核就是由git管理的。同时git是由linux之父设计的，显然在linux下搭建git更能发挥其功效。

以下教程搭建在ubuntu 10.04之上

搭建一个Git服务器一般需要以下软件：OpenSSH Server、Git、Gitosis和Apache2(如果想使用Gitweb的话)
Tips：
1,使用 gitosis 來管理(gitosis 可以設定到 "誰" 可以存取此專案).
2,使用 gitolite 來管理(gitolite 可以設定 "誰" 可以存取此專案, 而且, 可以設定只能存取哪個 branch 等路徑)
本文只使用了gitosis

一，首先，先在服务器上建立一个git用户，让其来管理commit / push，避免权限问题；

bc.. 
    sudo useradd -m git
    sudo passwd git 
p. 
二，安装OpenSSH Server
sudo apt-get install openssh-server
可以对SSH进行配置，主要是修改登录权限
修改ssh服务端配置文件 /etc/ssh/sshd_config
Port 22 # 修改成你想要的登陆端口,如2222
PermitRootLogin no # 禁止root用户登陆
StrictModes yes # 检查密钥的用户和权限是否正确，默认打开的
RSAAuthentication yes # 启用 RSA 认证
PubkeyAuthentication yes # 启用公钥认证
PasswordAuthentication no # 禁止密码认证，默认是打开的
ServerKeyBits 1024 # 修改后变为此状态，将ServerKey强度改为1024比特
PermitEmptyPasswords no # 修改后变为此状态，禁止空密码进行登录

修改完成后，重启ssh服务：
@sudo /etc/init.d/ssh restart@

三，安装Git
@sudo apt-get install git-core@
如果出现依赖包问题，先update和upgrade一下

四,安装gitosis
(1)新建一个文件夹，用来存放下载的gitosis文件，如
@mkdir ~/gitosis_setup@
(2)安装gitosis

bc.. 
cd ~/ gitosis_setup
git clone git://eagain.net/gitosis
cd gitosis
sudo python setup.py install

p. 
如果提示没有找到python-setuptools.py，请到文章底下下载deb安装包。

五，安装gitweb
@sudo apt-get install gitweb@

六，安装apache2
@sudo apt-get install apache2@

七，配置gitweb
（1）默认没有 css 加载，把 gitweb 要用的静态文件连接到 web服务器目录 下：

bc.. 
cd /var/ www/
sudo ln -s / usr/ share/ gitweb/* .

p. 
(注意后面的点)
（2）@sudo vi /etc/ gitweb.conf@
将 $projectroot 改为gitosis-admin.git所在目录： /home/git/repositories
（3）修改 /home/git/repositories权限，默认情况下，gitosis将 repositories权限设置为不可读的
@sudo chmod 777 -R /home/git/repositories@

八，重新启动apache：sudo /etc/init.d/apache2 restart，访问http://localhost/cgi-bin/gitweb.cgi

服务器端暂且告一段落，下面打开管理员电脑，我为windows os
一，搜索下载安装Git for windows
安装过程注意选择，允许git写入path路径
二，进入Git bash
输入 @ssh-keygen -t rsa -C "your_email@youremail.com"@
在administrator下的.ssh文件夹中找到id_rsa.pub和id_rsa文件用u盘 或者ssh的scp 或者ftp服务器 复制到服务器上的/home/ftpmaster/tmp内,其中ftpmaster为你的用户名

回到服务器

初始化gitosis
进入到拷贝过来的id_rsa.pub所在目录：

bc.. 
cd /home/ftpmaster/tmp
sudo chmod 777 id_rsa.pub
sudo -H -u git gitosis-init < id_rsa.pub

p. 
此时，会在/home/git目录下生成一些目录，如果想要别人能够clone gitosis-admin.git，需要执行以下操作：
@sudo chmod 755 /home/git/repositories/gitosis-admin.git/hooks/post-update@
至此，gitosis的安装工作已完成，其相关配置可以有管理员来操作，然后再提交到服务器上。

回到各客户端电脑
安装Git for windows，进入Git-bash，使用ssh-keygen生成密匙，并把id_rsa_user.pub上传到服务器

回到服务器端电脑
一，把用户加入git clone权限

@su -git # 切換成 git 權限@

將大家的 public key 加入

bc.. 
    cat /home/ftpmaster/id_rsa_user1.pub >> ~/.ssh/authorized_keys
    cat /home/ftpmaster/id_rsa_user2.pub >> ~/.ssh/authorized_keys

p. 
註: commit / push 還是會保留原始 commit 人得名字, 不會是 git

二，建立 Git Repository

bc.. 
su - git
mkdir -p /home/git/project_name.git
cd /home/git/project_name.git
git init --bare --shared

p. 
三，回到客户端电脑,下载项目，修改项目，并提交项目
方法一，

bc.. 
git clone git@example.com:/home/git/project_name.git
cd project_name
vim test.txt
git add .
git commit -m 'add test.txt'
git push origin master

p. 
方法二，

bc.. 
mkdir project_name
cd project_name
git init
git add .
git commit -m 'initial commit'
git remote add origin git@example.com:/home/git/project_name.git
git push origin master （ps：这是第一次 Commit / Push 的指令，之后可以使用git push）

p. 
Git服务器的配置暂且介绍到这，后续有新的内容继续添加。
以上内容参考整理自：blog.csdn.net/wirror800/article/details/5189564

http://blog.longwin.com.tw/2011/03/build-git-env-share-over-ssh-2011/

python-setuptools.py下载地址："点击此处":http://down.qiannao.com/space/file/jiemar/share/2012/3/10/python-002dsetuptools_0.6.10-002d4ubuntu1_all.deb/.page