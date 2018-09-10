```

#!/bin/bash

### permission problem return -1000
PERMISSION_CODE=1000
USAGE_CODE=1001
INPUT_CODE=1002
VALUE_POSITIONAL=()
KEY_POSITIONAL=()
### prompt ####################
function usage() {
cat << EOF
usage: sa_customer.py [-h] [-r | -a ASSIGNED_PORT] [--mapping MAPPING]
              [--input | --modify | --delete | --search | --list | --info | --pull PULL_FILE | --push PUSH_FILE]
              [--cloud CLOUD]
              customer

positional arguments:
  customer           Customer name, e.g. shence

optional arguments:
  -h, --help         show this help message and exit
  -r                 port forward(local:random, remote:7180)
  -a ASSIGNED_PORT   port forward(local:assign[15000-16999], remote:7180)
  --mapping MAPPING  assign remote host:port !!!should use with -r!!!
                     Example:-r --mapping 127.0.0.1:8050
  --input            input customer
  --modify           modify customer
  --delete           delete customer
  --search           search customer
  --list             --list all
  --info             --info customer_name
  --pull PULL_FILE   --pull /home/sa_cluster/sa_getlag.sh
  --push PUSH_FILE   --push ./sa_getlag.sh
  --cloud CLOUD      cloud ip address
EOF
exit ${USAGE_CODE}
}
function input_info() {
	while [[ $# -gt 0 ]]
	do
	key="$1"
	case $key in 
		--input)
		INPUT_TAG="$1"
		INPUT_CUSTOMER="$2"
		shift
		shift
		;;
		--modify)
		MODIFY_TAG="$1"
		MODIFY_CUSTOMER="$2"
		shift
		shift
		;;
		--delete)
		DELETE_TAG="$1"
		DELETE_CUSTOMER="$2"
		shift
		shift
		;;
		--search)
		SEARCH_TAG="$1"
		SEARCH_CUSTOMER="$2"
		shift
		shift
		;;
	        --list)
		LIST_TAG="$1"
		LIST_CUSTOMER="$2"
		shift
		shift
		list_user
		if [ ! $LIST_CUSTOMER ]
		then
			echo "当前可直连的客户列表:"
			echo -e "\033[32m${user_list[@]:1}\033[0m"
		else
			echo "当前匹配到的客户列表:"
			
			for customer in ${user_list[*]}
			do
				echo -e "\033[32mlto $customer\033[0m" | grep "${LIST_CUSTOMER}"
			done
				
		fi
		;;
		--info)
		INFO_TAG="$1"
		INFO_CUSTOMER="$2"
		shift
		shift
		if [ ! $INFO_CUSTOMER ]
		then
			show_screen "Please input the customer_name" "err"
			show_screen "$(basename $0) --info [customer_name]" "info"
			exit "$INPUT_CODE"
		else
			get_user_info "$INFO_CUSTOMER"
			exit 0
		fi
		;;
		-r)
		R_TAG=$1	
		RAND_CUSTOMER=$2
		shift
		shift
		;;
		--mapping)
		MAPPING_TAG=$1
		MAPPING=$2
		shift
		shift
		;;
		-a) 
		A_TAG=$1
		ASSIGN_PORT=$2
		shift
		shift
		;;	
		--pull)
		PULL_TAG="$1"
		PULL_FILE="$2"
		shift
		shift
		;;
		--push)
		PUSH_TAG="$1"
		PUSH_FILE="$2"
		shift
		shift
		;;
		--cloud)
		CLOUD_TAG="$1"
		CLOUD_FILE="$2"
		shift
		shift
		;;
		--default)
	    	DEFAULT=YES
	    	POSITIONAL+=("$1") # save it in an array for later
	   	shift # past argument
	    	;;
	    	*)    # unknown option
		CUSTOMER_NAME="$1"
	    	POSITIONAL+=("$1") # save it in an array for later
		#IFSbak=$IFS
		#IFS=" "
	#	list_user 
	#	for ((i=0;i<${#user_list[@]};++i)); do
	#		if [ "$customer_name" == "${user_list[$i]}" ];then
	#			#show_screen "Found username: $customer_name" "info"
	#			#query="select customer_name, ssh_ip, ssh_port, ssh_user, comments, is_jumpserver, create_time, create_user, modify_time, last_modify_user, access_time, last_access_user from customers where customer_name='$customer_name'"
	#			#query="select customer_name,ssh_ip, ssh_port, ssh_user, ssh_key, comments, is_jumpserver, access_time, last_access_user from customers where customer_name='$customer_name'"
        #			#while read -r customer_name ssh_ip ssh_port ssh_user ssh_key comments is_jumpserver access_time last_access_user
        #			#do
        #        		#	let tag++
	#			#	#if [ $tag -gt 0 ];then
	#			#	#	echo "ssh_key:$ssh_key"
	#			#	#fi
        #        		#	echo_function customer_name ${customer_name}
        #        		#	echo_function ssh_ip ${ssh_ip}
        #        		#	echo_function ssh_port ${ssh_port}
        #        		#	echo_function ssh_user ${ssh_user}
	#			#	echo_function ssh_key ${ssh_key}
        #        		#	echo_function comments ${comments}
        #        		#	echo_function is_jumpserver ${is_jumpserver}
        #        		#	echo_function access_time ${access_time}
        #        		#	echo_function last_access_user ${last_access_user}
        #			#done < <(mysql -u"$MYSQL_USER" -h"$MYSQL_HOST" -P"$MYSQL_PORT" -p"$MYSQL_PASSWORD" -D "$MYSQL_DB" -N -e "$query")
	#			#if [ $tag -eq "0" ];then
        #        		#	show_screen "can't find the customer, please check again!!!" "err"
        #        		#	exit $INPUT_CODE
        #			#fi
	#			#exit 0
	#			ssh_key=$(get_mysql_field "ssh_key" "$customer_name")
	#			is_jumpserver=$(get_mysql_field "is_jumpserver" "$customer_name")
	#			ssh_user=$(get_mysql_field "ssh_user" "$customer_name")
	#			ssh_port=$(get_mysql_field "ssh_port" "$customer_name")
	#			ssh_ip=$(get_mysql_field "ssh_ip" "$customer_name")
	#			if [ "$is_jumpserver" == "yes" ]
	#			then
	#				comments=$(get_mysql_field "comments" "$customer_name")
	#				echo "comments:$comments"
	#				show_screen "$comments" "info"
	#			fi
	#			if [ "$ssh_key" != "None" ]
	#			then
	#				salt=$(get_mysql_field "salt" "$customer_name")
	#				lto_key=`echo $salt:$ssh_key:$customer_name | decrypt`
	#				tmpfile=$(mktemp "/tmp/pem.XXXXXXXXXXXXXXXX") 
	#				echo "$lto_key" > "$tmpfile"
	#				ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}"
	#				rm -f "$tmpfile"
	#			else
	#				ssh_password=$(get_mysql_field "ssh_password" "$customer_name")
	#				echo "ssh_password:$ssh_password"
	#				salt=$(get_mysql_field "salt" "$customer_name")
	#				echo "salt:$salt"
	#				read lto_password <<< `echo $salt:$ssh_password:$customer_name | decrypt`	
	#				sshpass -p "${lto_password}" ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" "${ssh_user}@${ssh_ip}" && rm -f "${customer_name}".pem
	#				
	#				
	#			fi
	#			exit 0
	#			
	#		else
	#			continue
	#		fi
	#	done
	#	show_screen "Can't find customer $customer_name, please check again!!!" "err"
		shift
	    	;;
	esac
	done
}

function ssh_hosts_option() {
	echo "CUSTOMER_NAME:${CUSTOMER_NAME}"
	echo "R_TAG:${R_TAG}"
	echo "INPUT_TAG:${INPUT_TAG}"
	echo "INPUT_CUSTOMER:${INPUT_CUSTOMER}"
	echo "RAND_CUSTOMER:${RAND_CUSTOMER}"
	echo "INPUT_TAG:${INPUT_TAG}"
	if [ "${R_TAG}" ]&& [ "${RAND_CUSTOMER}" ]&&[ ! -n "${INPUT_TAG}" ]&&[ ! -n "${MODIFY_TAG}" ]&&[ ! -n "${DELETE_TAG}" ] && [ ! -n "${SEARCH_TAG}" ] && [ ! -n "${LIST_TAG}" ] && [ ! -n "${INFO_TAG}" ] && [ ! -n "${PULL_TAG}" ] && [ ! -n "${CLOUD_TAG}" ] && [ ! -n "${A_TAG}" ] && [ ! -n "${MAPPING_TAG}" ]
	then
		RAND_PORT=`shuf -i 15000-20000 -n 1`
		lineto "$RAND_CUSTOMER" "$RAND_PORT"
	elif [ "${R_TAG}" ]&&[ "${CUSTOMER_NAME}" ]&&[ ! -n "${INPUT_TAG}" ]&&[ ! -n "${MODIFY_TAG}" ] && [ ! -n "${DELETE_TAG}" ] && [ ! -n "${SEARCH_TAG}" ] && [ ! -n "${LIST_TAG}" ] && [ ! -n "${INFO_TAG}" ] && [ ! -n "${PULL_TAG}" ] && [ ! -n "${CLOUD_TAG}" ] && [ ! -n "${A_TAG}" ] && [ ! -n "${MAPPING_TAG}" ]
	then
		RAND_PORT=`shuf -i 15000-20000 -n 1`
		lineto "$CUSTOMER_NAME" "$RAND_PORT"
	elif [ ! -n "${R_TAG}" ] && [ ! -n "${INPUT_TAG}" ] && [ ! -n "${MODIFY_TAG}" ] && [ ! -n "${DELETE_TAG}" ] && [ ! -n "${SEARCH_TAG}" ] && [ ! -n "${LIST_TAG}" ] && [ ! -n "${INFO_TAG}" ] && [ ! -n "${PULL_TAG}" ] && [ ! -n "${CLOUD_TAG}" ] && [ ! -n "${A_TAG}" ] && [ ! -n "${MAPPING_TAG}" ]
	then
		lineto "$CUSTOMER_NAME"
	elif [ "${A_TAG}" ]&&[ "${ASSIGN_PORT}" ] && [ ! -n "${INPUT_TAG}" ] && [ ! -n "${MODIFY_TAG}" ] && [ ! -n "${DELETE_TAG}" ] && [ ! -n "${SEARCH_TAG}" ] && [ ! -n "${LIST_TAG}" ] && [ ! -n "${INFO_TAG}" ] && [ ! -n "${PULL_TAG}" ] && [ ! -n "${CLOUD_TAG}" ] && [ ! -n "${A_TAG}" ] && [ ! -n "${MAPPING_TAG}" ]
	then
		lineto "${CUSTOMER_NAME}" "${ASSIGN_PORT}"
	elif [ "${R_TAG}" ] && [ "${MAPPING_TAG}" ]&& [ "${MAPPING}" ]&& [ ! -n "$CUSTOMER_NAME" ]&&[ "${RAND_CUSTOMER}" ]&&[ ! -n "${INPUT_TAG}" ] && [ ! -n "${MODIFY_TAG}" ] && [ ! -n "${DELETE_TAG}" ] && [ ! -n "${SEARCH_TAG}" ] && [ ! -n "${LIST_TAG}" ] && [ ! -n "${INFO_TAG}" ] && [ ! -n "${PULL_TAG}" ] && [ ! -n "${CLOUD_TAG}" ] && [ ! -n "${A_TAG}" ] 
	then
		RAND_PORT=`shuf -i 15000-20000 -n 1`
		lineto "$RAND_CUSTOMER"  "$RAND_PORT"
	elif [ "${R_TAG}" ] && [ "${MAPPING_TAG}" ]&& [ "${MAPPING}" ] && [ "$CUSTOMER_NAME" ] && [ ! -n "${RAND_CUSTOMER}" ]&&[ ! -n "${INPUT_TAG}" ] && [ ! -n "${MODIFY_TAG}" ] && [ ! -n "${DELETE_TAG}" ] && [ ! -n "${SEARCH_TAG}" ] && [ ! -n "${LIST_TAG}" ] && [ ! -n "${INFO_TAG}" ] && [ ! -n "${PULL_TAG}" ] && [ ! -n "${CLOUD_TAG}" ] && [ ! -n "${A_TAG}" ] 
	then
		RAND_PORT=`shuf -i 15000-20000 -n 1`
		lineto "$CUSTOMER_NAME" "$RAND_PORT"
	else
		echo "parameter wrong"
		exit "${PERMISSION_CODE}"
	fi

}

function lineto() {
	list_user 
	customer_name=$1
	mapping_port=$2
	for ((i=0;i<${#user_list[@]};++i)); do
		if [ "$customer_name" == "${user_list[$i]}" ];then
			ssh_key=$(get_mysql_field "ssh_key" "$customer_name")
			is_jumpserver=$(get_mysql_field "is_jumpserver" "$customer_name")
			ssh_user=$(get_mysql_field "ssh_user" "$customer_name")
			ssh_port=$(get_mysql_field "ssh_port" "$customer_name")
			ssh_ip=$(get_mysql_field "ssh_ip" "$customer_name")
			cdh_ip=$(get_mysql_field "cdh_ip" "$customer_name")
			if [ "$is_jumpserver" == "yes" ]
			then
				comments=$(get_mysql_field "comments" "$customer_name")
				echo "comments:$comments"
				show_screen "$comments" "info"
			fi
			if [ "$ssh_key" != "None" ]
			then
				salt=$(get_mysql_field "salt" "$customer_name")
				lto_key=`echo $salt:$ssh_key:$customer_name | decrypt`
				tmpfile=$(mktemp "/tmp/pem.XXXXXXXXXXXXXXXX") 
				exec 3>$tmpfile
				echo "$lto_key" > "$tmpfile"
				if [ ! -n "${A_TAG}" ] && [ ! -n "${R_TAG}" ] && [ ! -n "${MAPPING_TAG}" ] 
				then
					echo "ssh -o StrictHostKeyChecking no -o ConnectTimeout=21 -o ServerAliveInterval=120 -p ${ssh_port} -i ${tmpfile} ${ssh_user}@${ssh_ip}"
					ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}"
					ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}"
				elif [ "${A_TAG}" ] || [ "${R_TAG}" ] && [ ! -n "{MAPPING_TAG}" ]
				then
					show_screen "cdh_url --->  http://relay.sensorsdata.cn:${mapping_port}" "info"
					ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}" -L :${mapping_port}:${cdh_ip}:7180  "monitor_tools get_config -m cloudera -t client" 
					echo ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}" -L :${mapping_port}:${cdh_ip}:7180
					ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}" -L :${mapping_port}:${cdh_ip}:7180
				elif [ "${MAPPING_TAG}" ] && [ ! -n "${A_TAG}" ] && [ "${R_TAG}" ]
				then
					show_screen "MAPPING_URL --->  http://relay.sensorsdata.cn:${mapping_port}" "info"
					ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}" -L :${mapping_port}:${MAPPING}  "monitor_tools get_config -m cloudera -t client"
					echo ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}" -L :${mapping_port}:${MAPPING}
					ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" -i "${tmpfile}" "${ssh_user}"@"${ssh_ip}" -L :${mapping_port}:${MAPPING}
				else
					echo "Please check your command"
					rm -f $tmpfile 2>/dev/null
					exit "${INPUT_CODE}"
				fi
				rm -f $tmpfile 2>/dev/null
			else
				ssh_password=$(get_mysql_field "ssh_password" "$customer_name")
				echo "ssh_password:$ssh_password"
				salt=$(get_mysql_field "salt" "$customer_name")
				echo "salt:$salt"
				read lto_password <<< `echo $salt:$ssh_password:$customer_name | decrypt`	
				if [ ! -n "${A_TAG}" ] && [ ! -n "${R_TAG}" ] && [ ! -n "${MAPPING_TAG}" ]
				then
					show_screen "sshpass -p '******' ssh -o StrictHostKeyChecking no -o ConnectTimeout=21 -o ServerAliveInterval=120 -p ${ssh_port} ${ssh_user}@${ssh_ip}"
					sshpass -p "${lto_password}" ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" "${ssh_user}@${ssh_ip}"
				elif [ "${A_TAG}" ] || [ "${R_TAG}" ] && [ ! -n "${MAPPING_TAG}" ]
				then	
					show_screen "cdh_url--->	http://relay.sensorsdata.cn:${mapping_port}" "info"
					sshpass -p "${lto_password}" ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" "${ssh_user}@${ssh_ip}" -L :${mapping_port}:${cdh_ip}:7180 "monitor_tools get_config -m cloudera -t client"
					show_screen "sshpass -p '******' ssh -o StrictHostKeyChecking no -o ConnectTimeout=21 -o ServerAliveInterval=120 -p ${ssh_port} ${ssh_user}@${ssh_ip} -L :${mapping_port}:${cdh_ip}:7180" 
					sshpass -p "${lto_password}" ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" "${ssh_user}@${ssh_ip}" -L :${mapping_port}:${cdh_ip}:7180
				elif [ ! -n "${A_TAG}" ] || [ "${R_TAG}" ] && [ "${MAPPING_TAG}" ]
				then	
					show_screen "MAPPING_URL --->	http://relay.sensorsdata.cn:${mapping_port}" "info"
					#sshpass -p "${lto_password}" ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" "${ssh_user}@${ssh_ip}" -L :${mapping_port}:${cdh_ip}:7180 "monitor_tools get_config -m cloudera -t client"
					show_screen "sshpass -p '******' ssh -o StrictHostKeyChecking no -o ConnectTimeout=21 -o ServerAliveInterval=120 -p ${ssh_port} ${ssh_user}@${ssh_ip} -L :${mapping_port}:${MAPPING}" 
					sshpass -p "${lto_password}" ssh -o "StrictHostKeyChecking no" -o ConnectTimeout=21 -o ServerAliveInterval=120 -p "${ssh_port}" "${ssh_user}@${ssh_ip}" -L :${mapping_port}:${MAPPING}
				else
					echo "Please check your command"
					exit "${INPUT_CODE}"
				fi
			fi
			exit 0
			
		else
			continue
		fi
	done
	show_screen "Can't find customer $customer_name, please check again!!!" "err"

}

function encrypt() {
python3 -c "$(cat <<EOPY
import sys
import string
import random
import base64
from crypt_demo import Crypt as crypt
def random_bytes(self, length=7):
	s=string.ascii_uppercase + string.digits + string.ascii_lowercase
	code=''.join(random.sample(s, length))
	return str(code)
salt=random_bytes(9)
en_salt = str(base64.b64encode(salt.encode('utf-8')), 'utf-8')
cry=crypt()
pass_txt=sys.stdin.readline().strip()
encrypt_txt=cry.encrypt(pass_txt, salt)
print('%s|%s' %(en_salt,encrypt_txt))
EOPY
)"
}

function decrypt() {
python3 -c "$(cat <<EOPY
import sys
import crypt_demo
import string
import random
import base64
import os
import tempfile
from crypt_demo import Crypt as crypt
def random_bytes(self, length=7):
	s=string.ascii_uppercase + string.digits + string.ascii_lowercase
	code=''.join(random.sample(s, length))
	return str(code)
de_salt, pass_txt, customer_name = sys.stdin.readline().strip().split(':')
salt = str(base64.b64decode(de_salt), 'utf-8')
cry=crypt()
decrypt_txt=cry.decrypt(pass_txt, salt)
#with open("%s.pem" %customer_name, "w") as fh:
#    fh.write(decrypt_txt)
#os.chmod("%s.pem" %customer_name, 0o700)
print("%s" %decrypt_txt)
EOPY
)"
}

#function lineto() {

#}

MYSQL_HOST="10.9.85.144"
MYSQL_PORT="3306"
MYSQL_USER="devops"
MYSQL_PASSWORD="rt4sniveeSgXV9UH0"
MYSQL_DB="DevOps"
WHITE="\033[0;37m"
D_GREEN="\033[0;36m"
PURPLE="\033[0;35m"
BLUE="\033[34m"
YELLOW="\033[0;33m"
GREEN="\033[0;32m"
RED="\033[0;31m"
C_END="\033[0m"


function echo_function() {
	title=$1
	content=$2
	
	echo -e "${title}:${YELLOW}${content}${C_END}"
}
function show_screen() {
	content=$1
	level=$2
	if [ $level=="err" ]
	then
		echo -e "${RED}${content}${C_END}"
	elif [ $level=="info" ]
	then
		echo -e "${GREEN}${content}${C_END}"
	else
		echo -e "${YELLOW}${conatent}${C_END}"
	fi
}

function get_mysql_field(){
	field=$1
	customer_name=$2
	query="set names utf8;select ${field} from customers where customer_name='${customer_name}'"
	field_value=$(mysql -u"$MYSQL_USER" -h"$MYSQL_HOST" -P"$MYSQL_PORT" -p"$MYSQL_PASSWORD" -D "$MYSQL_DB" -N -e "$query")
	echo "$field_value"
	

}

function get_user_info() {
	username=$1
	tag=0
	query="set names utf8;select customer_name, ssh_ip, ssh_port, ssh_user, comments, is_jumpserver, create_time, create_user, modify_time, last_modify_user, access_time, last_access_user from customers where customer_name='$username'"
	while read -r customer_name ssh_ip ssh_port ssh_user comments is_jumpserver create_time create_user modify_time last_modify_user access_time last_access_user
	do
		let tag++
		echo_function customer_name ${customer_name}
		echo_function ssh_ip ${ssh_ip}
		echo_function ssh_port ${ssh_port}
		echo_function ssh_user ${ssh_user}
		echo_function comments ${comments}
		echo_function is_jumpserver ${is_jumpserver}
		echo_function create_time ${create_time}
		echo_function create_user ${create_user}
		echo_function modify_time ${modify_time}
		echo_function last_modify_user ${last_modify_user}
		echo_function access_time ${access_time}
		echo_function last_access_user ${last_access_user}
	done < <(mysql -u"$MYSQL_USER" -h"$MYSQL_HOST" -P"$MYSQL_PORT" -p"$MYSQL_PASSWORD" -D "$MYSQL_DB" -N -e "$query")
	if [ $tag -eq "0" ];then
		show_screen "can't find the customer, please check again!!!" "err"
		exit $INPUT_CODE
	fi
}



user_list=()
function list_user() {
	query="select customer_name from customers"
	while read -r line
	do 
		#echo "line:$line"
    		user_list+=("$line")
	done < <(mysql -u"$MYSQL_USER" -h"$MYSQL_HOST" -P "$MYSQL_PORT" -p$MYSQL_PASSWORD -D "$MYSQL_DB" -e "$query")
	#done <<<`mysql -udevops -h10.9.85.144 -P 3306 -p -D DevOps -e "select customer_name from customers" -p"rt4sniveeSgXV9UH0"`
}

#function get_user_auth() {

#}


function main(){
	if [[ $# -eq 0 ]]
	then
		usage
	fi
	if [ $USER == "root" ];then
		echo "Cann't run as root"
		exit $PERMISSION_CODE
	fi
	input_info "$@"
	ssh_hosts_option
	

}
main "$@"
read salt password <<< `echo "hahaha" | encrypt | awk -F'|' '{print $1,$2}'`
#echo "salt:$salt"
#echo "password:$password"

en_salt="NU0zTFhIR1My"
pass_txt="PkRuJYIjgowYDm3q54j0CT+54bJcqa3oZ/tW8lnx6Xgt"
key_salt="R21JRjRvWHBN"
pass_key="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"

#read lto_key <<< `echo $key_salt:$pass_key:$customer_name | decrypt`
read lto_password <<< `echo $en_salt:$pass_txt:$customer_name | decrypt`
echo "lto_password:$lto_password"
#echo "lto_key:$lto_key"

```
