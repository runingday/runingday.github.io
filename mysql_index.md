### 1. mysql 创建及删除索引
sensor_sku表
```
MySQL [orders]> desc sensor_sku;
+---------------+--------------+------+-----+-------------------+-----------------------------+
| Field         | Type         | Null | Key | Default           | Extra                       |
+---------------+--------------+------+-----+-------------------+-----------------------------+
| sku_id        | varchar(50)  | NO   | PRI | NULL              |                             |
| ext_sku_name  | varchar(200) | YES  |     | NULL              |                             |
| ext_sku_tag   | varchar(200) | YES  |     | NULL              |                             |
| ext_sku_price | decimal(8,2) | YES  |     | NULL              |                             |
| create_time   | datetime     | YES  |     | CURRENT_TIMESTAMP |                             |
| update_time   | datetime     | YES  |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
| product_id    | varchar(50)  | YES  |     | NULL              |                             |
| sku_code      | varchar(50)  | NO   | MUL | NULL              |                             |
+---------------+--------------+------+-----+-------------------+-----------------------------+
8 rows in set (0.01 sec)
```
sensor_order_sku_detai
```

MySQL [orders]> desc sensor_order_sku_detail;
+-----------------------+--------------+------+-----+-------------------+-----------------------------+
| Field                 | Type         | Null | Key | Default           | Extra                       |
+-----------------------+--------------+------+-----+-------------------+-----------------------------+
| id                    | bigint(20)   | NO   | PRI | NULL              | auto_increment              |
| order_id              | varchar(50)  | YES  | MUL | NULL              |                             |
| sku_id                | varchar(50)  | YES  | MUL | NULL              |                             |
| ext_sku_count         | int(11)      | YES  |     | NULL              |                             |
| ext_user_id           | varchar(50)  | YES  |     | NULL              |                             |
| ext_sku_unit_price    | decimal(8,2) | YES  |     | NULL              |                             |
| ext_sku_total_price   | decimal(8,2) | YES  |     | NULL              |                             |
| ext_sku_discount_unit | decimal(8,2) | YES  |     | NULL              |                             |
| ext_sku_discount_sum  | decimal(8,2) | YES  |     | NULL              |                             |
| create_time           | datetime     | YES  |     | CURRENT_TIMESTAMP |                             |
| update_time           | datetime     | YES  |     | CURRENT_TIMESTAMP | on update CURRENT_TIMESTAMP |
| sku_code              | varchar(200) | YES  | MUL | NULL              |                             |
+-----------------------+--------------+------+-----+-------------------+-----------------------------+
12 rows in set (0.00 sec)

```
创建及删除索引
```
create index index_id on sensor_sku(sku_id);
create index index_id on sensor_order_sku_detail(sku_id);
drop index index_id on sensor_order_sku_detail;
drop index index_id on sensor_sku;
create index index_id on sensor_sku(sku_id,sku_code);
create index index_id on sensor_order_sku_detail(sku_id, sku_code);
create index index_id on sensor_sku(sku_id);
create index index_id_2 on sensor_sku(sku_code);
create index index_id_3 on sensor_order_sku_detail(order_id);
create index index_id_3 on sensor_order(order_id);
```
查看一张表上有哪些索引
```
MySQL [orders]> show index from sensor_order_sku_detail;
+-------------------------+------------+------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table                   | Non_unique | Key_name   | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------------------------+------------+------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| sensor_order_sku_detail |          0 | PRIMARY    |            1 | id          | A         |      301150 |     NULL | NULL   |      | BTREE      |         |               |
| sensor_order_sku_detail |          1 | index_id   |            1 | sku_id      | A         |        5220 |     NULL | NULL   | YES  | BTREE      |         |               |
| sensor_order_sku_detail |          1 | index_id_2 |            1 | sku_code    | A         |        6665 |     NULL | NULL   | YES  | BTREE      |         |               |
| sensor_order_sku_detail |          1 | index_id_3 |            1 | order_id    | A         |      154320 |     NULL | NULL   | YES  | BTREE      |         |               |
+-------------------------+------------+------------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
4 rows in set (0.00 sec)
```
