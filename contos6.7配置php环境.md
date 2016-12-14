>####centos 6.7 ip配置 使用NAT
##
    cd /etc/sysconfig/network-scripts

    vi ifcfg-eth0

    DEVICE=eth0
    HWADDR=00:0C:29:0E:71:A7
    TYPE=Ethernet
    UUID=9115d0cc-f045-492a-ab69-9264b30650af
    ONBOOT=yes
    NM_CONTROLLED=yes
    BOOTPROTO=dhcp

    重启网络服务
    service network restart

    ifconfig -a 查看ip地址
   
>####LAMP配置部分
##
    软件下载目录cd /usr/local/src 
    软件安装目录/app/local/
    wget http://mirrors.sohu.com/apache/httpd-2.4.18.tar.gz#若是提示没有wget命令则yum -y install wget
    wget http://mirrors.sohu.com/mysql/MySQL-5.6/mysql-5.6.31.tar.gz
    wget http://mirrors.sohu.com/php/php-5.6.12.tar.bz2
    wget http://www.apache.org/dist/apr/apr-1.5.2.tar.gz
    wget http://www.apache.org/dist/apr/apr-util-1.5.4.tar.gz
    wget https://sourceforge.net/projects/pcre/files/pcre/8.39/pcre-8.39.tar.gz
    wget http://xmlsoft.org/sources/libxml2-2.7.4.tar.gz
    wget ftp://mcrypt.hellug.gr/pub/crypto/mcrypt/libmcrypt/libmcrypt-2.5.7.tar.gz
    wget ftp://ftp.cn.debian.org/gentoo/distfiles/zlib-1.2.8.tar.gz
    wget http://zlib.net/zlib-1.2.8.tar.gz
    wget https://sourceforge.net/projects/libpng/files/libpng12/1.2.56/libpng-1.2.56.tar.xz
    wget http://www.ijg.org/files/jpegsrc.v9b.tar.gz
    wget https://sourceforge.net/projects/freetype/files/freetype2/2.4.12/freetype-2.4.12.tar.bz2
    wget https://github.com/libgd/libgd/archive/gd-2.1.1.tar.gz
    wget http://ftp.ntu.edu.tw/gnu/autoconf/autoconf-2.53.tar.gz
    wget https://github.com/libgd/libgd/releases/download/gd-2.1.1/libgd-2.1.1.tar.bz2
    wget https://cmake.org/files/v3.1/cmake-3.1.3.tar.gz

    首先安装相应的库文件：
    yum -y install xz  libXpm-devel lua-devel libxslt-devel gcc gcc-c++libjpeg libjpeg-devel glibc glibc-devel glib2 glib2-devel bzip2 bzip2-devel ncurses ncurses-devel curl curl-devel e2fsprogs e2fsprogs-devel krb5 krb5-devel libidn libidn-devel openssl openssl-devel openldap openldap-devel nss_ldap openldap-clients openldap-servers

    新建相应的目录和用户和用户组：
    [root@zqq /]# mkdir -p /app/local/{php,apache,mysql,data}
    （注意  cat /etc/passwd  可以查看所有用户的列表） （如果有与下面502一样的，就换过）

    cat /etc/group 查看用户组
    groupadd -g 502 www
    useradd -u 502 -g 502 -G 502 -s /sbin/nologin www
    chown -R www:www /app/local/apache/htdocs/
    chmod 755 /app/local/apache/htdocs/

    groupadd mysql
    useradd -g mysql mysql -s /bin/false

    cd /usr/local/src/
    tar zxvf  apr-1.5.2.tar.gz

    cd apr-1.5.2
    ./configure --prefix=/usr/local/apr
    报错提示：cannot remove `libtoolT’: No such file or directory ，
    解决办法：编辑 configure文件，查找 $RM "$cfgfile" 这个地方，用#注释掉，然后就可以了
    ./configure --prefix=/usr/local/apr

    make && make install
    cd ../
    tar zxvf apr-util-1.5.4.tar.gz

    cd apr-util-1.5.4
    ./configure --prefix=/usr/local/apr-util --with-apr=/usr/local/apr/bin/apr-1-config

    make && make install
    cd ../

    yum install -y gcc gcc-c++
    tar zxvf pcre-8.39.tar.gz 

    cd pcre-8.39
    ./configure --prefix=/usr/local/pcre
    make && make install
    cd ../

>####安装Apache
##
    tar zxvf httpd-2.4.18.tar.gz
    cd httpd-2.4.18
    ./configure --prefix=/app/local/apache --enable-mods-shared=all --enable-so --enable-proxy-ajp --enable-rewrite --with-apr=/usr/local/apr --with-apr-util=/usr/local/apr-util --withpcre=/usr/local/pcre

    make && make install
    cd ../
    cd /app/local/apache/conf
    vi httpd.conf
    先配置httpd.conf

    找到;ServerName www.example.com:80
    改为ServerName localhost:80
    /app/local/apache/bin/apachectl -k start
    在浏览器下输入ip地址查看apache是否成功启动
    （如果没有成功的话 可能是selinux和防火墙没有做相应设置）

    vi /etc/sysconfig/iptables
    在端口22下一行加入80 3306端口配置（分别是apache和mysql的端口）
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
    -A INPUT -m state --state NEW -m tcp -p tcp --dport 3306 -j ACCEPT

    esc :wq保存退出

    service iptables restart #重启防火墙使配置生效

    然后在关闭selinux
    vi /etc/sysconfig/selinux
    改为SELINUX=disabled
    esc :wq保存退出

    setenforce 0 #使配置立即生效

>####mysql安装
##
    然后安装mysql,首先安装cmake
    cd /usr/local/src
    tar zxvf cmake-3.1.3.tar.gz
    cd cmake-3.1.3

    make && make install
    cd ../
    tar zxvf mysql-5.6.31.tar.gz
    cd mysql-5.6.31

    make && make install
    cd ../
    chown -R mysql:mysql /app/local/data
    cp /app/local/mysql/support-files/my-default.cnf /etc/my.cnf

    cd /app/local/mysql/
    ./scripts/mysql_install_db --user=mysql --basedir=/app/local/mysql --datadir=/app/local/data

    cp support-files/mysql.server /etc/init.d/mysql
    chkconfig mysql on

    service mysql start
    cd /usr/local/src

>####php安装
##
    tar zxvf libxml2-2.7.4.tar.gz
    cd libxml2-2.7.4

    ./configure --prefix=/usr/local/libxml2
    报错提示：cannot remove `libtoolT’: No such file or directory ，
    解决办法：编辑 configure文件，查找 $RM "$cfgfile" 这个地方，用#注释掉，然后就可以了
    ./configure --prefix=/usr/local/libxml2

    make && make install

    cd ../
    tar zxvf libmcrypt-2.5.7.tar.gz
    cd libmcrypt-2.5.7
    ./configure --prefix=/usr/local/libmcrypt

    make && make install
    tar zxvf zlib-1.2.8.tar.gz
    cd zlib-1.2.8
    ./configure

    make && make install
    cd ../
    xz -d libpng-1.2.56.tar.xz
    tar -xvf libpng-1.2.56.tar
    cd libpng-1.2.56
    ./configure --prefix=/usr/local/libpng

    make && make install
    cd ../
    mkdir -p /usr/local/jpeg6/{bin,lib,include,man}
    mkdir /usr/local/jpeg6/man/man1
    tar zxvf jpegsrc.v9b.tar.gz
    cd jpeg-9b/
    ./configure --prefix=/usr/local/jpeg6/ --enable-shared --enable-static

    make && make install
    cd ../
    tar jxvf freetype-2.4.12.tar.bz2
    cd freetype-2.4.12
    ./configure -prefix=/usr/local/freetype

    make && make install
    cd ../
    tar zxvf autoconf-2.53.tar.gz
    cd autoconf-2.53
    ./configure

    make && make install
    cd ../
    tar jxvf libgd-2.1.1.tar.bz2
    cd libgd-2.1.1
    ./configure --prefix=/usr/local/gd2  --with-zlib=/usr/local/zlib --with-jpeg=/usr/local/jpeg6 --with-png=/usr/local/libpng--with-freetype=/usr/local/freetype/

    make && make install
    cd ../
    tar jxvf php-5.6.12.tar.bz2
    cd php-5.6.12
    ./configure --prefix=/app/local/php --with-config-file-path=/app/local/php/etc --withapxs2=/app/local/apache/bin/apxs --with-mysql --with-libxml-dir=/usr/local/libxml2 --with-pngdir=/usr/local/libpng --with-jpeg-dir=/usr/local/jpeg6 --with-freetype-dir=/usr/local/freetype --withgd=/usr/local/gd2 --with-zlib-dir=/usr/local/zlib --with-mcrypt=/usr/local/libmcrypt --with-mysqli --withxpm-dir=/usr/lib64 --enable-soap --enable-mbstring=all --enable-sockets

    make && make install
    cd ../

>####备份php配置文件

>####cp /usr/local/src/php-5.6.12/php.ini-production /app/local/php/etc/php.ini

##
>####php配置
##
    vi /app/local/php/etc/php.ini
    找到;date.timezone
    改为date.timezone=PRC
    保存退出

>####配置apache解析php
##
    vi /app/local/apache/conf/httpd.conf  
    (按Esc键后 按/ 查找)
    找到;ServerName www.example.com:80
    改为ServerName localhost:80
    找到：AddType application/x-gzip .gz .tgz
    在该行下面添加:AddType application/x-httpd-php .php
    找到DirectoryIndex index.html 
    改为：DirectoryIndex index.html index.htm index.php
    <IfModule dir_module>
    DirectoryIndex index.html index.htm index.php
    </IfModule>
    找到User daemon 。 Group daemon  删除
    然后在后面添加： User www 。 Group www
    找到： Options Indexes FollowSymLinks
    修改为：Options  FollowSymLinks
    找到：AllowOverride None
    修改为：AllowOverride All
    equire all granted这句话在 AllowOverride None 下面几行
    最后保存退出#/app/local/apache/bin/apachectl -t#测试httpd.conf 如果有错误，会有提示, 如果是正确的则显示为 “Syntax OK”
    /app/local/apache/bin/apachectl  start （开启apache服务）
    ps -ef|grep httpd  （查看组）看有没有 2个root，3个www
    vi /app/local/apache/htdocs/info.php
    <?php
    phpinfo();
    ?>
    打开浏览器输入localhost/info.php

>####安装composer
##
    export PATH=$PATH:/app/local/php/bin 
    echo $PATH （有路径就表示可以了）
    php -v有显示表示ok了
    wget https://getcomposer.org/download/1.0.0-alpha11/composer.phar
    mv composer.phar /usr/local/bin/composer （全局安装）
    composer self-update（更新composer）