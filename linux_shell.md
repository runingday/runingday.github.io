# 使用awk来统计数据的差异
echo $(($(bash get_kafka_offsets_standalone.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')-$(bash get_vertica_offsets.sh | awk 'BEGIN {FS=","} {sum+=$2} END {print sum}')))
