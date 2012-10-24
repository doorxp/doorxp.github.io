---
title: "Lion下，三步实现 PPTP(VPN) 自动路由"
layout: post
category: apple
---

前期准备：

1. 一个可用的 PPTP(VPN) 账号:

1.1. 自己购买或者自行 Google 一把;

2. 路由表文件:

2.1. 从开源项目 chnroutes (Google Project) 获得生成路由表的工具 chnroutes.py;

2.2. Teminal 窗口下, 找到 chnroutes.py 文件所在的文件夹, 执行如下命令:
帮助
1
	
python chnroutes.py -p mac 
正式开始:

1. 将生成的 ip-up 和 ip-down 两个文件复制到 /etc/ppp/ 目录下:

帮助
1
	
cp ip-up ip-down /etc/ppp/

2. 赋予 ip-up 和 ip-down 可执行权限:
帮助
1
	
sudo chmod a+x /etc/ppp/ip-up /etc/ppp/ip-down

3. 登陆 PPTP(VPN) 进行验证;
备注:

1. chnroutes 项目使用的路由表数据源来自于  http://ftp.apnic.net/apnic/stats/apnic/delegated-apnic-latest, 此路由表只针对中国大陆用户有效;

2. chnroutes 项目还提供了针对 Window/Linux/Android 系统, OpenVPN 方式的路由解决方案, 有需要的用户请自行查看解决方案;

3. 此路由表数据每三个月变动一次, 用户需要手动更新个人设备的路由表数据;