# Nginx篇

[TOC]

## 1.nginx 安装

### 1.winoows安装

首先下载[nginx](nginx.org/en/download.html)，下载完成后随便解压到你的电脑目录。

### 2.centos安装

要添加CentOS 7 EPEL仓库，请打开终端并使用以下命令：

```sh
1.sudo yum install epel-release --要添加CentOS 7 EPEL仓库
2.sudo yum install nginx --安装Nginx
3.sudo systemctl start nginx --启动Nginx
4.sudo systemctl enable nginx --系统启动时启用Nginx
```



## 2.nginx 服务

方法一

> 双击nginx.exe

方法二

> 打开cmd,进入nginx目录
>
> start nginx --启动nginx
>
> nginx -s stop --停止nginx
>
> nginx -s reload --重启nginx
>
> nginx -s quit --退出nginx



## 3.nginx配置说明

```nginx

```

👆👆👆↑↑↑ 这个配置一般写在第一个，是因为nginx读取配置没有找到匹配的配置之后会默认读取第一个，so 返回404。

```nginx
    server {
            listen       8888;
            server_name  192.168.66.100;
            
            location / {
                proxy_pass  http://127.0.0.1:3000;
            }

            location /file {
                proxy_pass   http://127.0.0.1:3000/file;
            }  
          

            error_page   500 502 503 504  /50x.html;
            location = /50x.html {
                root   html;
            }

        }
```

👆👆👆↑↑↑ 这个配置访问192.168.66.100:8888会被代理到 http://127.0.0.1:3000，192.168.66.100:8888/file 则会代理到http://127.0.0.1:3000/file，通过这个配置可以达到反向代理效果。



**配置socket服务**

```nginx
   server {
        listen 80;
        server_name sockect.test.com;
        location /socket/ {
            proxy_pass http://127.0.0.1:3100;    
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "Upgrade";
        }
	  }
```

配置意思就是使用HTTP1.1协议，遇到websocket协议就转发吧，升级到指定协议（本地验证通过）



获取访问真实IP

```nginx
proxy_set_header X-real-ip           $remote_addr; 
```



## 4.部署spa(single page application) vue、angular

方式一

```nginx
            location / {

                root   html/web;

                try_files $uri $uri/ @router;

                index index.html;

            }

 

            location @router {

                rewrite ^.*$ /index.html last;

            }
```

方式二

```nginx
            location / {

                try_files $uri $uri/ /index.html;

            }
```

方式三

```nginx
            location /weixin {
                proxy_pass  http://127.0.0.1:3001/;
            }  
```
方式三注意**proxy_pass**地址后面跟一个 / 有些时候不加会导致配置不成功（具体原因暂不清楚）

以 `try_files $uri $uri/ /index.php;` 为例，当用户请求 `http://servers.blog.ustc.edu.cn/example` 时，这里的 `$uri` 就是 `/example`。try_files 会到硬盘里尝试找这个文件。如果存在名为 `/$root/example`（其中 `$root` 是 WordPress 的安装目录）的文件，就直接把这个文件的内容发送给用户。显然，目录中没有叫 example 的文件。然后就看 `$uri/`，增加了一个 /，也就是看有没有名为 `/$root/example/` 的目录。又找不到，就会 fall back 到 try_files 的最后一个选项 /index.php，发起一个内部 “子请求”，也就是相当于 nginx 发起一个 HTTP 请求到 `http://servers.blog.ustc.edu.cn/index.php`。这个请求会被 `location ~ \.php$ { ... }` catch 住，也就是进入 FastCGI 的处理程序。而具体的 URI 及参数是在 REQUEST_URI 中传递给 FastCGI 和 WordPress 程序的，因此不受 URI 变化的影响。 

## 小编注

小编在在一开始对nginx有错误认识，误以为server_name会自动在本地产生一个服务。😳😳😳

所以导致访问server_name无法达到预期效果。后面仔细一看终于明白了，nginx只是在本机做一个服务，不会自动启动服务。通过listen 和 server_name 灵活处理我们的服务。