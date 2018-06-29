## 1. 大容量磁盘分区工具parted
parted -m -s /dev/vdc mklabel gpt mkpart primary 0% 100%
