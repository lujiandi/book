#### 添加到 win 服务

在安装的 bin 目录下创建.bat 文件

```
@echo off
cd /d %~dp0
mysqld.exe --install mysql --defaults-file="D:\developer\MySQL Server 5.5\my.ini"
pause

```

要 cmd 运行一些 mysql 的命令行需要把 bin 目录的路径添加到 Path 环境变量

#### 修改 my.ini

```
default-character-set=utf8
character-set-server=utf8
default-storage-engine=INNODB
```

#### mysql 命令

```
mysql -uroot -proot -P3306 -h127.0.0.1//

window 控制台中文注释乱码:
   set names gbk;

\G

show full columns from tableName;

查看索引
show index from tableName
show keys from tableName

查看存贮过程
show procedure status
show create procedure xxxx\G

explain

distinct

ignore

comment

join

on

limit

groud by

engine

current_timestamp();

now();

ifnull();

sum();

count();

max();

```

###3 添加用户示例 添加 mysql 用户 travis

create user travis@localhost;

#### 授权

```
grant all privileges on dataBaseName.tableName to travis@localhost;
grant all privileges on dataBaseName.* to travis@localhost;

查看 travis 用户的权限
show grants for travis@localhost;

```
