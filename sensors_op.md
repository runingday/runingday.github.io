# impala 查看partitions 信息
show partitions event_ros_p39;

day   | event_bucket | #Rows | #Files | Size    | Bytes Cached | Cache Replication | Format  | Incremental stats | Location                                                                                        |
|-------|--------------|------|--------|---------|--------------|-------------------|---------|-------------------|-------------------------------------------------------------------------------------------------|
| 17380 | 3            | -1    | 3      | 15.93KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17380/3/.snapshot/20170809_102906-0-1502245746601  |
| 17380 | 10           | -1    | 3      | 14.56KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17380/10/.snapshot/20170809_102906-0-1502245746601 |
| 17380 | 11           | -1    | 3      | 14.68KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17380/11/.snapshot/20170809_102906-0-1502245746601 |
| 17385 | 0            | -1    | 1      | 9.60KB  | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/0/.snapshot/20170808_014156-0-1502127716911  |
| 17385 | 1            | -1    | 3      | 17.14KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/1/.snapshot/20170807_235233-0-1502121153396  |
| 17385 | 2            | -1    | 1      | 9.75KB  | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/2/.snapshot/20170808_014219-0-1502127739598  |
| 17385 | 3            | -1    | 4      | 24.78KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/3/.snapshot/20170808_012715-0-1502126835050  |
| 17385 | 4            | -1    | 3      | 16.13KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/4/.snapshot/20170807_235233-0-1502121153396  |
| 17385 | 5            | -1    | 4      | 24.23KB | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/5/.snapshot/20170807_235233-0-1502121153396  |
| 17385 | 6            | -1    | 1      | 7.22KB  | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/6/.snapshot/20170807_205331-0-1502110411007  |
| 17385 | 7            | -1    | 1      | 6.71KB  | NOT CACHED   | NOT CACHED        | PARQUET | false             | hdfs://data01.yinke.sa:8020/sa/data/39/event/17385/7/.snapshot/20170807_205353-0-1502110433779  |

mysql> select bucket_id, count(*) as event_count from event_define group by bucket_id;

| bucket_id | event_count |
|-----------|-------------|
|      NULL |           7 |
|         0 |         249 |
|         1 |         244 |
|         2 |         241 |
|         3 |         239 |
|         4 |         237 |
|         5 |         234 |
|         6 |         227 |
|         7 |         219 |
|         8 |         217 |
|         9 |         215 |
|        10 |          38 |
|        11 |          19 |
|        12 |          17 |
|        13 |          15 |
|        14 |           7 |

16 rows in set (0.00 sec)

# scheduler 任务堆积
job_record

### 1. 停止scheduler模块，然后备份job_record表 
~/sa/mysql/bin/mysqldump -uroot -p密码 -S ~/sa/mysql/mysqld.sock metadata job_record > /tmp/job_record.sql
### 2. 然后清空job_record表
truncate table job_record 
### 3. 启动服务 scheduler

mysql 查看数据类型 
NUMBER(1), STRING(2), LIST(3), DATE(4), DATETIME(5), BOOL(6), UNKNOWN(-1);

### 4. 修改kafka offset
<pre>
function zk_conf_kv_get() {
    local m=$1 # module
    local t=$2 # type (server/client)
    local n=$3 # key
    local conf_value=$(monitor_tools get_config -m $m -t $t -n $n 2>&1 | awk -F '=' '{print $2}' | head -n 1 | awk -F '"' '{print $2}')
    echo $conf_value
}
BROKER_LIST=$(zk_conf_kv_get kafka client broker_list)
TOPIC_NAME=$(zk_conf_kv_get kafka client topic_name)
# get the oldest offset from kafka (--time -2 is the oldest offset)
function kafka_get_end_offset() {
        local p_id=$1  #partition_id
        local kafka_offset_value=$(kafka-run-class kafka.tools.GetOffsetShell --topic ${TOPIC_NAME} --broker-list ${BROKER_LIST} --time -2 --partition $p_id 2>&1| grep event_topic:$p_id | awk -F ':' '{print $3}')
        echo "partition $p_id kafka offset:" $kafka_offset_value >&2
        echo $kafka_offset_value
}
#compare the offset between batch_loader and kafka
function compare_offset() {
        local p_id=$1 #partition_id
        local bl_offset_value=$(echo "select end_offset from batch_loader_kafka_progress where process_partition in (select max(process_partition) from batch_loader_kafka_progress where kafka_partition_id = $p_id) and kafka_partition_id=$p_id;" | sa_mysql 2>&1 | grep [[:digit:]+])
        echo "partition $p_id batch_loader offset:" $bl_offset_value >&2
        local kafka_offset_value=$(kafka_get_end_offset $p_id)
        #if batch_loader progress is bigger than kafka progress, then you need modify the batch_loader progress
        if (( $bl_offset_value < $kafka_offset_value )); then
                echo -e "\033[31mYou should modify the offset of this partition $p_id...\nupdate batch_loader_kafka_progress set  end_offset="$kafka_offset_value"  where process_partition = (select * from (select max(process_partition) from batch_loader_kafka_progress where kafka_partition_id = $p_id) as tmpCol) and
kafka_partition_id=$p_id; \033[0m"
        else
                echo -e "\033[32mpartition $p_id batch_loader_offset_value is larger than kafka_offset_value, so no need to update \033[0m"
        fi
}
function modify_offset() {
        local p_id=$1 #partition_id
        local bl_offset_value=$(echo "select end_offset from batch_loader_kafka_progress where process_partition in (select max(process_partition) from batch_loader_kafka_progress where kafka_partition_id = $p_id) and kafka_partition_id=$p_id;" | sa_mysql 2>&1 | grep [[:digit:]+])
        echo "partition $p_id batch_loader offset:" $bl_offset_value >&2
        local kafka_offset_value=$(kafka_get_end_offset $p_id)
        #if batch_loader progress is bigger than kafka progress, then you need modify the batch_loader progress
        if (( $bl_offset_value < $kafka_offset_value )); then
                echo -e "\033[31mNow, starting to modify the offset of partition $p_id...\nupdate batch_loader_kafka_progress set  end_offset="$kafka_offset_value"  where process_partition = (select * from (select max(process_partition) from batch_loader_kafka_progress where kafka_partition_id = $p_id) as tmpCol) and kafka_partition_id=$p_id; \033[0m"
                echo "update batch_loader_kafka_progress set  end_offset="$kafka_offset_value"  where process_partition = (select * from (select max(process_partition) from batch_loader_kafka_progress where kafka_partition_id = $p_id) as tmpCol) and kafka_partition_id=$p_id;" | sa_mysql 2>&1 >/tmp/modify_kafka_offset.log
        else
                echo -e "\033[32mpartition $p_id batch_loader_offset_value is larger than kafka_offset_value, so no need to update \033[0m"
        fi
}
#This function will control check/modify operation. 
function magic_execute() {
        config_file=$SENSORS_ANALYTICS_HOME/conf/sensors_analytics.property
        num=`grep 'sensors_analytics.max_node_num' $config_file | awk -F'=' '{print $2}'`
        func=$1
        if [ $num == 1 ]
        then
            if [ $func == "check" ]
            then
                for i in {0..2}; do
                    compare_offset $i
                done
            fi
            if [ $func == "modify" ]
            then
                echo -e "\033[32m.......................................................................... \033[0m"
                echo
                echo -e "\033[31mPlease check whether the moduler extractor/batch_loader stopped first... \033[0m"
                echo
                echo -e "\033[33m.......................................................................... \033[0m"
                read -ep "$prompt" modify_tag
                if [ $modify_tag == "y" ] || [ $modify_tag == "yes" ]
                then
                        for i in {0..2};do
                                modify_offset $i
                        done
                fi
            fi
        else
            if [ $func == "check" ]
            then
                for i in {0..9}; do
                    compare_offset $i
                done
            fi
            if [ $func == "modify" ]
            then
                echo -e "\033[32m.......................................................................... \033[0m"
                echo
                echo -e "\033[31mPlease check whether the moduler extractor/batch_loader stopped first... \033[0m"
                echo
                echo -e "\033[33m.......................................................................... \033[0m"
                read -ep "$prompt" modify_tag
                if [ $modify_tag == "y" ] || [ $modify_tag == "yes" ]
                then
                        for i in {0..9};do
                                modify_offset $i
                        done
                fi
            fi
        fi
}
function usage() {
        echo -e "usage: $0 [OPTIONS]"
        echo -e " -h --help  display help info ."
        echo -e " -c --check check these offset OK"
        echo -e " -m --modify execute modify operate"
        exit 1
}
function main() {
        user=`whoami`
        if [ $user != "sa_cluster" ]
        then
                echo -e "\033[31mplease execute su - sa_cluster!\033[0m"
                exit 1
        fi
        if [ $PARA_NUM -eq 0 ]; then
                usage
        fi

        while [ $PARA_NUM -gt 0 ]; do
        case $chose in
        -h|--help)
                usage
                break
                ;;
        -c|--check)
                f_tag="check"
                magic_execute $f_tag
                break
                ;;
        -m|--modify)
                f_tag="modify"
                magic_execute $f_tag
                break
                ;;
        *)
                usage
                break
                ;;
        esac
        done
}

let PARA_NUM=$(echo $#)
chose=$1
prompt=$(tput setaf 6; echo -n 'Are you sure to modify the offset -->>>(y/yes)'; tput sgr0; echo -n ':')  #read add color
main
</pre>

### 5. CDH kafka三副本，有台节点挂了需要更换节点
将kafka新节点加上
将kafka旧节点kafka停止，并删除角色
kafka的broker_id改成要删除节点的kafka broker_id

启动kafka，由于是三副本配置，新加的kafka节点会从其他两台节点同步数据. 

### 6. cdh 转发配置
```

upstream sensors_data_cdh {
  server 172.30.200.201:7180;
  ip_hash;
}



server {
  listen 80;
  server_name  xxxxx.cn;
  access_log off;


  location /  {
    proxy_set_header HOST $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_pass http://sensors_data_cdh;
  }
}
```

