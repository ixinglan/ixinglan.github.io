---
title: "Let's Encrypt SSL证书ocsp域名无法访问解决办法"
date: 2020-05-15T09:27:52+08:00
draft: false
---
不少公司会用Let's Encrypt 免费ssl证书, 但由于国内无法访问其ocsp域名,会导致不能签发证书、OCSP Stapling 失败、ios内置H5页面及网页打开缓慢等问题  
针对这个问题,有以下两种解决办法:  
1.切换http,但当下https普及的情况下,不推荐  
2.购买SSL证书,可根据访问量及其他综合考虑购买对应证书  
3.本地修改hosts的方案进行临时处理,但不是长久方案,不稳定
在/etc/hosts中添加
```
23.32.3.72     ocsp.int-x3.letsencrypt.org
```
获取ocsp响应
```
openssl ocsp -no_nonce \
                 -respout /path/to/certs/holmesian.org/ocsp_res.der \
                 -issuer /path/to/certs/holmesian.org/ca.cer \
                 -cert /path/to/certs/holmesian.org/holmesian.org.cer \
                 -url http://ocsp.int-x3.letsencrypt.org/ \
                 -header "HOST" "ocsp.int-x3.letsencrypt.org"
#-cert 、 -issuer 、 -CAfile 分别对应的是子证书、中间证书、根证书，其实全部使用 acme.sh 文件夹中的 fullchain.cer 也是可以的
#-respout 是 OCSP 响应保存位置，将这个位置填入在 nginx 配置文件的 ssl_stapling_file 中，如下开启ssl_stapling
```
这里如果出现如下错误的话，说明你的openssl使用了1.1.0版本，这个时候已经不需要指定HOST，把上面命令中的“-header "HOST" "ocsp.int-x3.letsencrypt.org"”删掉就好了
```
ssl_stapling on;
ssl_stapling_verify on;
ssl_stapling_file /root/.acme.sh/*.holmesian.org_ecc/ocsp_res.der;
```
重载nginx服务后,检查是否开启
```
openssl s_client -connect holmesian.org:443 -status -tlsextdebug < /dev/null 2>&1 | grep -i "OCSP response"
```
如果显示successful,则代表成功,可以将期制成脚本,加到crontab中自动执行