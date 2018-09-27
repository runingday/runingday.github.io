## 1. 使用awk来统计数据的差异
echo $(($(bash get_kafka_offsets_standalone.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')-$(bash get_vertica_offsets.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')))

## 2. 使用awk来统计网络连接状态数
ss -tanp | awk 'BEGIN {FS=" "} {sum[$1]++} END {for (key in sum) print key, sum[key]}'
## 3. 实时监控日志状态、并做一些操作
1. shell 脚本实现
<pre>
#!/bin/bash
file="/sa_standalone/logs/stream_loader/stream_loader.log"
#tail -f      等同于--follow=descriptor，根据文件描述符进行追踪，当文件改名或被删除，追踪停止
#tail -F     等同于--follow=name  --retry，根据文件名进行追踪，并保持重试，即该文件被删除或改名后，如果再次创建相同的文件名，会继续追踪
#tailf        等同于tail -f -n 10（貌似tail -f或-F默认也是打印最后10行，然后追踪文件），与tail -f不同的是，如果文件不增长，它不会去访问磁盘文件，所以tailf特别适合那些便携机上跟踪日志文件，因为它减少了磁盘访问，可以省电
tail -Fn0 $file | while read -r line; do
        echo "$line" | grep "Ros Too Many ERROR" >> /sa_standalone/runtime/sl.log 2>&1
        if [ $? = 0 ]
        then
                echo "Error discovery" >> /sa_standalone/runtime/sl.log 2>&1
                echo "select make_ahm_now()" | sa_vsql >> /sa_standalone/runtime/vert.log 2>&1
                echo "select do_tm_task('mergeout', 'event_p1')" | sa_vsql >> /sa_standalone/runtime/vert.log 2>&1
                echo "select purge()" | sa_vsql >> /sa_standalone/runtime/vert.log 2>&1
        fi
        echo "$line" | grep "totalTps" >> /sa_standalone/runtime/sl.log 2>&1
        if [ $? = 0 ]
        then
                echo "all right" >> /sa_standalone/runtime/sl.log 2>&1
        fi
done
</pre>
2. 通过一句话脚本执行
<pre>
tail -fn0 logfile | awk '/pattern/ { print | "command" }'
</pre>

3. 正确匹配ip地址
<pre>
[sa_cluster@data01 nginx]$ echo '12.11.1.111' | egrep -o -w "([1-9][0-9]{0,2}[\.]){3}[1-9][0-9]{0,2}" --color
12.11.1.111
</pre>
4. awk 使用split 
<pre>
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[1]}'
I
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[2]}'
have
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[3]}'
an
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array," ")} END {print array[4]}'
apple
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array,"")} END {print array[3]}'
h
[sa_cluster@sensors08-third ~]$ echo "I have an apple" | awk '{split($0,array,"")} END {print array[5]}'
</pre>

5. awk 使用dict赋值
<pre>
sudo du -sh --exclude='/proc/*' /* | awk -F ' ' '/G/{my_dict[$2] = $1} END {for (key in my_dict) {print key ":" my_dict[key]}}'
</pre>
6. Use awk to split line into array and use that array's values in calling shell
```
        Joe:Johnson:25
        Sue:Miller:27

        cat "${smryfile}" | while IFS=: read first last varx
        do
            echo "first='$first' last='$last' varx='$varx'"
            # do something
        done

        while IFS=: read first last varx
        do
            echo "first='$first' last='$last' varx='$varx'"
            # do something
        done <"${smryfile}"

```
6. Bash Space-Separated (e.g., --option argument) (without getopt[s])
Usage   ./myscript.sh -e conf -s /etc -l /usr/lib /etc/hosts 
```
#!/bin/bash

POSITIONAL=()
while [[ $# -gt 0 ]]
do
key="$1"

case $key in
    -e|--extension)
    EXTENSION="$2"
    shift # past argument
    shift # past value
    ;;
    -s|--searchpath)
    SEARCHPATH="$2"
    shift # past argument
    shift # past value
    ;;
    -l|--lib)
    LIBPATH="$2"
    shift # past argument
    shift # past value
    ;;
    --default)
    DEFAULT=YES
    shift # past argument
    ;;
    *)    # unknown option
    POSITIONAL+=("$1") # save it in an array for later
    shift # past argument
    ;;
esac
done
set -- "${POSITIONAL[@]}" # restore positional parameters

echo FILE EXTENSION  = "${EXTENSION}"
echo SEARCH PATH     = "${SEARCHPATH}"
echo LIBRARY PATH    = "${LIBPATH}"
echo DEFAULT         = "${DEFAULT}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
if [[ -n $1 ]]; then
    echo "Last line of file specified as non-opt/last argument:"
    tail -1 "$1"
fi
```

7. Bash Equals-Separated (e.g., --option=argument) (without getopt[s])
Usage ./myscript.sh -e=conf -s=/etc -l=/usr/lib /etc/hosts
```
#!/bin/bash

for i in "$@"
do
case $i in
    -e=*|--extension=*)
    EXTENSION="${i#*=}"
    shift # past argument=value
    ;;
    -s=*|--searchpath=*)
    SEARCHPATH="${i#*=}"
    shift # past argument=value
    ;;
    -l=*|--lib=*)
    LIBPATH="${i#*=}"
    shift # past argument=value
    ;;
    --default)
    DEFAULT=YES
    shift # past argument with no value
    ;;
    *)
          # unknown option
    ;;
esac
done
echo "FILE EXTENSION  = ${EXTENSION}"
echo "SEARCH PATH     = ${SEARCHPATH}"
echo "LIBRARY PATH    = ${LIBPATH}"
echo "Number files in SEARCH PATH with EXTENSION:" $(ls -1 "${SEARCHPATH}"/*."${EXTENSION}" | wc -l)
if [[ -n $1 ]]; then
    echo "Last line of file specified as non-opt/last argument:"
    tail -1 $1
fi
```
8. To better understand ${i#*=} search for "Substring Removal" in this guide. It is functionally equivalent to `sed 's/[^=]*=//' <<< "$i"` which calls a needless subprocess or `echo "$i" | sed 's/[^=]*=//'` which calls two needless subprocesses.

Using getopt[s]
from: http://mywiki.wooledge.org/BashFAQ/035#getopts

getopt(1) limitations (older, relatively-recent getopt versions):

can't handle arguments that are empty strings
can't handle arguments with embedded whitespace
More recent getopt versions don't have these limitations.

Additionally, the POSIX shell (and others) offer getopts which doesn't have these limitations. Here is a simplistic getopts example:
```#!/bin/sh

# A POSIX variable
OPTIND=1         # Reset in case getopts has been used previously in the shell.

# Initialize our own variables:
output_file=""
verbose=0

while getopts "h?vf:" opt; do
    case "$opt" in
    h|\?)
        show_help
        exit 0
        ;;
    v)  verbose=1
        ;;
    f)  output_file=$OPTARG
        ;;
    esac
done

shift $((OPTIND-1))

[ "${1:-}" = "--" ] && shift

echo "verbose=$verbose, output_file='$output_file', Leftovers: $@"

# End of file
```
The advantages of getopts are:

It's more portable, and will work in other shells like dash.
It can handle multiple single options like -vf filename in the typical Unix way, automatically.
The disadvantage of getopts is that it can only handle short options (-h, not --help) without additional code.

There is a getopts tutorial which explains what all of the syntax and variables mean. In bash, there is also help getopts, which might be informative.

9. awk 赋值加判断
```
[sa_cluster@cloud ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/vda1        20G  7.7G   13G  39% /
devtmpfs         16G     0   16G   0% /dev
tmpfs            16G     0   16G   0% /dev/shm
tmpfs            16G   89M   16G   1% /run
tmpfs            16G     0   16G   0% /sys/fs/cgroup
/dev/vdb        200G   16G  185G   8% /data
tmpfs           3.2G     0  3.2G   0% /run/user/1000
tmpfs           3.2G     0  3.2G   0% /run/user/0
cm_processes     16G  2.8M   16G   1% /run/cloudera-scm-agent/process
[sa_cluster@cloud ~]$ df -h | tail -n +2 | awk '{sub('/\%/',"",$5);var=$5}{if((var-8)>=0) print var}'
39
8
```

