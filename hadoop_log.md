查看yarn 任务是否有读数据
```

hadoop job -status job_1527420382962_87145 （application_1527420382962_87145）


curl http://xxx.xxx.xx.xxx:8088/proxy/application_1527420382962_87145/


<span title="job_1527420382962_87145"></span><a href="/proxy/application_1527420382962_87145/mapreduce/job/job_1527420382962_87145">job_1527420382962_87145</a>


 curl http://xxx.xxx.xx.xxx:8088/proxy/application_1527420382962_87145/mapreduce/attempts/job_1527420382962_87145/m/RUNNING

<script type="text/javascript">
              var attemptsTableData=[
["attempt_1527420382962_87145_m_000001_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068037-resouce.bigdata.prod.bj1:8042'>ll-016068037-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068037-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000003/sa_cluster'>logs</a>","1539320960507","0","6861656",""],
["attempt_1527420382962_87145_m_000002_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016070058-sensordata.prod.bj1:8042'>ll-016070058-sensordata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016070058-sensordata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000004/sa_cluster'>logs</a>","1539320960507","0","6861656",""],
["attempt_1527420382962_87145_m_000003_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068035-resouce.bigdata.prod.bj1:8042'>ll-016068035-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068035-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000005/sa_cluster'>logs</a>","1539320960506","0","6861658",""],
["attempt_1527420382962_87145_m_000004_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068049-resouce.bigdata.prod.bj1:8042'>ll-016068049-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068049-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000006/sa_cluster'>logs</a>","1539320960508","0","6861656",""],
["attempt_1527420382962_87145_m_000000_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016070057-resouce.bigdata.prod.bj1:8042'>ll-016070057-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016070057-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000002/sa_cluster'>logs</a>","1539320960501","0","6861663",""],
["attempt_1527420382962_87145_m_000009_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068048-resouce.bigdata.prod.bj1:8042'>ll-016068048-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068048-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000011/sa_cluster'>logs</a>","1539320960507","0","6861657",""],
["attempt_1527420382962_87145_m_000005_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068050-resouce.bigdata.prod.bj1:8042'>ll-016068050-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068050-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000007/sa_cluster'>logs</a>","1539320960506","0","6861658",""],
["attempt_1527420382962_87145_m_000006_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068112-sensordata.prod.bj1:8042'>ll-016068112-sensordata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068112-sensordata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000008/sa_cluster'>logs</a>","1539320960507","0","6861657",""],
["attempt_1527420382962_87145_m_000007_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068036-resouce.bigdata.prod.bj1:8042'>ll-016068036-resouce.bigdata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068036-resouce.bigdata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000009/sa_cluster'>logs</a>","1539320960506","0","6861658",""],
["attempt_1527420382962_87145_m_000008_0","0.00","RUNNING","map &gt; map","<a class='nodelink' href='http://ll-016068111-sensordata.prod.bj1:8042'>ll-016068111-sensordata.prod.bj1:8042</a>","<a class='logslink' href='http://ll-016068111-sensordata.prod.bj1:8042/node/containerlogs/container_1527420382962_87145_01_000010/sa_cluster'>logs</a>","1539320960506","0","6861658",""]
]

curl http://xxx.xxx.xx.xxx:8042/node/containerlogs/container_1527420382962_87145_01_000006/sa_cluster/syslog/?start=0 |grep " Load data speed"
```
