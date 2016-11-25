**切换为超级管理员**

>####sudo passwd （密码）

**查看ubuntu版本**

>####lsb_release -a

更新安装包，如果这里出错，请更换镜像

>####sudo apt-get update

**ubuntu语言包避免系统引起的语言问题**

>####sudo apt-get install -y language-pack-en-base

**设置语言(需要root权限)阿里云上要设置语言。不然报错**

>####locale-gen en_US.UTF-8

**安装vim git跟cpu查看工具**

>####sudo apt-get install -y vim htop git

 **ppa是个人软件包存档是默认情况下并未被包含在Ubuntu中的软件的一个集合**

>####sudo apt-get install -y software-properties-common

>####sudo LC_ALL=en_US.UTF-8 add-apt-repository ppa:ondrej/php

**再次更新（如果出错可能是网络问题）**

>####sudo apt-get update

**安装nginx服务器**

>####sudo apt-get install nginx

>####如果成功直接在浏览器输入localhost则会显示nginx欢迎界面

**查看php的包。注：不代表全都已经安装**

>####apt-cache search php7.1

**安装php7.1**

>####sudo apt-get -y install php7.1

>####sudo apt-get -y install php7.1-mysql

**安装php文件解析**

>####sudo apt-get -y install php7.1-fpm

**安装所需扩展包**

>####sudo apt-get -y install php7.1-curl php7.1-xml php7.1-mcrypt php7.1-json php7.1-gd php7.1-mbstring

**安装mysql5.6**

>####sudo apt-get install mysql-server-5.6

**修改配置**

>####sudo vim /etc/php/7.1/fpm/php.ini

>####找到cgi.fix_pathinfo 设为0 （这样防止上传的图片是恶意代码攻击）

>####sudo vim /etc/php/7.1/fpm/pool.d/www.conf

>####找到listen= 并改为listen = /var/run/php7.1-fpm.sock

>####创建 www目录  mkdir /var/www （把/var/www设为根目录）

## 配置nginx服务器 ##

>####sudo vim /etc/nginx/sites-available/default

修改成如下:

    server {
    
    listen 80 default_server;
    
    listen [::]:80 default_server ipv6only=on;
    
    root  /var/www/blog;（根目录）
    
    index index.php index.html index.htm;
    
    server_name localhost;  （上线后这里要改）
    
    location / {
    
    try_files $uri $uri/ /index.php?$query_string;
    
    }
    
    location ~ \.php$ {
    
    try_files $uri /index.php =404;
    
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    
    fastcgi_pass unix:/var/run/php7.1-fpm.sock;
    
    fastcgi_index index.php;
    
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    
    include fastcgi_params;
    
    }

**打开github创建项目**

>####然后推送项目    git init

>####git add ./

>####git commit -m "注释"

>####把github页面中…or push an existing repository from the command line  下面的两行复制到git上去

**然后下载composer把下面四行复制到终端**

    1：php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
    
    2：php -r "if (hash_file('SHA384', 'composer-setup.php')
    
    ==='e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
    
    3：php composer-setup.php
    
    4：php -r "unlink('composer-setup.php');"
    全局安装composer
    mv composer.phar /usr/local/bin/composer

**查看composer是否安装成功**

>####composer -v (成功则显示composer)

>####cd 项目名 执行composer install

**在项目根目录下创建.env文件**

>####vim .env （复制如下代码）

    APP_ENV=local （上线后改为production）
    
    APP_DEBUG=true （上线后改为false）
    
    APP_KEY=iCfdjsHjdgoBp5HS9JmDNWyR1CCSmsu3（上线后执行php artisan key:generate 重新生成key）
    
    APP_LOG_LEVEL=debug
    
    APP_URL=http://localhost （上线后改）
    
    DB_CONNECTION=mysql
    
    DB_HOST=127.0.0.1
    
    DB_PORT=3306
    
    DB_DATABASE=blog（数据库名）
    
    DB_USERNAME=root （数据库帐号）
    
    DB_PASSWORD=root （数据库密码）
    
    DB_PREFIX=blog_  （表前缀）
    
    CACHE_DRIVER=file
    
    SESSION_DRIVER=file
    
    QUEUE_DRIVER=sync
    
    REDIS_HOST=127.0.0.1
    
    REDIS_PASSWORD=null
    
    REDIS_PORT=6379
    
    MAIL_DRIVER=smtp
    
    MAIL_HOST=mailtrap.io
    
    MAIL_PORT=2525
    
    MAIL_USERNAME=null
    
    MAIL_PASSWORD=null
    
    MAIL_ENCRYPTION=null

**按esc然后:wq保存退出**

**给文件775权限**

>####sudo chmod -R 775 storage/

**所属用户组**

>####sudo chown -R www-data:www-data /var/www/

>####sudo chown -R www-data:www-data /var/www/项目名

**重新读取配置文件**

>####sudo service nginx reload

**重启nginx服务**

>####sudo service nginx restart

**重启nginx解析php文件的包**

>####sudo service php7.1-fpm restart