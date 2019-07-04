---
layout: post
title: 在Mac上安装MySQL
tags: [docker]
bigimg: /img/path.jpg
comments: true
---

* toc
{:toc}

# 1. 下载MySQL
首先，进入MySQL官网的Download界面，点击进入“MySQL Community Edition (GPL)”下载界面。

选取MacOS对应的MySQL版本，点击下载。

# 2. 安装MySQL

双击mysql-5.7.17-macos10.12-x86_64.dmg进行解压, 然后双击mysql-5.7.17-macos10.12-x86_64.pkg进行安装。

一直点击continue确认安装。最后弹出框会为你的MySQL的root账户产生一个临时密码，记得保存。

最后确认安装成功。

# 3.配置环境变量

在命令行中，首先查找/usr/local/mysql/bin

```
ShulindeMacBook-Pro:bin shulin$ ls /usr/local/mysql/bin
ibd2sdi				mysql_secure_installation	mysqldump
innochecksum			mysql_ssl_rsa_setup		mysqldumpslow
lz4_decompress			mysql_tzinfo_to_sql		mysqlimport
my_print_defaults		mysql_upgrade			mysqlpump
myisam_ftdump			mysqladmin			mysqlrouter
myisamchk			mysqlbinlog			mysqlrouter_passwd
myisamlog			mysqlcheck			mysqlrouter_plugin_info
myisampack			mysqld				mysqlshow
mysql				mysqld-debug			mysqlslap
mysql_config			mysqld_multi			perror
mysql_config_editor		mysqld_safe			zlib_decompress
```

接下来，在~/.bash_profile中添加mysql/bin的目录：

```
ShulindeMacBook-Pro:bin shulin$ vim ~/.bash_profile
```

```
export PATH="/usr/local/bin:$PATH"
# Virtual Environment Wrapper
#export WORKON_HOME=~/venvs

# Set Path for MySQL
PATH=$PATH:/usr/local/mysql/bin

#VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
#source /usr/local/bin/virtualenvwrapper.sh
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
~                                                                                                               
"~/.bash_profile" 9L, 442C
```

最后，运行：
```
source ~/.bash_profile
```
# 4.使用MySQL

现在，通过mysql -uroot -p，并输入用户密码，root便可以登陆mysql了。

登录成功后，可以通过以下命令修改密码：
```
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('newpassword')
```

其中，“newpassword”为新设的密码。