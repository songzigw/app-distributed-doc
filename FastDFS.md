

1. 所有跟踪服务器和存储服务器均执行如下操作

yum install make cmake gcc gcc-c++

安装 libfastcommon
上传libfastcommon-master.zip 到 /usr/local/src 目录
cd /usr/local/src/
unzip libfastcommon-master.zip
cd libfastcommon-master/

编译、安装
./make.sh
./make.sh install
libfastcommon 默认安装到了
/usr/lib64/libfastcommon.so
/usr/lib64/libfdfsclient.so

因为FastDFS主程序设置的lib目录是 /usr/local/lib, 所以需要创建软链接
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /user/lib/libfastcommon.so
ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so
ln -s /usr/lib64/libfdfsclient.so /user/lib/libfdfsclient.so

安装 FastDFS
上传 FastDFS源码包 (FastDFS_v5.05.tar.gz)到/usr/local/src目录
cd /usr/local/src/
tar -zxvf FastDFS_v5.05.tar.gz
cd FastDFS

编译、安装
./make.sh
./make.sh install

默认安装
服务脚本
/etc/init.d/fdfs_storaged
/etc/init.d/fdfs_tracker
配置文件在(样例配置文件)
/etc/fdfs/client.conf.sample
/etc/fdfs/storage.conf.sample
/etc/fdfs/tracker.conf.sample
命令工具在/usr/lib目录下
fdfs_*
stop.sh
restart.sh

应为FastDFS 服务脚本设置的bin目录是/usr/local/bin,但是实际命令安装在/usr/bin,可以进入/usr/bin，使用以下命令查看fdsf的相关命令
cd /usr/bin/
ls | gerp fdfs

因此需要修改FastDFS服务脚本中对应的命令路径
/etc/init.d/fdfs_storaged /etc/init.d/fdfs_tracker 两个脚本中的 /usr/local/bin 改成 /usr/bin

配置FastDFS跟踪服务器
复制FastDFS跟踪器样例配置文件,并重命名:
cd /etc/fdfs/
cp tracker.conf.sample tracker.conf
编辑跟踪器配置文件：
vi /etc/fdfs/tracker.conf
修改的内容如下：
disabled=false
port=22122
base_path=/fastdfs/tracker
（其它参数保留默认配置，具体配置解释请参考官方文档说明：
http://bbs.chinaunix.net/thread-1941456-1-1.html ）

创建基础数据目录（参考基础目录base_path配置）:
mkdir -p /fastdfs/tracker

防火墙中打开跟踪器端口（默认为22122）:
vi /etc/sysconfig/iptables
添加如下端口行：
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22122 -j ACCEPT
重启防火墙：
service iptables restart

启动Tracker：
/etc/init.d/fdfs_trackerd start
（初次成功启动，会在/fastdfs/tracker目录下创建data、logs两个目录）
查看FastDFS Tracker是否已成功启动：
ps -ef | grep fdfs

关闭Tracker：
# /etc/init.d/fdfs_trackerd stop

设置FastDFS跟踪器开机启动：
vi /etc/rc.d/rc.local
添加以下内容：
## FastDFS Tracker
/etc/init.d/fdfs_trackerd start


配置FastDFS存储(192.168.4.125)
1、  复制FastDFS存储器样例配置文件,并重命名:
# cd /etc/fdfs/
# cp storage.conf.sample storage.conf
1、  编辑存储器样例配置文件：
# vi /etc/fdfs/storage.conf
修改的内容如下:
disabled=false
port=23000
base_path=/fastdfs/storage
store_path0=/fastdfs/storage
tracker_server=192.168.4.121:22122
http.server_port=8888
（其它参数保留默认配置，具体配置解释请参考官方文档说明：
http://bbs.chinaunix.net/thread-1941456-1-1.html ）
1、  创建基础数据目录（参考基础目录base_path配置）:
# mkdir -p /fastdfs/storage

1、  创建基础数据目录（参考基础目录base_path配置）:
# mkdir -p /fastdfs/storage

1、  防火墙中打开存储器端口（默认为23000）:
# vi /etc/sysconfig/iptables
添加如下端口行：
-A INPUT -m state --state NEW -m tcp -p tcp --dport 23000 -j ACCEPT
重启防火墙：
# service iptables restart

2、  启动Storage：
# /etc/init.d/fdfs_storaged start
（初次成功启动，会在/fastdfs/storage目录下创建data、logs两个目录）
查看FastDFS Storage是否已成功启动
# ps -ef | grep fdfs

1、  关闭Storage：
# /etc/init.d/fdfs_storaged stop

2、  设置FastDFS存储器开机启动：
# vi /etc/rc.d/rc.local
添加：
## FastDFS Storage
/etc/init.d/fdfs_storaged start

