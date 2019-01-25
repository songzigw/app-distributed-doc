activemq-5.13.0安装介绍

安装环境
操作系统: CentOS 7
HostIP: 192.168.3.x
Hostname: songm00x
java version "1.8.0_121"

1. 下载 ActiveMQ 安装包 apache-activemq-5.13.0-bin.tar.gz

2. 解压安装
tar -zxvf apache-activemq-5.13.0-bin.tar.gz

3. 将软件安装到 /home/ 目录下
解压包移动到 /home/ 目录下
rm apache-activemq-5.13.0 /home/
cd /home/apache-activemq-5.13.0/

4. 开启防火中对应的端口
一个是消息通讯的端口（61616）
一个是管理控制台端口（8161）
可以在 conf/activemq.xml conf/jetty.xml 中修改

防火墙设置
vi /etc/sysconfig/iptables
-A INPUT -m state --state NEW -m tcp -p tcp --dport 61616 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 8161 -j ACCEPT
重启动防火墙
service iptables restart

5. 启动ActiveMQ
cd /home/apache-activemq-5.13.0/bin
./activemq start

6. 打开管理界面 
http://192.168.3.x:8161

7. 安全策略配置
http://activemq.apache.org/security.html
设置管控台登入密码
设置客户端收发消息的账号密码