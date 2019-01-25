mysql-5.7.20安装介绍

安装环境
操作系统: CentOS 7
HostIP: 192.168.3.x

下载安装包
https://dev.mysql.com/downloads/mysql/#downloads

1. 解压mysql-5.7.20-linux-glibc2.12-x86_64.tar.gz
tar -zxvf mysql-5.7.20-linux-glibc2.12-x86_64.tar.gz

2. 移动解压后的文件到 /usr/local 并且命名为 mysql
mv mysql-5.7.20-linux-glibc2.12-x86_64 /usr/local/mysql、

3. 添加 mysql 用用户和所属组
groupadd mysql
useradd -r -g mysql mysql

4. 更改 mysql 目录所属的组和用户
chown -R mysql /usr/local/mysql
chgrp -R mysql /usr/local/mysql

5. 初始化数据库
bin/mysql_install_db --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data

6. 安装并启动
/etc 目录下增加 my.cnf 文件
vi my.cnf

内容配置如下

```
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html

[mysql]
port = 3306
default-character-set=utf8mb4
socket=/usr/local/mysql/mysql.sock

[client]
port = 3306
default-character-set=utf8mb4
socket=/usr/local/mysql/mysql.sock

[mysqld]

# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M

# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin

# These are commonly set, remove the # and set as required.
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
port = 3306
# server_id = .....
socket = /usr/local/mysql/mysql.sock
tmpdir = /tmp
pid-file=/usr/local/mysql/mysqld.pid

# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M 

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES 
```
添加服务
cp -a ./support-files/mysql.server  /etc/init.d/mysqld

开始启动
service mysqld start
查看状态
service mysqld status

7. 初始化密码,以及添加远程访问
查看系统默认密码
cat /root/.mysql_secret
./bin/mysql -uroot -p
Enter password:

mysql> select version();
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql> SET PASSWORD = PASSWORD('123456');
Query OK, 0 rows affected, 1 warning (0.00 sec)


mysql> select version();
+-----------+
| version() |
+-----------+
| 5.7.20    |
+-----------+
1 row in set (0.00 sec)


mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A


Database changed
mysql> select host, user from user;
+-----------+---------------+
| host      | user          |
+-----------+---------------+
| localhost | mysql.session |
| localhost | mysql.sys     |
| localhost | root          |
+-----------+---------------+
3 rows in set (0.00 sec)


mysql>  update user set host = '%' where user = 'root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0


mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)


mysql>quit;

8. 设置环境变量

9. 设置开机启动

10. 开放防护墙端口