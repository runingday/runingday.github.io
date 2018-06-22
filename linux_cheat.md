## 1. 下载工具
### 多线程下载工具：
axel -n 10 'http://update.sensorsdata.cn/release/update/centos7/sensors-analytics-1.7.2816-centos7/sensors-analytics.tgz' -v -o sensors-analytics.tgz
### 在centos 6上安装axel
rpm -ivh ftp://fr2.rpmfind.net/linux/dag/redhat/el6/en/x86_64/dag/RPMS/axel-2.4-1.el6.rf.x86_64.rpm
### wget 断点续传
wget -c --tries=1000 可以不断尝试多少次



<pre>
sysctl -w fs.file-max=10485760
sysctl -w fs.nr_open=10485760

[root@data01 ~]# sysctl -a | grep fs
保证该配置值最大:
fs.file-max = 10485760
fs.nr_open = 10485760
</pre>


## 2. How do I put an already-running process under nohup?
	
<pre>
Using the Job Control of bash to send the process into the background:

    Ctrl+Z to stop (pause) the program and get back to the shell.
    bg to run it in the background.
    disown -h [job-spec] where [job-spec] is the job number (like %1 for the first running job; find about your number with the jobs command) so that the job isn't killed when the terminal
</pre>
## 3. add sudo privilege to sa_cluster
<pre>
/etc/sudoers.d/sa_cluster
Defaults:sa_cluster !requiretty
Defaults env_keep += "TZ"
sa_cluster ALL=(ALL) NOPASSWD:ALL
</pre>
