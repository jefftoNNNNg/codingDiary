# 在Linux服务器上配置内网穿透
本文介绍了基于阿里云ECS服务器Linux操作系统，使用ngrok搭建内网穿透服务，用于钉钉/微信小程序开发
> 备注：作者已经将开发环境迁移至vscode remote。暂时不需要ngrok了。本文仅作以前工作的总结归档

## NGROK安装
我们这里采用自己搭建方式，首先在服务器上安装ngrok。作者当初是自己下载源码，自己编译的，有需求随便搜索一下一大把

### 1. 编译
安装好ngrok后，是需要编译服务端和客户端的。编译客户端时候注意，看选择好你的操作系统和CPU架构，比如要注意新出的苹果M1


### 2. 启动服务端
```shell
$ setsid ./bin/ngrokd -tlsKey="assets/server/XXXXX/snakeoil.key" -tlsCrt="assets/server/XXXXX/snakeoil.crt" -domain="ngrok.domain.com" -httpAddr=":5080" -httpsAddr=":5443"
```
端口号是安装的时候自己设置的，记得去阿里云后台解析好自己的域名，笔者当时解析了ngrok和*.ngrok

### 3. 启动客户端
编译好的客户端，需要从服务器上下载下来，使用scp即可
mac用户可自行使用autoscript制作一个启动项
```shell
./ngrok -config ngrok.cfg start-all
```

ngrok.cfg
```
server_addr: "ngrok.domain.com:4443"  
trust_host_root_certs: true
tunnels:
    xxxxxx-ding-xxxxx-api:
        subdomain: xxxx-ding-xxxx-api
        proto:
            http: 1234
    xxxx-ding-xxxx-desktop:
        subdomain: xxxx-ding-xxxx-desktop
        proto:
            http: 5678
    xxxx-api:
        subdomain: xxxx-api
        proto:
            https: 9012
    xxxx-xxxx-test-api:
        subdomain: xxxx-xxxx-test-api
        proto:
            https: 3456
```

### 4. 服务器设置nginx代理
```
server {
    listen 80;
    server_name ngrok.domain.com *.ngrok.domain.com;

    location / {
            proxy_pass http://127.0.0.1:5080;
            proxy_redirect off;
            proxy_set_header Host $http_host:5080;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Nginx-Proxy true;
            proxy_set_header Connection "";
            proxy_connect_timeout 90;
            proxy_send_timeout 120;
            proxy_read_timeout 120;
    }
}
```
记得开ecs安全组的防火墙

### 5. 启动项目，enjoy！

## 钉钉说明
钉钉的后台是可以配置ip地址的，所以在开发模式转为vscode remote以后，就完全不需要ngrok
但是本地开发的时候，我们的ip地址是运营商随机分配的，所以需要把这个不知道什么时候会变的ip地址解析到一个域名上，钉钉的官网是提供ngrok类库的，详细见：[钉钉内网穿透](https://developers.dingtalk.com/document/resourcedownload/http-intranet-penetration)

编译钉钉这种http即可的客户端，openssl做一个签名证书即可

## 微信说明
微信就比较恶心了，因为必须是https，所以要在ngrok基础上配置证书，而且不能openssl自建，否则不认，必须通过第三方，而且这个第三方的证书也需要ngrok，所以就放一起说了

这里使用Let's Encrypt

安装Certbot，官方有步骤，详见：[certbot官方步骤](https://certbot.eff.org/lets-encrypt/centosrhel8-nginx)

中间笔者赶上了一次更新，需要额外装snapd，装snapd比较恶心，这里只是告诉大家，必须装，恶心也没办法

### 1. 做证书
```shell
./certbot certonly -d *.ngrok.domain.com -d ngrok.domain.com --manual --preferred-challenges dns --server https://acme-v02.api.letsencrypt.org/directory
```
这里注意，一定要手打！不要复制！
之后命令行会让填写一些信息，email之类的，最后，会提供一段文字，复制下来去ecs做域名解析
额外要注意，这个证书60天就过期，过期以前会有email

### 2. 做好后，找地方保存好
```shell
$ cd /home/ngrok/
$ cp /etc/letsencrypt/live/open.yuelingnet.cn/privkey.pem /home/ngrok/assets/server/tls/snakeoil.key
$ cp /etc/letsencrypt/live/open.yuelingnet.cn/fullchain.pem /home/ngrok/assets/server/tls/snakeoil.crt
$ cp /etc/letsencrypt/live/open.yuelingnet.cn/fullchain.pem /home/ngrok/assets/client/tls/ngrokroot.crt
$ make release-server
GOOS=linux GOARCH=386 make release-client
GOOS=linux GOARCH=amd64 make release-client
GOOS=windows GOARCH=386 make release-client
GOOS=windows GOARCH=amd64 make release-client
GOOS=darwin GOARCH=386 make release-client
GOOS=darwin GOARCH=amd64 make release-client
GOOS=linux GOARCH=arm make release-client
```

### 3. 这个证书即用于ngrok的客户端编译，也用于nginx的https配置
```
server {
    listen 443 ssl;
    server_name ngrok.domain.com *.ngrok.domain.com;

    keepalive_timeout 70;
    #keepalive_timeout 600;
    ssl_certificate /usr/local/application/ngrok_cert_https/fullchain.pem;
    ssl_certificate_key /usr/local/application/ngrok_cert_https/privkey.pem;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:i!aNULL:!MD5:!RC4:!DHE;
    ssl_prefer_server_ciphers on;

    location / {
        proxy_pass https://127.0.0.1:5443;
        proxy_redirect off;
        proxy_set_header Host $http_host:5443;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Nginx-Proxy true;
        proxy_set_header Connection "";
        proxy_connect_timeout 90s;
        proxy_send_timeout 90s;
        proxy_read_timeout 90s;
        #proxy_connect_timeout 600;
        #proxy_send_timeout 600;
        #proxy_read_timeout 600;
    }
}
```
可以和刚才的80配置共存

### 4. 起服务，服务可以（必须）是http，nginx会转https，enjoy！

### 5. 额外注意
我们在部署生产/测试环境的时候，也必须这样干，因为微信必须https，https我们选了《Let's Encrypt》，生成的证书还是放nginx

但是这时候就不用ngrok的服务了，直接在域名那里解析就好了，比如：xxxx-api.domain.com


## 本文参考
> https://www.jianshu.com/p/c4fa0a378a60
