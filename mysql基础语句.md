## 一：数据库创建与查看 ##
    > 查看数据库： show databases;
    > 默认数据库：
    > mysql - 用户权限相关数据
    > test - 用于用户测试数据
    > information_schema - MySQL本身架构相关数据
    > 创建数据库: 
    > create database 数据库名 DEFAULT CHARSET utf8 COLLATE utf8_general_ci;（utf8编码不指定大小写）
    > create database 数据库名 DEFAULT CHARACTER SET gbk COLLATE gbk_chinese_ci;（gbk编码不指定大小写）
    > 使用数据库: use 数据库名;
    > 显示当前使用的数据库中所有表：show tables;
    > 查看表结构：desc 表名;
    > 查看表所有内容：select * from 表名

#### 1:创建表： ####
    create table 表名(
    'id' int(11) NOT NULL auto_increment, （不能为空，自增）
    列名  类型  是否可以为空，
    列名  类型  是否可以为空
    )ENGINE=InnoDB DEFAULT CHARSET=utf8;
#### 2:删除表： ####
    drop table 表名;
    清空表内容: 
    delete from 表名
    truncate table 表名
#### 3:修改表: ####
    添加列：
    alter table 表名 add 列名 类型
    删除列：
    alter table 表名 drop column 列名
    修改列属性: 
    alter table 表名 modify column 列名 新类型; 
    修改列名：  
    alter table 表名 change 原列名 新列名 类型;
    添加主键：   
    alter table 表名 add primary key(列名);
    删除主键：
    alter table 表名 drop primary key;
    alter table 表名  modify  列名 int, drop primary key;
    添加外键：
    alter table 从表 add constraint 外键名称
    （形如：FK_从表_主表）  foreign key 从表(外键字段) references 主表(主键字段);
    删除外键： 
    alter table 表名 drop foreign key 外键名称
    修改默认值：
    ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;
    删除默认值：
    ALTER TABLE testalter_tbl ALTER i DROP DEFAULT;
#### 4：对表内容的操作 ####
    1、增
    insert into 表名 (列名,列名...) values (值,值,...);
    insert into 表名 (列名,列名...) values (值,值,...),(值,值,值...);
    insert into 表名 (列名,列名...) select (列名,列名...) from 表名;
    例：
    insert into tab1(name,email) values('zhengqiqiang','707297386@qq.com');

    2、删 (谨慎处理 记得带条件)
    delete from 表名 where id＝1 and name＝'zhengqiqiang';
    （删除ID =1 和name='zhangyanlin' 那一行数据）

    3、改 (谨慎处理 记得带条件)
    update 表 set name ＝ 'zhengqiqiang' where id>1;

    4、查
    select * from 表
    select * from 表 where id > 1
    select id,name,gender as gg from 表 where id > 1

    a、条件判断where
    select * from 表 where id > 1 and name != 'zqq' and num = 12;
    select * from 表 where id between 5 and 16;
    select * from 表 where id in (11,22,33)
    select * from 表 where id not in (11,22,33)
    select * from 表 where id in (select nid from 表)

    b、通配符like
    select * from 表 where name like 'zhang%'   zhang开头的所有（多个字符串）
    select * from 表 where name like 'zhang_'   zhang开头的所有（一个字符）

    c、限制limit
    select * from 表 limit 5;- 前5行
    select * from 表 limit 4,5;  - 从第4行开始的5行
    select * from 表 limit 5 offset 4- 从第4行开始的5行

    d、排序asc，desc
    - 根据 “列” 从小到大排列
    select * from 表 order by 列 asc

    - 根据 “列” 从大到小排列 
    select * from 表 order by 列 desc

    - 根据 “列1” 从大到小排列，如果相同则按列2从小到大排序
    select * from 表 order by 列1 desc,列2 asc 

    e、分组group by
    select num from 表 group by num
    select num,nid from 表 group by num,nid
    select num,nid from 表  where nid > 10 group by num,nid order nid desc
    select num,nid,count(*),sum(score),max(score),min(score) from 表 group by num,nid
    select num from 表 group by num having max(id) > 10

>#### 特别的：group by 必须在where之后，order by之前 ####

#### 四：用户管理 ####
    创建用户:  
    create user '用户名'@'IP地址' identified by '密码';

    删除用户: 
    drop user '用户名'@'IP地址';

    修改用户: 
    rename user '用户名'@'IP地址'; to '新用户名'@'IP地址';

    修改密码:
    set password for '用户名'@'IP地址' = Password('新密码');
#### 五：权限管理 ####
    查看所用用户: show grants;

    对于数据库及内部其他权限如下：
    数据库名.*  数据库中的所有
    数据库名.表 指定数据库中的某张表
    数据库名.存储过程 指定数据库中的存储过程
    *.*  所有数据库

    对于用户和IP的权限如下：
    用户名@IP地址用户只能在改IP下才能访问
    用户名@192.168.1.% 用户只能在改IP段下才能访问通配符%表示任意)
    用户名@%用户可以再任意IP下访问(默认IP地址为%)

    查看权限：show grants for '用户'@'IP地址';

    授权: grant 权限 on 数据库.表 to '用户'@'IP地址';

    取消授权: revoke 权限 on 数据库.表 from '用户'@'IP地址';

    授权实例:
    grant all privileges on db1.tb1 TO '用户名'@'IP';
    grant select on db1.* TO '用户名'@'IP';
    grant select,insert on *.* TO '用户名'@'IP';
    revoke select on db1.tb1 from '用户名'@'IP';