### 使用while 循环语句配合awk生成字典

```

[sa_cluster@sensors08-third ~]$ cat a.sh
#!/bin/bash
#`sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key" "my_dict[key]}}'` | while read key value
IFS=" "
declare -A myarr
myarr["0"]="33G"
myarr["1"]="50G"
myarr["2"]="55G"
data_dir_arr=()
while read data_dir
do
        data_dir_arr+=($data_dir)
done <<<`grep data_disk_paths .sa_config | awk -F: '{print $2}' | awk -F, '{for(i=1;i<=NF;i++){print $i}}' | sed 's/\(.*\)\/.*/\1/'`

function directory_digram()
{
    dir_path=$1
    echo "dir_path:$dir_path"
    if [ $dir_path == '/' ]
    then
        dir_path=root
    else
        dir_path=$1
    fi

    dir_path_dict=${dir_path}
    echo "dir_path_dict: $dir_path_dict"
    declare -A dir_path_dict
    #过滤出/目录下大于1G的目录
    while read path disk_space
    do
        dir_path_dict+=([$path]="$disk_space")

    #done <<<`sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key" "my_dict[key]}}'`
    done < <(echo `sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key" "my_dict[key]}}'`)


    #for path in $(echo ${!${dir_path}_dict[*]})
    for path in $(echo ${!dir_path_dict[*]})
    do
        echo "$path: ${dir_path_dict[$path]}"
    done
}

function data_directory_digram()
{
    declare -A data_dir

}

test()
{
    test_arr=myarr
    for key in $(echo ${!test_arr[*]})
    do
            echo "$key : ${test_arr[$key]}"
    done
}

get()
{
        echo -n "数据盘目录:"
        for ele in "${data_dir_arr[@]}"
        do
                echo -n "$ele "
        done
        echo

}
directory_digram "/"

#root_directory_digram

```

awk 加些类判断

```
[root@node1.all.sensors.dmp.com runtime]$du -sh * | awk '{print $1","$2}'| awk -F, '$2=="user.conf"{print $1};$2!="user.conf"{print $2}'
partitions_to_move.json
partitions_to_move.json.bak
partitions_to_move_kafka.json
rollback2.json
rollback.json
rollback_kafka.json
sa.tgz
sensors-analytics-update-1.6-1.7-406
sensors-analytics-update-1.7-1.8-548
topic.json
update
4.0K
[root@node1.all.sensors.dmp.com runtime]$du -sh *
4.0K    partitions_to_move.json
4.0K    partitions_to_move.json.bak
4.0K    partitions_to_move_kafka.json
4.0K    rollback2.json
4.0K    rollback.json
4.0K    rollback_kafka.json
1.2G    sa.tgz
64K     sensors-analytics-update-1.6-1.7-406
36K     sensors-analytics-update-1.7-1.8-548
4.0K    topic.json
529M    update
4.0K    user.conf
```

***注*** while存在一些陷阱，详情：https://www.cnblogs.com/f-ck-need-u/p/7431578.html 
