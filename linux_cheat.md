## 1. 下载工具
### 多线程下载工具：
axel -n 10 'http://update.sensorsdata.cn/release/update/centos7/sensors-analytics-1.7.2816-centos7/sensors-analytics.tgz' -v -o sensors-analytics.tgz
### 在centos 6上安装axel
rpm -ivh ftp://fr2.rpmfind.net/linux/dag/redhat/el6/en/x86_64/dag/RPMS/axel-2.4-1.el6.rf.x86_64.rpm
### wget 断点续传
wget -c --tries=1000 可以不断尝试多少次



sysctl -w fs.file-max=10485760
sysctl -w fs.nr_open=10485760

<pre>
[root@data01 ~]# sysctl -a | grep fs
保证该配置值最大:
fs.file-max = 10485760
fs.nr_open = 10485760
<pre>





