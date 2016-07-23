# Wordpress 迁移 

标签（空格分隔）：Wordpress 

---

##方法一:

1. 将wp-content/plugins，wp-content/themes，wp-content/uploads复制到新的服务器中去。

2. 使用Setting –> export，点击下载导出文件，生成一个xml文件。

![image.png-23.2kB][1]

3. 在新搭建好的WP中，使用Setting –> Import 导入旧WP生成的xml文件。

![image-1.png-10.6kB][2]

注：首先，这里需要先安装Wordpress Importer插件

![image-2.png-8.3kB][3]
4. 完成导入工作，重新设置语言、主题和active插件。

5. 处理图片显示问题，进入WP的数据库中，执行SQL:

```SQL
UPDATE wp_posts SET post_content = replace(post_content, 'http://new_url/new_root/','http://new_url/new_root/');
```
下载并安装语言包：
https://make.wordpress.org/polyglots/teams/


  [1]: http://static.zybuluo.com/happinessnch/d748a3imncl63czjqy6ohpov/image.png
  [2]: http://static.zybuluo.com/happinessnch/2sm6rmyiuduz41axzenkfpqk/image-1.png
  [3]: http://static.zybuluo.com/happinessnch/7ocxujx01e3bnx5xgy9ytg2v/image-2.png