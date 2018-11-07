查看ulimit 的所有参数
```
$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 7959
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 1024
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited

开启系统的core dump,用于排查系统异常所以系统会初始化core_pattern的值。如果想永久设置coredump文件为上面的格式，
开启core file size 
  ulimit -c unlimited 
手动设置core文件的路径及文件格式
echo '/corefiles/core-%e-%p-%t' > /proc/sys/kernel/core_pattern
那么需要使用编辑文件 /etc/sysctl.conf ，加入
kernel.core_pattern=/mydata/corefile/core-%e-%s-%u-%g-%p-%t，
然后使用命令
sysctl -p /etc/sysctl.conf

```
