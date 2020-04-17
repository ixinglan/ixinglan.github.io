---
title: "如何快速在linux成功安装nginx"
date: 2020-04-17T16:27:52+08:00
draft: false
---
​		安装nginx的教程很多,有时候跟着走着走着就走不通了, 这就像一千个读者就有一千个哈姆雷特一样,一千个人就有一千种系统环境,就有一千种步骤. 眼花缭乱的博客有时是不是很头疼,那就自己整理一份吧.

​		这篇文章我们不谈论很多参数的调优,如果不是大型系统的话,默认的配置,跑起来也毫无感觉. 本文我们仅谈如何快速成功在linux系统安装nginx,让你的ngin运行起来.以下即是安装步骤:

## 安装nginx

### 一.安装编译工具及库文件

​		如果是新系统,则下面这些必须安装,如果不是,则你有可能安装过了,安装过了也无所谓,会覆盖;

```shell
yum -y install make zlib zlib-devel gcc-c++ libtool openssl openssl-devel
```

### 二.安装pcre 让nginx⽀持rewrite功能--可以不安装

​		rewrite功能一般也用不着,所以这个可以不安装

```shell
1. cd /usr/local/src/
2. wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-
8.35.tar.gz #可去官网找最新稳定版
3. tar zxvf pcre-8.35.tar.gz
4. cd pcre-8.35
5. ./configure
6. make && make install
7. pcre-config --version #显示版本即成功
```

### 三.安装nginx

​		一般我们最好把nginx安装到/usr/local目录下,如果严格按照我的步骤不报错,那么恭喜安装成功了

```shell
1. cd /usr/local/src
2. wget http://nginx.org/download/nginx-1.16.1.tar.gz --最新稳定版
3. tar zxvf nginx-1.16.1.tar.gz
4. cd nginx-1.16.1
5. ./configure --prefix=/usr/local/nginx --with-http_stub_status_module --
with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.35 
#--with-http_stub_status_module 是https必须的,如果你以后可能用到https访问,这个最好带上,省得以后再次编译
#--with-pcre=/usr/local/src/pcre-8.35 这个是rewrite,在第 二 步没有安装的话这个也就不要带上了
6. make
7. make install
```

## 配置nginx

### 一. 切换到nginx目录

```shell
cd /usr/local/nginx
```

### 二. **conf/nginx.conf --**配置⽂件如下**,**⽇志默认关闭**,https**默认关闭

```nginx
#user  nobody;
worker_processes  1;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;

worker_rlimit_nofile 65535;
events {
    worker_connections  1024;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  127.0.0.1;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;
        gzip on;
        gzip_min_length 1k;
        gzip_comp_level 9;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        gzip_vary on;
        gzip_disable "MSIE [1-6]\.";

				root /usr/local/nginx/html

      	#这个location是我的一个测试工程,可以定义自己的测试loation
        #location ^~ /jeecg-boot {
					#proxy_pass              http://127.0.0.1:8080/jeecg-boot/;
					#proxy_set_header        Host 127.0.0.1;
					#proxy_set_header        X-Real-IP $remote_addr;
					#proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
				#}
      
        location / {
          root   html;
          index  index.html index.htm;
      		#解决Router(mode: 'history')模式下，刷新路由地址不能找到页面的问题, vue项目时用	
          #if (!-e $request_filename) {
            #rewrite ^(.*)$ /index.html?s=$1 last;
            #break;
          #}
        }
        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443 ssl;
    #    server_name  localhost;

    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_cache    shared:SSL:1m;
    #    ssl_session_timeout  5m;

    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers  on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}
}
```

### 三. 测试启动

​		以下我用的是相对路径,需要先切换到安装目录/usr/local/nginx

```shell
1. sbin/nginx -t #测试启动,配置文件有问题时会报错,不影响nginx运行,建议先执行
2. sbin/nginx #启动 有时会提示nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already inuse) 错误, 则通过步骤3进行解决
3. netstat -ntlp #查看并杀掉占⽤的80端⼝, kill pid(进程号)
```











