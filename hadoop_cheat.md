## namenode format is danger command 
Hadoop namenode -format

    Hadoop namenode directory contains the fsimage and edit files which holds the basic information's about hadoop file system such as where is data available, which user created files like that

    If you format the namenode then the above information's are deleted from namenode directory which is specified in the hdfs-site.xml as dfs.namenode.name.dir

    But you still have the datas on the hadoop but not namenode meta data

## Hadoop recovery namenode from secondarynamenode
 

[root@dn1 ~]# scp /dfs/snn/current/* nn2:/dfs/nn/current
root@nn2's password:
edits_0000000000000000001-0000000000000000004 100% 104 0.1KB/s 00:00
edits_0000000000000000005-0000000000000000078 100% 1024KB 1.0MB/s 00:00
edits_0000000000000000079-0000000000000000080 100% 30 0.0KB/s 00:00
edits_0000000000000000081-0000000000000000093 100% 1024KB 1.0MB/s 00:00
edits_0000000000000000094-0000000000000000095 100% 30 0.0KB/s 00:00
fsimage_0000000000000000080 100% 931 0.9KB/s 00:00
fsimage_0000000000000000080.md5 100% 62 0.1KB/s 00:00
fsimage_0000000000000000095 100% 931 0.9KB/s 00:00
fsimage_0000000000000000095.md5 100% 62 0.1KB/s 00:00
VERSION 100% 176 0.2KB/s 00:00

Start the new HDFS NN

Start the Data Nodes
Redeploy client config (if using CM)
You should be good to go!
Recovery:
1. Create an empty directory specified in the dfs.namenode.checkpoint.dir configuration variable.
a.mkdir -p /data/secondary_nn/dfs/namesecondary
b.chown hdfs:hadoop /data/secondary_nn/dfs/namesecondary
2. Scp fsimage and edit logs from secondary namenode to namenode's dfs.namenode.checkpoint.dir.
a.[root@hdw3 namesecondary]# pwd
b./data/secondary_nn/dfs/namesecondary
c.[root@hdw3 namesecondary]# scp -r current hdm:/data/secondary_nn/dfs/namesecondary/
3. Change owner and group on namenode
a.chown -R hdfs:hadoop /data/secondary_nn/dfs/namesecondary/*
4. Namenode import checkpint
a) hdfs namenode -importCheckpoint
5. Restart HDFS cluster
