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

***注*** while存在一些陷阱，详情：https://www.cnblogs.com/f-ck-need-u/p/7431578.html 
