## 1. 使用awk来统计数据的差异
echo $(($(bash get_kafka_offsets_standalone.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')-$(bash get_vertica_offsets.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')))

## 2. 使用awk来统计网络连接状态数
ss -tanp | awk 'BEGIN {FS=" "} {sum[$1]++} END {for (key in sum) print key, sum[key]}'
## 3. 实时监控日志状态、并做一些操作
1. shell 脚本实现
<pre>
#!/bin/bash
file="/sa_standalone/logs/stream_loader/stream_loader.log"
tail -fn0 $file | while read -r line; do
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
