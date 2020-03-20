# nginx https配置

## 1.nginx配置https前准备



1.先去申请证书，可以付费的也可以免费，我用了一个免费证书 [freessl](https://freessl.org/)，如下所示，我们填入需要申请的域名和选择品牌，然后创建SSL证书。

![](C:\Users\qing\Pictures\Camera Roll\freeSSL.PNG)



2.创建成功后会弹出如下界面

![](C:\Users\qing\Pictures\Camera Roll\generatro key.PNG)

配置如图所示，这里我们有csr所以吧csr填入表单。如果需要这里是生成csr [MySSL](https://myssl.com/csr_create.html)。填写好资料生产，**这里需要注意，把csr文件和key文件保存下来，后面证书需要用到。**

![](C:\Users\qing\Pictures\Camera Roll\csr&ke.PNG)

把csr内容复制到证书申请网站表单，然后点击创建。

创建完成后需要对域名校验，这里是为了确认是你自己的域名。校验完成后下载证书。

## 注！！！

通过 MySSL生产的csr填写的域名需要和你freessl填写的域名一致。

eg：

> MySSL --- api.xxxx.com  |  freessl --- api.xxxx.com
>
> MySSL --- web.xxxx.com  |  freessl --- web.xxxx.com
>
> MySSL --- www.xxxx.com  |  freessl --- www.xxxx.com

这里要对应起来，不然浏览器会报证书不安全，那么表示https配置有问题。这时候在小程序中是无法访问的。

## 2.nginx配置https

centoshttps配置如下

![](C:\Users\qing\Pictures\Camera Roll\centos ssl.PNG)

full_chain.pem是申请下来的证书

SSL是生成的私钥 （MySSL 生产的，如有疑问，请看MySSL的生成 ）

