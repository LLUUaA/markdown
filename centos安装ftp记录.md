#centos安装ftp记录

[TOC]

## 1.更换默认防火墙

首先是停用原先的firewall防火墙服务：

**systemctl stop firewalld.service**

然后禁用firewall防火墙的开机自启：

**systemctl disable firewalld.service**

然后是安装iptables：

**yum install iptables-services**

接下来我们需要对iptables进行一些必要的配置。

**vim /etc/sysconfig/iptables**

上面的命令将启动Vim编辑器编辑iptables的配置文件，先按i键进入编辑模式，把里面原有内容全部删除，将以下内容输入。输入结束后按Esc键退出编辑模式，并输入:wq保存并退出。



```sh
#sample configuration for iptables service
#you can edit this manually or use system-config-firewall
#please do not ask us to add additional ports/services to this default configuration
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT

# SSH

-A INPUT -p tcp -m state --state NEW -m tcp --dport 22 -j ACCEPT

# HTTP

-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT

# MySQL

-A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
COMMIT
```

配置文件编辑完成之后记得进行以下两个操作：

重启iptables服务以使用新的配置文件：

**systemctl restart iptables.service**

设置iptables服务开机自启动：

**systemctl enable iptables.service**

## 2.关闭SELinux

除了需要更改系统默认的防火墙以外，我们还需要关闭SELinux，这个步骤是可选的。

SELinux是一个由美国国家安全局和SCC开发的 Linux的一个扩张强制访问控制安全模块。它可以保护Linux，但是开着SELinux有时候会发生一些莫名其妙的问题。所以在这里还是关掉算了。

 

先修改一下配置文件

**vim /etc/selinux/config**

在Vim编辑器中找到

```sh
#SELINUX=enforcing                  （在前面加#注释掉）

#SELINUXTYPE=targeted           （在前面加#注释掉）

SELINUX=disabled                      （增加此行）
```

修改结束后保存退出 

修改配置文件之后并不能立即关闭SELinux，我们还需要键入以下命令来立即关闭SELinux：

**setenforce 0**

### 到这里安装iptables 就完成了，接下来进入主要环节了就是是安装vsftpd了

------





## 1.安装VSFTPD

然后键入以下命令以安装VSFTPD

**yum install vsftpd**

然后我们需要将vsftpd启动并设置成开机自启动：



启动vsftpd：

**systemctl start vsftpd.service**

设置vsftpd开机自启动：

**systemctl enable vsftpd.service**

## 2.配置VSFTPD

完成第一步之后其实已经启动了ftp服务器，但我们并用不了，因为我们还没有对vsftpd进行一些必要的设置。

vsftpd的配置文件是/etc/vsftpd/vsftpd.conf，直接用vim打开编辑即可。

使用vim编辑器打开vsftpd配置文件：

**vim /etc/vsftpd/vsftpd.conf**

 

vsftpd的配置文件非常大，所以我就不截图展示和完整展示了，我们直接挑关键的地方进行一些简单的修改。

注：在vim中，非编辑状态下输入“/”+需要查找的内容 后按回车键可以快键查找指定字符串。

 

**anonymous_enable=YES**

是否允许匿名用户登陆FTP。

为了安全起见关闭这个功能（将等号后的YES改成NO即可）。

 

**dirmessage_enable=YES**

切换目录时，显示目录下.message文件中的内容

默认是开启的



**local_umask=022**

FTP上本地的文件权限，默认是077，不过vsftpd安装后的配置文件里默认是022.

没有什么特殊情况不用修改。

 

**xferlog_enable=YES**

启用上传和下载的日志功能，默认开启。

建议开启此功能，它可以对用户的操作进行日志记录，当出现问题的时候可以通过日志排查问题。

 

**ftpd_banner=XXXX**

FTP的欢迎信息。

在FTP登陆成功之后，服务器会往客户端发送一个欢迎消息以表示登陆成功。这是一个个性化的功能，您可以自由的设置其值，也可以在配置最前加上#注释本行。

 

**data_connection_timeout=120**

数据连接超时时间。

如果在使用vsftpd上传下载碎小文件的时候容易发生超时中断的问题，可以将本行前的#注释符去掉，然后将120改成5或者更小，然后重启vsftpd即可。

 

修改配置文件完成。保存后重启VSFTPD。

重启vsftpd服务：

**systemctl restart vsftpd.service**

## 3.创建FTP用户

修改完vsftpd的配置文件之后我们还是不能使用vsftpd，因为我们还没有设置ftp的用户。

 

添加一个名为ftpuser的用户，用户文件夹位置为：/var/www/html，且禁止此用户登陆服务器：

**useradd -d /var/www/html -s /sbin/nologin ftpuser**

 

然后设置一下密码，为ftpuser设置密码：

**passwd ftpuser**

## 4.调整防火墙

经过第三步创建用户之后，有小部分的同学可能已经可以登陆了。但是绝大部分的同学会连接ftp失败，提示连接失败 (连接已超时)

失败的原因很简单，不知大伙有没有想起来之前的博文中我教大家关闭了系统自带的firewall防火墙，换上了新的iptables防火墙？就是iptables防火墙将我们的连接请求阻断了。如果你个人怕麻烦，而且也觉得防火墙没什么用，那你可以将iptables防火墙关闭，关闭防火墙之后就可以正常使用了。

不过因为这么一点小事儿就关闭防火墙未免显得有点水，而且防火墙摆在那里总归是有用的。那么有什么办法既保留防火墙，又能让iptables不把我们的ftp连接请求阻断呢？

当然有，我们需要调整一下iptables的配置文件，使ftp协议的端口可以通过防火墙。 FTP有两种模式，主动模式和被动模式。由于两种模式使用的端口不一样，所以调整的内容也不一样。 

### FTP主动模式

使用Vim编辑器打开iptables配置文件：

**vim /etc/sysconfig/iptables**

然后在配置文件中加入这么一句：

> -A INPUT -m state --state NEW -m tcp -p tcp --dport 21 -j ACCEPT

这句话告诉iptables开放21端口，允许接受从21端口传入的连接。

然后重启iptables服务：

**systemctl restart iptables.service**

 

现在就可以使用ftp工具登陆我们的ftp服务器了！

### FTP被动模式

如果ftp处于被动模式下，除了需要修改iptables的配置文件以外，还需要修改vsftpd的配置文件。

首先是修改vsftpd的配置文件：

使用Vim编辑器打开vsftpd配置文件：

**vim /etc/vsftpd/vsftpd.conf**

 

现在配置文件中找到“connect_from_port_20=YES”并将它修改为“connect_from_port_20=NO”，关闭掉vsftpd的主动模式。

然后在配置文件的末尾追加：

> \#使vsftpd运行在被动模式
>
> pasv_enable=YES
>
> \#被动模式最小端口号30000
>
> pasv_min_port=30000
>
> \#被动模式最大端口号31000
>
> pasv_max_port=31000

保存配置文件并退出。

然后重启vsftpd服务：

**systemctl restart vsftpd.service**

 

然后再使用Vim编辑器打开iptables配置文件：

**vim /etc/sysconfig/iptables**

 

添加这两句话：（“#”开头的是注释，可以不添加）

> \#开放ftp协议21端口，允许接受来自21端口的新建TCP连接
>
> -A INPUT -p tcp -m state --state NEW -m tcp --dport 21 -j ACCEPT
>
> \#开放30000-31000号端口，允许接受来自此端口号段的新建TCP连接
>
> -A INPUT -p tcp --dport 30000:31000 -j ACCEPT

保存并退出，然后重启iptables服务：

**systemctl restart iptables.service**

 

现在就可以使用ftp工具登陆我们的ftp服务器了！

## 5.调整文件夹权限

可能现在又有个问题冒出来了，使用ftp工具登陆服务器之后，不管创建什么，都会失败。

这个问题主要是在服务器的文件夹权限设置上。以笔者为例，笔者将ftp服务器登陆后的默认文件夹设置为/var/www/html，登陆ftp之后上传什么文件都显示553 Could not create file. 

进入/var/www文件夹

**cd /var/www**

查看一下目录权限

**ls –l**

我们可以看到html文件夹的权限是drwxr-xr-x。

我们将这个权限字符串分为四个部分，以顿号隔开：d、rwx、r-x、r-x。

这四部分分别说明了：

1.此文件其实是个文件夹；

2.此文件的文件主拥有读、写、执行权限；

3.此文件的组用户拥有读、执行权限；

4.此文件的其他用户拥有读、执行权限。

 

果然是权限不够！

调整一下权限，让所有人都拥有读、写、执行的权力

**chmod 777 html**

再看一下现在的文件夹权限

**ls –l**

可以了，返回ftp工具，上传文件，一切正常！ 

## 小编注：

有些同学在安装会遇到Failed to restart iptables.service: Unit **not** found. 这样的错误，这种情况一般重新走一次流程或者系统还原后优先安装VSFTPD。