Mysql Community Server官网下载链接[链接](https://dev.mysql.com/downloads/mysql/)

注：本教程针对Mysql-8.0.21，未进行测试其他版本，可能会有存在区别，请自行判断。

第一步：初始化数据库
这个版本的Mysql是社区版服务版，有免安装包和安装包，我们一般使用免安装包——简单。
从前面的链接中下载Mysql-8.X.XX.zip，X.XX就是你看到的当前小版本。先将下载下来的Mysql安装包解压，得到如下目录：


Mysql初始目录

初始目录中没有data文件夹，没有my.ini文件。很多其他教程都有自己建data文件夹，自己创建my.ini文件并配置的，没关系。data文件夹不需要自己创建，轻度使用mysql也并不需要my.ini配置文件。
接下来管理员权限打开cmd，cd到你解压的目录中的bin文件夹下，输入以下命令进行初始化数据库存储文件：

mysqld.exe --initialize --console

这条命令会初始化数据库的基本存储结构和一些数据表格，默认用户名为root。一定记得加上--console，你才能在接下来打印的日志里看到密码。别想着不加--console找日志看密码，默认情况下不会生成日志文件滴。格式如下：

root@localhost:Uyejdm3,c8

Uyejdm3,c8就是你的密码，记下来，后面会用到。

这样mysql的路径下就会生成data文件夹，里面包含mysql初始化生成的数据文件，你以后的数据库和数据也都会存放在这个路径下。

第二步：不是改密码，而是注册服务
还是mysqld，这个兄弟有个d的后缀，是daemon(守护进程)，也就是mysql的服务由它来控制。继续执行如下命令创建Mysql的Windows服务：

mysqld.exe -install

如果你想删除服务了，还是用它，执行如下命令：

mysqld.exe -remove mysql

第三步：启动服务
启动服务执行windows的命令行：

net start mysql

停止服务则是：

net stop mysql

为啥注册服务和启动服务要分开来呢，因为，注册服务可能会成功，启动服务有小伙伴可能都会失败。常见原因猜测如下：

自己在初始化前创建了data文件目录或者my.ini文件
未按照上诉步骤进行
初始化后删除data文件重新安装过，等
因为出现错误没有任何日志和系统消息，至今原因只能靠猜测。如果一旦出现启动服务失败，请先删除服务，然后删除data文件夹，然后重启电脑，坐和放宽，再来一遍。

第四步：修改密码
这个步骤很关键，初始化时产生的密码只是临时密码，存在有效期，如果过了有效期没改密码，则只能——Again。

注意：需要服务器启动成功后才能登录mysql哈

还是在cmd里，执行以下命令登录mysql：

mysql -u root -p

键入命令后需要你输入密码回车登录。

接着修改user表中root这个用户的密码，输入以下SQL语句。

mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '这里的汉字换成你的新密码';

注意：前面的mysql>的不是SQL语句内容哈。
注意：root@localhost用户名和域名一定要加单引号哈，网上有的教程Copy的时候把单引号都抄掉了，然后mysql提示你没得这个用户。

提示成功，则说明完成了。赶紧打个压缩包，以后这个数据库你就可以到处copy，只需要1. 解压；2. 注册服务；3. 启动服务；就可以运行了。

作者：术士冲前面
链接：https://www.jianshu.com/p/76571f185dcb
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。MySQL网上教程很多，但是都是互相Copy，写的人自己都不懂是怎么回事。所以，想想还是写一个教程，总有能找到这个教程的兄弟不被误导。

Mysql Community Server官网下载[链接](https://links.jianshu.com/go?to=https%3A%2F%2Fdev.mysql.com%2Fdownloads%2Fmysql%2F)

> 注：本教程针对Mysql-8.0.21，未进行测试其他版本，可能会有存在区别，请自行判断。

#### 第一步：初始化数据库

这个版本的Mysql是社区版服务版，有免安装包和安装包，我们一般使用免安装包——简单。
 从前面的链接中下载Mysql-8.X.XX.zip，X.XX就是你看到的当前小版本。先将下载下来的Mysql安装包解压，得到如下目录：



![img](https:////upload-images.jianshu.io/upload_images/2301550-481c6ec96e652714.png?imageMogr2/auto-orient/strip|imageView2/2/w/383/format/webp)

Mysql初始目录



初始目录中没有data文件夹，没有my.ini文件。很多其他教程都有自己建data文件夹，自己创建my.ini文件并配置的，没关系。data文件夹不需要自己创建，轻度使用mysql也并不需要my.ini配置文件。
 接下来管理员权限打开cmd，cd到你解压的目录中的bin文件夹下，输入以下命令进行初始化数据库存储文件：

> mysqld.exe --initialize --console

这条命令会初始化数据库的基本存储结构和一些数据表格，默认用户名为root。一定记得加上--console，你才能在接下来打印的日志里看到密码。别想着不加--console找日志看密码，默认情况下不会生成日志文件滴。格式如下：

> root@localhost:Uyejdm3,c8

`Uyejdm3,c8`就是你的密码，记下来，后面会用到。

这样mysql的路径下就会生成data文件夹，里面包含mysql初始化生成的数据文件，你以后的数据库和数据也都会存放在这个路径下。

#### 第二步：不是改密码，而是注册服务

还是mysqld，这个兄弟有个d的后缀，是daemon(守护进程)，也就是mysql的服务由它来控制。继续执行如下命令创建Mysql的Windows服务：

> mysqld.exe -install

如果你想删除服务了，还是用它，执行如下命令：

> mysqld.exe -remove mysql

#### 第三步：启动服务

启动服务执行windows的命令行：

> net start mysql

停止服务则是：

> net stop mysql

为啥注册服务和启动服务要分开来呢，因为，注册服务可能会成功，启动服务有小伙伴可能都会失败。常见原因猜测如下：

- 自己在初始化前创建了data文件目录或者my.ini文件
- 未按照上诉步骤进行
- 初始化后删除data文件重新安装过，等

因为出现错误没有任何日志和系统消息，至今原因只能靠猜测。如果一旦出现启动服务失败，请先删除服务，然后删除data文件夹，然后重启电脑，坐和放宽，再来一遍。

#### 第四步：修改密码

这个步骤很关键，初始化时产生的密码只是临时密码，存在有效期，如果过了有效期没改密码，则只能——Again。

> 注意：需要服务器启动成功后才能登录mysql哈

还是在cmd里，执行以下命令登录mysql：

> mysql -u root -p

键入命令后需要你输入密码回车登录。

接着修改user表中root这个用户的密码，输入以下SQL语句。

> mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY '这里的汉字换成你的新密码';

注意：前面的`mysql>`的不是SQL语句内容哈。
 注意：root@localhost用户名和域名一定要加单引号哈，网上有的教程Copy的时候把单引号都抄掉了，然后mysql提示你没得这个用户。

提示成功，则说明完成了。赶紧打个压缩包，以后这个数据库你就可以到处copy，只需要1. 解压；2. 注册服务；3. 启动服务；就可以运行了。
