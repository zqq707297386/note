## Apache配置文件开启 ##

>     LoadModule ssl_module modules/mod_ssl.so
>     
>     LoadModule rewrite_module modules/mod_rewrite.so
>     
>     LoadModule vhost_alias_module modules/mod_vhost_alias.so
>     
>     Include conf/extra/httpd-vhosts.conf

## php.ini中开启 ##

>     extension=php_openssl.dll
>     
>     extension=php_mbstring.dll
>     
>     extension=php_pdo_mysql.dll

## WAMP有2个php.ini文件 ##

>     WAMP\bin\apache\Apache2.4\bin\php.ini （这个是WAMP默认使用的）
>     
>     WAMP\bin\php\php5.5\php.ini （这个才是composer需要的）

## 在Apache配置文件配置站点开启 ##

>     <Directory />
>     AllowOverride all
>     Require all granted
>     </Directory>
>     
>     #Define SRVROOT "E:/amp/Apache24"   我这里定义了常量路径
>     #ServerRoot "${SRVROOT}"
>     
>     所有的AllowOverride改为All就不会出现Not Found 404错误

## 添加本地域名 ##

>     在hosts文件中加   127.0.0.1zqqblog.top

## 在httpd-vhosts.conf中加 ##

>     <VirtualHost *:80>
>     DocumentRoot "${SRVROOT}/htdocs/blog/" 这里站点路径
>     ServerName zqqblog.top 访问的时候直接输入zqqblog.top
>     </VirtualHost>

## 注意：站点指向public则无须动如下文件 ##

>     把　server.php改为index.php
>     伪静态配置文件blog/public下.htaccess复制到入口文件index.php同路径下