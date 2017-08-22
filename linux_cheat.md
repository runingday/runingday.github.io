## 1. 下载工具
### 多线程下载工具：
axel -n 10 'http://update.sensorsdata.cn/release/update/centos7/sensors-analytics-1.7.2816-centos7/sensors-analytics.tgz' -v -o sensors-analytics.tgz
### wget 断点续传
wget -c --tries=1000 可以不断尝试多少次
