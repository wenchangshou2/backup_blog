---
title: ngrok平台搭建
date: 2016-12-26 14:55:27
tags: 运维
---

通过vps来搭建一个ngrok的服务端来进行微信的开发
<!--more-->

# ngrok 搭建
![](http://o7ez1faxc.bkt.clouddn.com/2016-11-13-14790419806988.jpg)

之前反向代理的功能一直使用其他人搭建的服务来进行微信的开发，但是最近一段时间特别的不稳定，正好手里还有一台空闲VPS和域名，所以就萌生自己搭建服务器的念头。
## 编译 ngrok
我的系统是ubuntu 16.04 LTS版本的，在编译之前我们需要安装以下的工具
>sudo apt-get install build-essential golang mercurial git

获取ngrok的源码
>https://github.com/inconshreveable/ngrok.git ngrok
>cd ngrok

在使用之前特地注册了一个域名ngrokc.top来映射vps的IP，在编译的时候我们需要将证书替换成自己新生成的，在创建证书的时候需要将域名修改成自己注册的域名。(之后使用的过程当中会利用这个证书来进行数据的加密，来保证安全性。

``` sh
NGROK_DOMAIN="ngrokc.top"
openssl genrsa -out base.key 2048
openssl req -new -x509 -nodes -key base.key -days 10000 -subj "/CN=$NGROK_DOMAIN" -out base.pem
openssl genrsa -out server.key 2048
openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
openssl x509 -req -in server.csr -CA base.pem -CAkey base.key -CAcreateserial -days 10000 -out server.crt

cp base.pem assets/client/tls/ngrokroot.crt

```
进行上面的操作之后，接下来我们就需要进行编译

``` sh
sudo make release-server release-client

```
操作完成之后在bin目录下面会产生二个可执行的文件ngrokc(服务端)和ngrok(客户端）程序

为了能够让自己的客户端运行在不同的平台上面，我们要根据自己要使用的平台来分别使用下面的某一项指令进行操作。

``` sh
//生成windows客户端

sudo GOOS=windows GOARCH=386 make release-client

//生成Linux客户端


sudo GOOS=linux GOARCH=amd64 make release-client

//生成树莓派客户端

sudo GOOS=linux GOARCH=arm make release-client

```
## 服务端
前面生成的ngrokd就是服务端的程序，我们在运行时需要通过参数来指定证书、域名和端口
> sudo ./bin/ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="ngrokc.top" -httpAddr=":8081" -httpsAddr=":8082"

执行上而把操作之后，我们的服务端也正式启动起来了，可以通过观察屏幕来获得更多的信息。httpAddr、httpsAddr分别是ngokc用来转发http和https的端口，可以根据自己的需要随意更改。同时默认开了一个4443端口用来与客户端的通信。

现在我们可以通过 https://server.ngrokc.top:8081 和 https://server.ngrokc.top:8082 来访问转发的服务，倘若你在浏览器当中输入以上的任意域名会输出下面的语句，表示你的服务已正式启动。
> Tunnel server.ngrokc.top:8081 not found


## 客户端
启动客户端通过可以采用两种方式
1. 直接通过参数的方式来指定需要映射的端口
2. 通过配置文件方式来进行指定相应的端
下面是一个ngrok的配置文件，其中server_addr是指定ngrok服务端的地址和通讯的端口，同时我们也映射了两个tcp的端口。

ngrok.cfg配置文件

```
server_addr: ngrokc.top:4443
trust_host_root_certs: false

tunnels:
  ssh:
    subdomain: server
    proto:
      tcp: 22
  aria2c:
    subdomain: server
    proto:
      tcp: 6800 
```

启动方式

``` sh
#调用下面的语句会自动从服务端生成两个端口用来映射本地的22和6800端口
./ngrok -config=ngrok.cfg start ssh aria2c 
```



### 指定多个配置文件
ngrok可以指定多个配置文件，同时将几个配置文件进行合并然后从第一个配置文件开始逐步进行映射。
>ngrok start -config ~/ngrok.yml -config ~/projects/example/ngrok.yml demo admin

### 启动全部的通道
ngrok可以指定--all和--none来指定启动全部的通道或者不启动任何通道。
> ngrok start --al #启动全部的通道
> ngrok start --none #不启动任何的通道

## 示例配置文件

```
authtoken: 4nq9771bPxe8ctg7LKr_2ClH7Y15Zqe4bWLWF9p
region: us
console_ui: true
compress_conn: false
http_proxy: false
inspect_db_size: 50000000
log_level: info
log_format: json
log: /var/log/ngrok.log
metadata: '{"serial": "00012xa-33rUtz9", "comment": "For customer alan@example.com"}'
root_cas: trusted
socks5_proxy: "socks5://localhost:9150"
update: true
update_channel: stable
web_addr: localhost:4040
tunnels:
 website:
   addr: 8888
   auth: bob:bobpassword
   bind_tls: true
   host_header: "myapp.dev"
   inspect: false
   proto: http
   subdomain: myapp
 
 e2etls:
   addr: 9000
   proto: tls
   hostname: myapp.example.com
   crt: example.crt
   key: example.key
 
 ssh-access:
   addr: 22
   proto: tcp
   remote_addr: 1.tcp.ngrok.io:12345
```
