### 使用while 循环语句配合awk生成字典
```
[sa_cluster@cloud ~]$ cat a.sh
#!/bin/bash
#`sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key" "my_dict[key]}}'` | while read key value
IFS=" "
declare -A myarr
myarr["0"]="33G"
myarr["1"]="50G"
myarr["2"]="55G"

function root_directory_digram()
{
    declare -A root_dir
    #过滤出/目录下大于1G的目录
    while read path disk_space
    do
        root_dir+=([$path]="$disk_space")
    #done <<<`sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key" "my_dict[key]}}'`
    done < <(echo `sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key" "my_dict[key]}}'`)


    for path in $(echo ${!root_dir[*]})
    do
        echo "$path: ${root_dir[$path]}"
    done
}

function data_directory_digram()
{
    declare -A data_dir

}

echo "helloworld"
for path in $(echo ${!root_dir[*]})
do
    echo "abcd"
    echo "$path: ${root_dir[$path]}"
done
test()
{
    for key in $(echo ${!myarr[*]})
    do
            echo "$key : ${myarr[$key]}"
    done
}

root_directory_digram

```
