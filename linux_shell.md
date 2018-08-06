## 1. 使用awk来统计数据的差异
echo $(($(bash get_kafka_offsets_standalone.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')-$(bash get_vertica_offsets.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')))

## 2. 使用awk来统计网络连接状态数
ss -tanp | awk 'BEGIN {FS=" "} {sum[$1]++} END {for (key in sum) print key, sum[key]}'
## 3. 实时监控日志状态、并做一些操作
1. shell 脚本实现
<pre>
#!/bin/bash
file="/sa_standalone/logs/stream_loader/stream_loader.log"
#tail -f      等同于--follow=descriptor，根据文件描述符进行追踪，当文件改名或被删除，追踪停止
#tail -F     等同于--follow=name  --retry，根据文件名进行追踪，并保持重试，即该文件被删除或改名后，如果再次创建相同的文件名，会继续追踪
#tailf        等同于tail -f -n 10（貌似tail -f或-F默认也是打印最后10行，然后追踪文件），与tail -f不同的是，如果文件不增长，它不会去访问磁盘文件，所以tailf特别适合那些便携机上跟踪日志文件，因为它减少了磁盘访问，可以省电
tail -Fn0 $file | while read -r line; do
        echo "$line" | grep "Ros Too Many ERROR" >> /sa_standalone/runtime/sl.log 2>&1
        if [ $? = 0 ]
        then
                echo "Error discovery" >> /sa_standalone/runtime/sl.log 2>&1
                echo "select make_ahm_now()" | sa_vsql >> /sa_standalone/runtime/vert.log 2>&1
                echo "select do_tm_task('mergeout', 'event_p1')" | sa_vsql >> /sa_standalone/runtime/vert.log 2>&1
                echo "select purge()" | sa_vsql >> /sa_standalone/runtime/vert.log 2>&1
        fi
        echo "$line" | grep "totalTps" >> /sa_standalone/runtime/sl.log 2>&1
        if [ $? = 0 ]
        then
                echo "all right" >> /sa_standalone/runtime/sl.log 2>&1
        fi
done
</pre>
2. 通过一句话脚本执行
<pre>
tail -fn0 logfile | awk '/pattern/ { print | "command" }'
</pre>

3. 正确匹配ip地址
<pre>
[sa_cluster@data01 nginx]$ echo '12.11.1.111' | egrep -o -w "([1-9][0-9]{0,2}[\.]){3}[1-9][0-9]{0,2}" --color
12.11.1.111
</pre>
4. awk 使用split 
<pre>
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[1]}'
I
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[2]}'
have
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[3]}'
an
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[4]}'
apple
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array,"")} END {print array[3]}'
h
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array,"")} END {print array[5]}'
</pre>

5. awk 使用dict赋值
<pre>
sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key ":" my_dict[key]}}'
</pre>
