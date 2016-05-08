# nginx php 搭建 FAQ 

标签（空格分隔）： Wordpress

---
###1. 无法启用php
报错502 Bad Gateway，nginx error log 显示如下：
```php
upstream timed out (10060: A connection attempt failed because the connected

party did not properly respond after a period of time,

or established connection failed because connected host has failed to respond)

while connecting to upstream, client: 127.0.0.1, server: localhost, request: "

GET /php.php HTTP/1.1", upstream: "fastcgi://127.0.0.1:9000", host: "localhost:83"
```
很有可能是php-fastcgi挂了，或是没有启动，  可以将nginx.conf中的：
```shell
fastcgi_pass 127.0.0.1:9000;
```
修改为：
```shell
fastcgi_pass unix:/var/run/php5-fpm.sock; 
```

原因：http://stackoverflow.com/questions/15852884/nginx-connect-failed-error

###2. ubuntu nginx 配置404问题：

如果使用apt-get安装 nginx 默认使用的root路径为
```shell
/usr/share/nginx/html/
```
这个路径被配置在sites-available/default文件中， 导致在nginx.conf中配置root失效，
```
include /etc/nginxsites-enable*;
(enable/default -> available/default)
```