---
title: CentOS7 yum方式安装MySQL5.7
---



转载至博客：https://www.cnblogs.com/bigbrotherer/p/7241845.html

在CentOS中默认安装有MariaDB，这个是MySQL的分支，但为了需要，还是要在系统中安装MySQL，而且安装完成之后可以直接覆盖掉MariaDB。

### 1 下载并安装MySQL官方的 Yum Repository

```bash
[root@localhost ~]# wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm
```

 使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了。

<!--more-->

```bash
[root@localhost ~]# yum -y install mysql57-community-release-el7-10.noarch.rpm
```

 之后就开始安装MySQL服务器。

```bash
[root@localhost ~]# yum -y install mysql-community-server
```

 这步可能会花些时间，安装完成后就会覆盖掉之前的mariadb。

![img](https://images2017.cnblogs.com/blog/1079354/201707/1079354-20170726201927328-459165254.png)

至此MySQL就安装完成了，然后是对MySQL的一些设置。

### 2 MySQL数据库设置

 首先启动MySQL

```bash
[root@localhost ~]# systemctl start  mysqld.service
```

 查看MySQL运行状态，运行状态如图：

```bash
[root@localhost ~]# systemctl status mysqld.service
```

![img](https://images2017.cnblogs.com/blog/1079354/201707/1079354-20170726202441687-1168874203.png)

 此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：

```bash
[root@localhost ~]# grep "password" /var/log/mysqld.log
```

![img](https://images2017.cnblogs.com/blog/1079354/201707/1079354-20170726202722796-1932560887.png)

 如下命令进入数据库：

```bash
[root@localhost ~]# mysql -uroot -p
```

 输入初始密码，此时不能做任何事情，因为MySQL默认必须修改密码之后才能操作数据库：

```bash
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```

 这里有个问题，新密码设置的时候如果设置的过于简单会报错：

![img](https://images2017.cnblogs.com/blog/1079354/201707/1079354-20170726203136000-1398594667.png)

 原因是因为MySQL有密码设置的规范，具体是与validate_password_policy的值有关：

 ![img](https://images2017.cnblogs.com/blog/1079354/201707/1079354-20170726203904812-1008115240.png)

 MySQL完整的初始密码规则可以通过如下命令查看：

```bash
mysql> SHOW VARIABLES LIKE 'validate_password%';
+--------------------------------------+-------+
| Variable_name                        | Value |
+--------------------------------------+-------+
| validate_password_check_user_name    | OFF   |
| validate_password_dictionary_file    |       |
| validate_password_length             | 4     |
| validate_password_mixed_case_count   | 1     |
| validate_password_number_count       | 1     |
| validate_password_policy             | LOW   |
| validate_password_special_char_count | 1     |
+--------------------------------------+-------+
7 rows in set (0.01 sec)
```



 密码的长度是由validate_password_length决定的，而validate_password_length的计算公式是：

```bash
validate_password_length = validate_password_number_count + validate_password_special_char_count + (2 * validate_password_mixed_case_count)
```

 

我的是已经修改过的，初始情况下第一个的值是ON，validate_password_length是8。可以通过如下命令修改：

```bash
mysql> set global validate_password_policy=0;
mysql> set global validate_password_length=1;
```

 设置之后就是我上面查出来的那几个值了，此时密码就可以设置的很简单，例如1234之类的。到此数据库的密码设置就完成了。

 但此时还有一个问题，就是因为安装了Yum Repository，以后每次yum操作都会自动更新，需要把这个卸载掉：

```bash
[root@localhost ~]# yum -y remove mysql57-community-release-el7-10.noarch
```

 此时才算真的完成了。

转载至博客：https://www.cnblogs.com/bigbrotherer/p/7241845.html











# CentOS7安装mysql5.7（yum）



安装环境：CentOS7 64位 ，安装MySQL5.7

### 1、配置YUM源

#### 在MySQL官网中下载YUM源rpm安装包：[https://dev.mysql.com/downloads/repo/yum/](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdownloads%2Frepo%2Fyum%2F)



```cpp
wget http://dev.mysql.com/get/mysql57-community-release-el7-8.noarch.rpm
```

#### 安装mysql源



```css
yum localinstall mysql57-community-release-el7-8.noarch.rpm
```

#### 检查mysql源是否安装成功



```bash
yum repolist enabled | grep "mysql.*-community.*"
```

![img](https://upload-images.jianshu.io/upload_images/13498144-df07bd235bf5bc35.png?imageMogr2/auto-orient/strip|imageView2/2/w/638/format/webp)



### 2、安装MySQL



```undefined
yum install mysql-community-server
```

### 3、启动MySQL服务



```undefined
systemctl start mysqld
```

### 4、开机启动



```bash
systemctl enable mysqld
systemctl daemon-reload
```

### 5、修改root本地登录密码

mysql安装完成之后，在/var/log/mysqld.log文件中给root生成了一个默认密码。通过下面的方式找到root默认密码，然后登录mysql进行修改：



```bash
set password for 'root'@'localhost'=password('password!'); 
```

![img](https://upload-images.jianshu.io/upload_images/13498144-c1957a4544ef2021.png?imageMogr2/auto-orient/strip|imageView2/2/w/683/format/webp)

### 6、允许远程连接



```csharp
grant all on *.* to root@'%' identified by 'password' with grant option; 
flush privileges;
```