##查看hadoop数据容量占用情况，并排序显示
<code>

hadoop fs -du -s '/sa/data/4/event/*' | sort -r -k 1 -g | awk '{suffix="KMGT"; for(i=0;$1>1024&&i<length(suffix);i++) $1/=1024; print int($1) substr(suffix,i,1),$3;}'

</code>
hdfs 获取配置命令
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
