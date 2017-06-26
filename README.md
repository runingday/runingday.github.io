## Mysql 笔记
### 创建表
1. 
CREATE TABLE `email_report` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(255) COLLATE utf8mb4_bin DEFAULT NULL,
  `project_id` int(11) DEFAULT NULL,
  `user_id` int(11) DEFAULT NULL,
  `dashboard_id` int(11) DEFAULT NULL,
  `smtp_config` text COLLATE utf8mb4_bin,
  `subject` varchar(255) COLLATE utf8mb4_bin DEFAULT NULL,
  `description` text COLLATE utf8mb4_bin,
  `to_list` text COLLATE utf8mb4_bin,
  `cc_list` text COLLATE utf8mb4_bin,
  `bcc_list` text COLLATE utf8mb4_bin,
  `alarm_to_list` text COLLATE utf8mb4_bin,
  `with_pdf` int(11) DEFAULT NULL,
  `with_image` int(11) DEFAULT NULL,
  `dashboard_url` varchar(255) COLLATE utf8mb4_bin DEFAULT NULL,
  `last_success_time` datetime DEFAULT NULL,
  `scheduler_cron` varchar(255) COLLATE utf8mb4_bin DEFAULT NULL,
  `in_use` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `project_index` (`project_id`,`dashboard_id`)
) ENGINE=InnoDB AUTO_INCREMENT=30 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
### 修改字段的值
修改同一个表中某个字段的值（关联到同一个表中的其中一个字段及字段值+字符串）
update email_report set email_report.subject=concat(email_report.name, "${data_date}");

### AND OR 优先级问题
mysql> select vend_id, prod_name, prod_price from products where vend_id='DLL01' OR vend_id='BRS01';

| vend_id | prod_name           | prod_price |
| ------- | ------------------  | ---------- |
| DLL01   | Fish bean bag toy   |       3.49 |
| DLL01   | Bird bean bag toy   |       3.49 |
| DLL01   | Rabbit bean bag toy |       3.49 |
| BRS01   | 8 inch teddy bear   |       5.99 |
| BRS01   | 12 inch teddy bear  |       8.99 |
| BRS01   | 18 inch teddy bear  |      11.99 |
| DLL01   | Raggedy Ann         |       4.99 |

7 rows in set (0.00 sec)

mysql> select vend_id, prod_name, prod_price from products where vend_id='DLL01' OR vend_id='BRS01' and prod_price >= 10;

| vend_id | prod_name           | prod_price |
|---------|---------------------|------------|
| DLL01   | Fish bean bag toy   |       3.49 |
| DLL01   | Bird bean bag toy   |       3.49 |
| DLL01   | Rabbit bean bag toy |       3.49 |
| BRS01   | 18 inch teddy bear  |      11.99 |
| DLL01   | Raggedy Ann         |       4.99 |
5 rows in set (0.00 sec)

mysql> select vend_id, prod_name, prod_price from products where (vend_id='DLL01' OR vend_id='BRS01') and prod_price >= 10;

| vend_id | prod_name          | prod_price |
|---------|---------------------|----------|
| BRS01   | 18 inch teddy bear |      11.99 |

1 row in set (0.00 sec)

mysql> select vend_id, prod_name, prod_price from products where (vend_id='DLL01' OR vend_id='BRS01') and prod_price >= 10;

### SQL中使用正则匹配

mysql> mysql> select cust_contact from customers where cust_contact REGEXP '^[JM].*$';

| cust_contact   |
| -------------  |
| John Smith     |
| Michelle Green |
| Jim Jones      |

3 rows in set (0.00 sec)

mysql> select cust_contact from customers where cust_contact REGEXP '^[^JM].*$';

| cust_contact       |
|--------------------|
| Denise L. Stephens |
| Kim Howard         |

2 rows in set (0.00 sec)

### 字符串拼接
mysql> select * from vendors;

| vend_id | vend_name       | vend_address    | vend_city  | vend_state | vend_zip | vend_country |
|--------- | ----------------| ---------------| ------------| ------------| ------------| ---------|
| BRE02   | Bear Emporium   | 500 Park Street | Anytown    | OH         | 44333    | USA          |
| BRS01   | Bears R Us      | 123 Main Street | Bear Town  | MI         | 44444    | USA          |
| DLL01   | Doll House Inc. | 555 High Street | Dollsville | CA         | 99999    | USA          |
| FNG01   | Fun and Games   | 42 Galaxy Road  | London     | NULL       | N16 6PS  | England      |
| FRB01   | Furball Inc.    | 1000 5th Avenue | New York   | NY         | 11111    | USA          |
| JTS01   | Jouets et ours  | 1 Rue Amusement | Paris      | NULL       | 45678    | France       |

6 rows in set (0.00 sec)

mysql> select concat (vend_name, ' (', vend_country, ')' ) from vendors;

| concat (vend_name, ' (', vend_country, ')' ) |
|----------------------------------------------|
| Bear Emporium (USA)                          |
| Bears R Us (USA)                             |
| Doll House Inc. (USA)                        |
| Fun and Games (England)                      |
| Furball Inc. (USA)                           |
| Jouets et ours (France)                      |

6 rows in set (0.00 sec)
