##查看hadoop数据容量占用情况，并排序显示
<code>

hadoop fs -du -s '/sa/data/4/event/*' | sort -r -k 1 -g | awk '{suffix="KMGT"; for(i=0;$1>1024&&i<length(suffix);i++) $1/=1024; print int($1) substr(suffix,i,1),$3;}'

</code>

* hdfs 获取配置命令

```
  hadoop.http.logs.enabled
  [hdfs@node1.all.sensors.dmp.com ~]$hdfs getconf -confKey dfs.datanode.data.dir
  file:///tmp/hadoop-hdfs/dfs/data
  [hdfs@node1.all.sensors.dmp.com ~]$hdfs getconf -confKey dfs.data.dir
  18/08/08 19:25:44 INFO Configuration.deprecation: dfs.data.dir is deprecated. Instead, use dfs.datanode.data.dir
  file:///tmp/hadoop-hdfs/dfs/data
  [hdfs@node1.all.sensors.dmp.com ~]$hdfs getconf -confKey hadoop.http.logs.enabled
  true
```
* clodera manager api v12获取hdfs存储的数据目录列表
```
[sa_cluster@sensors08-third ~]$  curl -u admin:密码 "http://ip_address:7180/api/v13/clusters/cluster/services/hdfs/roles/hdfs-dn-1/config/"
  {
    "items" : [ {
      "name" : "datanode_log_dir",
      "value" : "/data/sa_cluster/cloudera/logs/hadoop-hdfs"
    }, {
      "name" : "dfs_data_dir_list",
      "value" : "/data1/sa_cluster/dfs/dn,/data2/sa_cluster/dfs/dn,/data3/sa_cluster/dfs/dn,/data4/sa_cluster/dfs/dn"
    }, {
      "name" : "dfs_datanode_data_dir_perm",
      "value" : "755"
    }, {
      "name" : "dfs_datanode_du_reserved",
      "value" : "787570543820"
    }, {
      "name" : "dfs_datanode_failed_volumes_tolerated",
      "value" : "0"
    }, {
      "name" : "dfs_datanode_max_locked_memory",
      "value" : "2113929216"
    }, {
      "name" : "max_log_size",
      "value" : "10"
    } ]
  }
```
