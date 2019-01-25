zookeeper-3.4.9安装介绍

安装环境
操作系统: CentOS 7
HostIP: 192.168.3.x
Hostname: songm00x

1. 操作系统 /etc/hosts 增加一行
192.168.3.x songm00x
   
2. 解压安装包
tar -zxvf zookeeper-3.4.9.tar.gz

3. 将软件安装到 /home/ 目录下
解压包移动到 /home/ 目录下
rm zookeeper-3.4.9 /home/
cd /home/zookeeper-3.4.9/
mkdir data
mkdir logs

4. 将 ./conf 目录下的  zoo_sample.cfg 文件复制一份，命名为 zoo.cfg
cp zoo_sample.cfg zoo.cfg

6. 修改 zoo.cfg 配置文件
# The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just
# example sakes.
dataDir=/home/zookeeper-3.4.9/data
dataLogDir=/home/zookeeper-3.4.9/logs
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1

server.1=vm_001:2888:3888

7. 在 dataDir 指向的目录下创建 myid 文件，文件输入对应IP机器上输入对应的编号

8. 修改环境变量
export ZOOKEEPER_HOME=/home/zookeeper-3.4.9
export PATH=$ZOOKEEPER_HOME/bin:$PATH

9. 在防火墙中打开要用到的端口2181\2888\3888
chkconfig iptables on
service iptables start
vi /etc/sysconfig/iptables
service iptables restart

10. 启动zookeeper
zkServer.sh start