# centos 安装mysql 7.5

CentOS下的Mysql的安装和使用
1.使用安装命令 ：yum -y install mysql mysql-server mysql-devel
安装完成却发现Myserver安装缺失，在网上找原因，原来是因为CentOS 7上把MySQL从默认软件列表中移除了，用MariaDB来代替，所以这导致我们必须要去官网上进行下载，找到链接，用wget打开：

1.  wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm

2. rpm -ivh mysql-community-release-el7-5.noarch.rpm

3. 两次进行yum -y install mysql mysql-server mysql-devel，安装成功。再次使用该命令，然后得到以下信息






 2.Mysql分为client，server端两个端口，如果想要使用，先打开服务端




3.接下来进入客户端，作用命令mysql -u root -p 输入密码，默认为空




 4.基本操作 show databases;

5.使用某个数据库use chouti,查看表show tables;
6.常用数据库操作
复制代码
 1 mysql数据库使用总结
 2 本文主要记录一些mysql日常使用的命令，供以后查询。
 3 1.更改root密码
 4 mysqladmin -uroot password 'yourpassword'
 5 2.远程登陆mysql服务器
 6 mysql -uroot -p -h192.168.137.10 -P3306
 7 3.查询数据库
 8 show databases;
 9 4.进入某个数据库
10 use databasename;
11 5.列出数据库中的表
12 show tables;
13 6.查看某个表全部字段
14 desc slow_log;
15 show create table slow_log\G; （不仅可以显示表信息，还可以显示建表语句）
16 7.查看当前用户
17 select user();
18 8.查看当前所在数据库
19 select database();
20 9.创建新数据库（可以指定字符集）
21 create database db1 charset utf8;
22 10.创建新表
23 create table t1 (`id` int(4), `name` char(40));
24 11.查看数据库版本
25 select version();
26 12.查看数据库状态
27 show status;         当前会话状态
28 show global status;  全局数据库状态
29 show slave status\G;   查看主从数据库状态信息
30 13.查询数据库参数
31 show variables;
32 14.修改数据库参数
33 show variables like 'max_connect%';
34 set global max_connect_errors = 1000;（重启数据库会失效，要在配置文件中修改）
35 15.查看当前数据库队列
36 show processlist;
37 16.创建普通用户并授权给某个数据库
38 grant all on databasename.* to 'user1'@'localhost' identified by '123456';
39 17.查询表数据
40 select * from mysql.db;           //查询该表中的所有字段
41 select count(*) from mysql.user;  //count(*)表示表中有多少行
42 select db,user  from mysql.db;    //查询表中的多个字段
43 select * from mysql.db where host like '10.0.%';在查询语句中可以使用万能匹配 “%”
44 18.插入一行数据
45 insert into db1.t1 values (1, 'abc');
46 19.更改表的某一行数据
47 update db1.t1 set name='aaa' where id=1;
48 20.清空表数据
49 truncate table db1.t1;
50 21.删除表
51 drop table db1.t1;
52 22.清空数据库中的所有表（数据库名是eab12）
53 mysql -N -s information_schema -e "SELECT CONCAT('TRUNCATE TABLE ',TABLE_NAME,';') FROM TABLES WHERE TABLE_SCHEMA='eab12'" | mysql -f eab12
54 23.删除数据库
55 drop database db1;
56 24.数据库备份
57 mysqldump  -uroot -p'yourpassword' mysql >/tmp/mysql.sql
58 25.数据库恢复
59 mysql -uroot -p'yourpassword' mysql </tmp/mysql.sql
60 26.新建普通用户
61 CREATE USER name IDENTIFIED BY 'ssapdrow';
62 27.更改普通用户密码
63 SET PASSWORD FOR name=PASSWORD('fdddfd');
64 28.查看name用户权限
65 SHOW GRANTS FOR name;
66 29.脚本中执行mysql命令
67 mysql -uuser -ppasswd -e"show databases"
68 echo "show databases"|mysql -uuser -ppassword
69 以下是执行大量mysql语句采用的方式
70 mysql -uuser -hhostname -ppasswd <<EOF
71 mysql语句
72 EOF
复制代码
7.至于对于表的增删查改，由于属于另外的内容，这里不再展示。由于服务器（CentOS）的带宽或者空间限制，所以不太可能像windows一样进行相应的类似于navicat式的图形化操作，因此还是请大家多多熟悉相应的关于黑屏窗口的操作

解决创建my sql用户报错 :ERROR 1364 (HY000): Field 'ssl_cipher' doesn't have a default value

`GRANT USAGE ON *.* TO 'qing'@'%' IDENTIFIED BY 'wqs7217' WITH GRANT OPTION`

然后对你建的用户进行授权 

`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON tablename.*  TO 'qing'@'%' IDENTIFIED BY 'wqs7217';` --注localhost改为% 才能通过任何ip访问数据库

`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON *.*  TO 'qing'@'%' IDENTIFIED BY 'wqs7217';` --为任意表

### **修改加密规则**   

ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;  

### **更新密码（mysql_native_password模式）**

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'NewPassword';



最后   



## 忘记密码怎么办?

1.从安全模式进入mysql

进入 mysql/bin 目录 

输入`sudo su`获取管理员权限

输入 `./mysqld_safe --skip-grant-tables` 回车 以禁用mysql验证功能

输入 `mysql`  回车

输入

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER;   
```

设置PASSWORD EXPIRE

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```

修改root账户密码

FLUSH PRIVILEGES;  刷新

## 忘记密码怎么办2?

​	mysqladmin -u root password newpwd

## 错误记录

Q:Could not open unix socket lock file /var/lib/mysql/mysql.sock.lock.

A:https://blog.csdn.net/qq_41359051/article/details/89815921> 

Q:linux 删除mysql

A:<https://www.cnblogs.com/leelice/p/10728129.html> 

##最后

记得重启mysql

忘记root密码修改方法：<https://www.jianshu.com/p/891af51c143f> 

参考地址：https://blog.csdn.net/fly0744/article/details/8889586

原文地址 https://www.cnblogs.com/suxiaoman/p/7693066.html

参考地址安装地址  https://www.cnblogs.com/bigbrotherer/p/7241845.html



GRANT USAGE ON *.* TO 'qing'@'%' IDENTIFIED BY 'wqs7217' WITH GRANT OPTION;

`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON reader.*  TO 'qing'@'localhost' IDENTIFIED BY 'wqs7217';` 

`GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON tablename.*  TO 'username'@'localhost' IDENTIFIED BY 'password';` 

GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP ON tablename.*  TO 'qing'@'localhost' IDENTIFIED BY 'wqs7217';



rm -rf /var/lib/mysql
rm -rf  /var/lib/mysql/mysql
rm -rf  /etc/selinux/targeted/active/modules/100/mysql
rm -rf  /usr/share/mysql