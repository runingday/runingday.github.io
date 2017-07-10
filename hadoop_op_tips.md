##查看hadoop数据容量占用情况，并排序显示
<code>

hadoop fs -du -s '/sa/data/4/event/*' | sort -r -k 1 -g | awk '{suffix="KMGT"; for(i=0;$1>1024&&i<length(suffix);i++) $1/=1024; print int($1) substr(suffix,i,1);$3;}'

</code>
