---
title: wordpress FAQ
date: 2016-05-13 09:58:10
tags: wordpress
categories: blog
---
# 插件

1. 插件SyntaxHighlighter Evolved 代码高亮

2. 主题 iNove（修改sidebar）

3. WordPress URL & Website URL 设置

4. 修改permalink自定义结构 /folder/%post_id%.html

5. 安装Windows Live Writer离线Blog
安装Windows Live Writer Source Code plugin for SyntaxHighlighter http://files.cnblogs.com/cmt/SourceCodePlugin_version_1.1.zip

6. 安装wp-postviews插件，修改theme的index.php，添加

```html
// 在主循环中添加 the_views统计文章访问量

// 主循环： 
if (have_posts()): while (have_posts()) : the_post(); update_post_caches($posts);
<?php if(function_exists('the_views')) { the_views(); } ?>

// 在合适的位置统计总数量
<?php if(function_exists('get_totalviews')) { get_totalviews();} ?>
```

7\. Jetpack 提供查看网站流量， 社交，评论分享等功能。

---
# 将Wordpress放置到子目录
1\. WordPress地址修改为新地址，Site Address修改不变，
比如WP URL: http://h2nachuang.cn/blog/，Site URL : http://h2nachuang.cn/

2\. 将index.php和.htaccess拷贝到根目录（注意：这里是拷贝，不是剪切！），然后修改
```php
index.phprequire( dirname( __FILE__ ) . ‘/wp-blog-header.php’  );
```
例如，wordpress核心文件放到了/wwwroot/blog/下，那么应该修改成：
```php
require( dirname( __FILE__ ) . ‘/blog/wp-blog-header.php’ );
```
3\. 修改完后，如果还想修改WP的设置，那么要登录 http://h2nachuang.cn/blog/wp-admin/，URL进行修改。

4\. 如果设置过Permalinks记得要更新一下，WP会自动更新.htaccess文件，如果权限允许的话。不允许的话，就需要将新的rewrite的内容手动的复制到.htaccess中。

---
# Wordpress 404 问题

点击设置->固定链接->保存更改（Setting->Permalink->Change Save）
也可以先Deactivate所有的Plugins在逐一打开查看问题。
看问题是否解决，如果没有继续检查。


### Apache 

1. wordpress 安装目录下是否存在.htaccess文件，并注意.htaccess是否有写权限。

2. 打开AllowOverride，在配置apache2.conf / httpd.conf中：

```html
// 将AllowOverride None修改为All 
<Directory /> 
Options FollowSymLinks 
AllowOverride All 
</Directory>

<Directory /var/www/> 
Options Indexes FollowSymLinks 
AllowOverride All 
</Directory> 
```
打开mod_rewrite:

```shell
LoadModule rewrite_module modules/mod_rewrite.so  
/etc/init.d/apache2 restart
```
---
### Nginx 
```shell
location / { 
    try_files $uri $uri/ /index.php?$args; 
} 
```
重启并保存设置：
```shell
nginx –s reload
```
```
WP->Setting->Change Save
```
启动WP的相关服务
```shell
service mysql start
/etc/init.d/apache2 restart 
```

---
# Theme 

iNove主题（作者NeoEase）
主题右侧冗余的menu栏，通过修改sidebar.php可以去除。

其他WP主题: https://www.zhihu.com/question/19746780
