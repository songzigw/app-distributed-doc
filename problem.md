
### 1. Question: ssh: connect to host 192.168.188.177 port 22: Connection refused

出现这个问题的原因是ssh-server服务器没有安装或启动，我的操作系统ubuntu-16.04-desktop-amd64.iso，运行 `ps -e|grep ssh` 查看是否有sshd进程；

通过 `/etc/init.d/ssh -start` 启动server进程，如果提示ssh不存在，那么就是没安装服务，通过 `sudo apt-get install openssh-server`命令安装即可。