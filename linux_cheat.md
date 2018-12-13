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

## 4. 针对SSD盘做读优化
```

Improving Read performance of Disks using 'blockdev'
The Read performance of a Disk can be improved by increasing a parameter called "Read+Ahead" using 'blockdev' command. By default the Linux OS will read 128 KB of data in advance so that it is already in Memory cache before the program needs it. This value can be increased so as to get better Read Performance.

Steps to check and increase the Read-Ahead value:

To check the current 'blockdev' status of all block device:   # blockdev --report
[root@linuxserver ~]# blockdev --report
RO    RA   SSZ   BSZ   StartSec     Size    Device
rw 16384   512  4096          0   14680064  /dev/sda
rw 16384   512  1024         63     208782  /dev/sda1
rw 16384   512   512     208845   10265535  /dev/sda2
rw 16384   512  4096          0 2147483648  /dev/sdb
rw 16384   512  4096          0   35651584  /dev/sdc
rw 16384   512  2048         63   35648172  /dev/sdc1
rw 16384   512  4096          0  104857600  /dev/sdd

To check the 'Read-Ahead' value of an individual disk (let's take sda)
# blockdev --getra /dev/sda
(or)
# cat /sys/block/sda/queue/read_ahead_kb

To change the 'Read-Ahead' value to 8 MB (16384 times of 512 bytes blocks).
# blockdev --setra 16384 /dev/sda
# blockdev --setra 2048 /dev/sda  (1MB)

To make it permanent upon system reboot, just add this command entry in /etc/rc.local.

```
