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
