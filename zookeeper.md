## zookeeper-3.4.6 安装

1. 下载安装包

`$ wget http://apache.fayea.com/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz`

2. 解压安装包

`$ tar -zxvf zookeeper-3.4.6.tar.gz`

3. 将解压后的文件zookeeper-3.4.6移动到你规划的目录下，我规划的目录/home/zhangsong/app/

`
$ mv zookeeper-3.4.6 /home/zhangsong/app/
$ cd /home/zhangsong/app/zookeeper-3.4.6/
$ mkdir data
$ mkdir logs
`

5. 将 zookeeper-3.4.6/conf 目录下的 zoo_sample.cfg 文件拷贝，并命名为zoo.cfg

`cp zoo_sample.cfg zoo.cfg`

6. 修改 zoo.cfg 配置文件

