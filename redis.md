redis-3.0.7安装

安装环境
操作系统: CentOS 7
HostIP: 192.168.3.x
Hostname: songm00x

1. 编译和安装所需要的包
yum install gcc tcl

2. 解压 redis-3.0.7.tar.gz
tar -zxvf redis-3.0.7.tar.gz

3. 进入到解压目录开始安装
cd redis-3.0.7/
使用PREFIX指定安装目录
make PREFIX=/usr/local/redis install

安装完成后，可以看到 /usr/local/redis 目录下有个bin目录，bin目录里就是 redis 的命令脚本
redis-benchmark redis-check-aof redis-check-dump redis-cli redis-server

4. 将redis配置成服务
启动脚本 redis-3.0.7/utils/redis_init_script
将启动脚本复制到 /etc/rc.d/init.d/ 目录下，并命名为 redis
cp redis_init_script /etc/rc.d/init.d/redis
编辑 /etc/rc.d/init.d/redis
vi /etc/rc.d/init.d/redis

```
#!/bin/sh
#chkconfig: 2345 80 90
#
# Simple Redis init.d script conceived to work on Linux systems
# as it does use of the /proc filesystem.

REDISPORT=6379
EXEC=/usr/local/redis/redis-server
CLIEXEC=/usr/local/redis/redis-cli

PIDFILE=/var/run/redis_${REDISPORT}.pid
CONF="/usr/local/redis/conf/${REDISPORT}.conf"

case "$1" in
    start)
        if [ -f $PIDFILE ]
        then
                echo "$PIDFILE exists, process is already running or crashed"
        else
                echo "Starting Redis server..."
                $EXEC $CONF &
        fi
        ;;
    stop)
        if [ ! -f $PIDFILE ]
        then
                echo "$PIDFILE does not exist, process is not running"
        else
                PID=$(cat $PIDFILE)
                echo "Stopping ..."
                $CLIEXEC -p $REDISPORT shutdown
                while [ -x /proc/${PID} ]
                do
                    echo "Waiting for Redis to shutdown ..."
                    sleep 1
                done
                echo "Redis stopped"
        fi
        ;;
    *)
        echo "Please use start or stop as first argument"
        ;;
esac
```

在脚本的第一行后面添加一行内容如下
`#chkconfig: 2345 80 90`
(如果不添加上面的内容，在注册服务时会提示: service redis does not support chkconfig)
修改
REDISPORT=6379
EXEC=/usr/local/redis/bin/redis-server
CLIEXEC=/usr/local/redis/bin/redis-cli
创建配置文件
mkdir /usr/local/redis/conf
cp redis-3.0.7/redis.conf /usr/local/redis/conf/6379.conf
修改
CONF="/usr/local/redis/conf/${REDISPORT}.conf"
更改以后台的方式运行
$EXEC $CONF &

以上配置完成后，便可以将Redis注册成为服务
chkconfig --add redis

防火墙打开对应的端口 6397

修改 redis 配置文件设置
vi /usr/local/redis/conf/6379.conf
daemonize no >> daemonize yes
pidfile /var/run/redis.pid >> pidfile /var/run/redis_6379.pid

启动redis服务
service redis start
