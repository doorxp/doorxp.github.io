---                                                                                                                                                          
layout: post
title: iOS
category: linux
#published: false
---
"ShopQi":https://github.com/saberma/shopqi 中应用到的技术越来越多，开发环境的搭建变得越来越繁琐，这对开源项目的团队协作是相当不利的
试想，每个程序员的偏好都不同，所使用的操作系统，编辑器都是五花八门的，在开发过程中由于开发环境不一致出现问题的不在少数，调试难度也大

所以，很多公司要求所有的开发机器不能随意安装软件，要保持开发环境高度一致
这种强制限制虽然让人感到不自由，但在公司做软件，也算情有可原

而做开源项目就不同了，我们要尽可能提供便利，减少安装过程对原有环境的影响
要实现这一目标是非常困难的，还好，现在有了Vagrant

h2. Vagrant

!/images/article/develop/vagrant.png!

Vargant是构建虚拟化开发环境的工具，它是用ruby开发的，并以gem方面发布，它会帮我们

# 创建一个Ubuntu10.04版本的32位VirtualBox虚拟机，并应用chef安装项目所需的ruby、数据库、应用服务器程序等
# 将宿主机项目所在的目录与虚拟机共享，我们在宿主机用喜欢的编辑器修改程序，虚拟机中的开发环境可以立即读取到

这样就可以保证项目组成员的开发环境是一致的,不一定是rails项目才适用,java php或其他的都是可以使用vagrant

有时候,我们需要跟进开发多个项目,有的用java,有的用ruby,有的使用mysql,有的使用mongodb
把这些都装起来也没问题,但是有点浪费性能了,同一时间我们只会开发一个项目
用vagrant我们可以很好的解决这类问题,每个项目一个虚拟机,需要时才启动

h3. 下载

虚拟机的操作系统镜像文件是经过定制的，在vargant中引入的镜像文件称为"Box"
我们先下载这个镜像文件，地址如下

bq. 
"http://files.vagrantup.com/lucid32.box":http://files.vagrantup.com/lucid32.box
假设下载后放至~/lucid32.box

同时需要下载VirtualBox程序，并安装

bq. 
"http://www.virtualbox.org/wiki/Downloads":http://www.virtualbox.org/wiki/Downloads

h3. 安装

安装前请确保宿主机已经安装了ruby和rubygem

pre. 
gem install bundler vagrant --no-ri --no-rdoc

h3. 创建虚拟机

先引入刚才下载的镜像文件，作为vagant管理下的一个box，以后每次要生成一个用于开发的虚拟机，都会从这个box中复制出来

pre. 
vagrant box add lucid32 ~/lucid32.box
#进入项目目录
cd ~/Documents/shopqi
#初始化，生成配置文件Vagrantfile
vagrant init lucid32
#生成项目虚拟机
vagrant up

等一会儿，一个ubuntu的虚拟机就建好了

h3. 在虚拟机上安装开发环境

!/images/article/vagrant/pstree.png!

vagrant可以根据chef指定的cookbook recipe进行安装，关于chef的介绍可以参考 "应用chef构建服务器集群自动化部署与管理":/linux/2011/01/27/chef-for-automate-deploy.html
这里采用的是不依赖chef服务器的chef-solo方式，recipe采用 "ShopQi":https://github.com/saberma/shopqi 服务器部署的cookbook
指定recipe，需要修改Vagrantfile，增加以下内容

bq. 
config.vm.provision :chef_solo do |chef|
　chef.recipe_url = "https://dl.dropbox.com/u/19519145/shopqi/chef-solo.tar.gz"
　chef.add_recipe "develop"
end

chef-solo.tar.gz文件是包含cookbooks集合的压缩包

pre. 
#进入cookbooks所在目录后运行命令
tar zcvf chef-solo.tar.gz ./cookbooks

develop cookbook会引入开发机所需要的依赖recipe， "查看内容":https://github.com/saberma/chef-repo/blob/master/cookbooks/develop/recipes/default.rb

h3. 启动虚拟机

pre. 
vagrant up
#如果已经启动，只想更新或者调试recipe，运行vagrant provision

如果你和我一样,喜欢rvm来管理ruby版本,并安装了除system自带的ruby,则需要修改/etc/profile,否则gems会被安装在自带ruby之下
参考 "http://rvm.beginrescueend.com/integration/vagrant/":http://rvm.beginrescueend.com/integration/vagrant/

h3. ssh登录虚拟机

之后，我们可以登录虚拟机，像以前一样执行rake等命令

pre. 
vagrant ssh
#查看项目目录，会发现与宿主机的当前目录内容一致
ls /vagrant

刚才引入的cookbook包含了nginx recipe，所以我们可以看看虚拟机上nginx是否能正常访问了

pre. 
wget -qO- 127.0.0.1:3000

結果返回:

pre. 
<html>
<head>
<title>Welcome to nginx!</title>
</head>
<body bgcolor="white" text="black">
<center><h1>Welcome to nginx!</h1></center>
</body>
</html>

说明能正常访问，为了能在宿主机上用浏览器访问它，我们需要进行端口映射
修改VagrantFile，加入以下内容:

bq. 
Vagrant::Config.run do |config|
　# Forward guest port 3000 to host port 8088 and name the mapping "web"
　config.vm.forward_port("web", 3000, 8088)
end

用浏览器打开 "http://127.0.0.1:8088":http://127.0.0.1:8088 就可以访问了

h3. 打包发布

现在开发环境已经安装好了，整个过程非常高效。更进一步，我们可以把已经做好的环境打包成镜像文件，分发给其他人，他们就可以瞬间建好开发环境了。

准备pkg文件,此文件只用于定义映射端口,会被合并至Vagrantfile文件中,内容一般为

bq. 
Vagrant::Config.run do |config|
  #Forward nginx
  config.vm.forward_port("web", 3000, 8088)
end

pre. 
#执行打包
vagrant package --vagrantfile Vagrantfile.pkg

执行后当前目录会生成package.box文件

h3. 关闭虚拟机

pre. 
vagrant halt

h2. 测试

h3. 单元测试

还是跟以前一样

h3. 验收测试

由于cucumber使用的@javascript，需要打开浏览器测试ajax。而虚拟机并未安装桌面，此测试进行不了。
开源社区中有不少技术可以进行headless后台测试，但都不成熟.

所以,我们只能在宿主机上进行这样的测试了,这样的话,宿主机又要安装项目的相关插件,这可不少.
有什么办法能让插件跟着项目跑,而不把插件安装到系统上呢?

答案是可以的,我们在虚拟机上使用bundle install安装插件的时候,指定参数--path vendor/bundle，将插件安装在项目的vendor/bundle目录，这样在宿主机就不用再安装这些插件了。
还有,我们的测试数据库还在虚拟机上,还得修改 @Vagrantfile@ ,把mongodb,redis-server,nodejs等服务的端口映射至宿主机
直接在宿主机上运行以下命令就可以测试了

pre. 
bundle exec cucumber features

注意:将gems打包后,会导致在虚拟机运行rails各种命令时,速度巨慢,原因是virtaulbox共享目录的bug:随着目录内文件数的增长,对共享目录的访问会很慢
解决方案是应用nfs共享目录,具体查看这里 "http://vagrantup.com/docs/nfs.html":http://vagrantup.com/docs/nfs.html 和 "http://vagrantup.com/docs/host_only_networking.html":http://vagrantup.com/docs/host_only_networking.html

pre. 
#宿主机需要安装nfs服务
sudo apt-get install nfs-kernel-server

h3. 进入rails控制器

经过上面的处理,插件已经安装好了,但是由于rails并未安装进系统,使用以下命令是进不了控制台的

pre. 
rails c

得在项目目录下,运行

pre. 
script/rails c

h2. 报错

h3. 找不到client.pem

bq. 
[default] I cannot read /etc/chef/client.pem, which you told me to use to sign requests!: stderr
[default]  (: stderr
[default] Chef::Exceptions::PrivateKeyMissing: stderr

这也太奇怪了，用的是chef-solo方式，而非chef-server，怎么也需要client.pem?
原来是部分cookbook使用了search方法，此方法用于从chef-server中查找相应的信息

解决方法是在cookbook中加入判断条件，开发环境部署时不调用search，生产环境才调用

bq. 
node[:instance_role]

h3. 'unknown': unknown terminal type.

很不幸，这是vagrant的一个bug，详情参考 "commit#77a1b9a6efe4f5cdc993":https://github.com/mitchellh/vagrant/commit/77a1b9a6efe4f5cdc99343e844835fc790a64c28
解决方法，更新vagrant至0.7.3，不过，这个版本还没有发布，只能先手动修改本地gem的代码了

h3. FATAL: No cookbook found in ["/tmp/vagrant-chef/cookbooks-0", "/tmp/vagrant-chef/cookbooks"], make sure cookbook_path is set correctly.

这是vagrant的一个bug,详情请看
"https://github.com/mitchellh/vagrant/issues#issue/308":https://github.com/mitchellh/vagrant/issues#issue/308
"https://github.com/mitchellh/vagrant/issues/closed#issue/297":https://github.com/mitchellh/vagrant/issues/closed#issue/297

不影响使用,作者正在修复...

h3. 运行chef-solo过程的其他错误?

pre. 
#登录虚拟机
vagrant ssh
#在虚拟机运行chef-solo,显示更显示的debug信息
cd /tmp/vagrant-chef
sudo chef-solo -c solo.rb -j dna.json -l debug

如果希望vagrant命令运行时都显示详细信息,可以配置Vagrantfile

bq. 
config.vm.provision :chef_solo do |chef|
　chef.log_level = :debug
end

h2. 参考资源

"Vagrant官网":http://vagrantup.com/
"chef_solo配置":http://vagrantup.com/docs/provisioners/chef_solo.html
"client.pem error":http://tickets.opscode.com/browse/CHEF-1115
"Creating An Ubuntu 10.10 x32 Vagrant Box":https://github.com/fnichol/wiki-notes/wiki/Creating-An-Ubuntu-10.10-x32-Vagrant-Box
"Setting up a Vagrant VM for TriSano":https://wiki.csinitiative.com/display/tri/Setting+up+a+Vagrant+VM+for+TriSano
"opscode vagrant":http://wiki.opscode.com/display/chef/Vagrant
