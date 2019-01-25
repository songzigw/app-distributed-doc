Red5流媒体服务器安装教程

1. 下载red5
下载地址 https://github.com/Red5/red5-server/releases
源文件 red5-server-1.0.10-M10.tar.gz

2. 解压启动
解压
tar -zxvf red5-server-1.0.10-M10.tar.gz
将解压后的文件移动到 /usr/local 并从命名为 red5
mv red5-server /usr/local/red5
启动
cd /usr/local/red5
./red5.sh

3. 添加服务启动项
vi /etc/init.d/red5
将以下shell脚本copy到刚才打开的red5文件中

```
    #!/bin/bash  
    # For RedHat and cousins:  
    # chkconfig: 2345 85 85  
    # description: Red5 flash streaming server  
    # processname: red5  
    # Created By: zhangsong (songmzi@163.com)  
      
    PROG=red5  
    RED5_HOME=/usr/local/red5 
    DAEMON=$RED5_HOME/$PROG.sh  
    PIDFILE=/var/run/$PROG.pid  
      
    # Source function library  
    . /etc/rc.d/init.d/functions  
      
    [ -r /etc/sysconfig/red5 ] && . /etc/sysconfig/red5
      
    RETVAL=0  
      
    case "$1" in  
    start)  
    echo -n $"Starting $PROG: "  
    cd $RED5_HOME  
    $DAEMON >/dev/null 2>/dev/null &  
    RETVAL=$?  
    if [ $RETVAL -eq 0 ]; then  
    echo $! > $PIDFILE  
    touch /var/lock/subsys/$PROG  
    fi  
    [ $RETVAL -eq 0 ] && success $"$PROG startup" || failure $"$PROG startup"  
    echo  
    ;;  
    stop)  
    echo -n $"Shutting down $PROG: "  
    killproc -p $PIDFILE  
    RETVAL=$?  
    echo  
    [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/$PROG  
    ;;  
    restart)  
    $0 stop  
    $0 start  
    ;;  
    status)  
    status $PROG -p $PIDFILE  
    RETVAL=$?  
    ;;  
    *)  
    echo $"Usage: $0 {start|stop|restart|status}"  
    RETVAL=1  
    esac  
      
    exit $RETVAL
```