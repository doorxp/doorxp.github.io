---
layout: post
title: "Installing Apache, MySQL, PHP in CentOS 5.5"
category: php
---

Most of cloud users prefer Rackspace Cloud for its better service and cost effectiveness…
All commands are performed as root to install Apache, MySQL, PHP in CentOS 5.5 Server on Rackspace Cloud.

Install Apache

@sudo yum install httpd mod_ssl@

Configure ServerName
@sudo /etc/init.d/httpd start@

Remove Error
Starting httpd: httpd: Could not reliably determine the server’s fully qualified domain name, using xx.xx.xxx.xx for ServerName

Modify
@sudo vi /etc/httpd/conf/httpd.conf@

Add server name after #ServerName www.example.com:80

ServerName svnlabs

@sudo /etc/init.d/httpd reload@

By default port 80 is blocked on server, enable in iptables…
@iptables -I RH-Firewall-1-INPUT -p tcp –dport 80 -j ACCEPT@

<img src='/images/article/phpcentos/Apache_CentOS.png' width='90%' height='90%'/>
Chkconfig to start automatically if the Cloud Server is rebooted.

@sudo /sbin/chkconfig httpd on@

Install MySQL

@yum install mysql-server@
@/etc/init.d/mysqld start@

Install PHP

Edit Repo file
@sudo vi /etc/yum.repos.d/CentOS-Base.repo@

add at last

[c5-testing]
name=CentOS-5 Testing
baseurl=http://dev.centos.org/centos/$releasever/testing/$basearch/
enabled=1
gpgcheck=1
gpgkey=http://dev.centos.org/centos/RPM-GPG-KEY-CentOS-testing

now run
@yum install php php-common php-gd php-mcrypt php-pear php-pecl-memcache php-mhash php-mysql php-xml php-mbstring@

Install PhpMyAdmin

@cd /var/www/html@

bc. wget http://downloads.sourceforge.net/project/phpmyadmin/phpMyAdmin/3.3.6/phpMyAdmin-3.3.6-english.tar.gz?r=http%3A%2F%2Fwww.phpmyadmin.net%2Fhome_page%2Fdownloads.php&ts=1283745912&use_mirror=voxel
tar zxvf phpMyAdmin-3.3.6-english.tar.gz
mv phpMyAdmin-3.3.6-english phpmyadmin
cd phpmyadmin
cp config.sample.inc.php config.inc.php

p. Edit config.inc.php for random string $cfg['blowfish_secret'] = ‘RandomStringHere’;

Restart server
@service httpd restart@

!/images/article/phpcentos/phpMyAdmin.png!
