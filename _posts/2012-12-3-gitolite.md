---
title: "搭建Git服务器-gitolite"
layout: post
category: apple
---



git就是一个很流行的版本控制工具,至于gitolite的介绍，可以参考官方网站。

直接进入正题

    安装git
    yum install git
    添加git用户及git组,目录仅供参考,可以设置成其他的
    mkdir -o /data/git
    groupadd git
    useradd -g git -d /data/git -s /bin/bash git
    修改git用户密码,及git目录权限
    passwd git

    客户端声称ssh-key,YOURNAME改成你喜欢的
    ssh-keygen -t rsa -f ~/.ssh/YOURNAME
    然后把~/.ssh/YOURNAME.pub传到服务器上(scp/ftp…)
    安装gitolite
    su git
    cd $HOME
    git clone git://github.com/sitaramc/gitolite
    cd gitolite
    src/gl-system-install
    echo "PATH=$PATH:$HOME/bin" >> ~/.bashrc
    source ~/.bashrc
    gl-setup ~/YOURNAME.pub       #这里就是你刚才传的那个公钥

服务器端搭建结束，快吧！

接下来是gitolite的配置，以下都是在客户端进行，客户端要有git

    编辑~/.ssh/config,仿照以下内容
    host gitolite #只是一个名字
            user git
            hostname YOUR SERVER NAME/IP
            port 22    #ssh端口号,默认22,改成你的
            identityfile ~/.ssh/YOURNAME    #最开始ssh-keygen生成的那个
    改config文件权限(不改似乎也行)
    chmod 0644 config
    找个好地方,把gitolite-admin clone下来
    git clone gitolite:gitolite-admin
    设置git管理员信息,任意
    git config –global user.email "YOUREMAIL"
    git config –global user.name "YOURNAME"
    添加用户(首先要得到那个用户机器上生成的ssh-key)
    cd gitolite-admin/
    cp ANOTHERUSER.pub keydir/ANOTHERUSER.pub
    git add keydir/
    git commit -m "add new user"
    git push
    添加用户完成,如需添加多个用户方法类似

–以上–