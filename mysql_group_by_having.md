## mysql group by 使用
mysql> select * from products;

| prod_id | vend_id | prod_name           | prod_price | prod_desc |                                              
|---------|---------|---------------------|------------|-----------|
| BNBG01  | DLL01   | Fish bean bag toy   |       3.49 | Fish bean bag toy, complete with bean bag worms with |
| BNBG02  | DLL01   | Bird bean bag toy   |       3.49 | Bird bean bag toy, eggs are not included    |             
| BNBG03  | DLL01   | Rabbit bean bag toy |       3.49 | Rabbit bean bag toy, comes with bean bag carrots   | 
| BR01    | BRS01   | 8 inch teddy bear   |       5.99 | 8 inch teddy bear, comes with cap and jacket     |     
| BR02    | BRS01   | 12 inch teddy bear  |       8.99 | 12 inch teddy bear, comes with cap and jacket     |       
| BR03    | BRS01   | 18 inch teddy bear  |      11.99 | 18 inch teddy bear, comes with cap and jacket      |   
| RGAN01  | DLL01   | Raggedy Ann         |       4.99 | 18 inch Raggedy Ann doll            |                
| RYL01   | FNG01   | King doll           |       9.49 | 12 inch king doll with royal garments and crown  | 
| RYL02   | FNG01   | Queen doll          |       9.49 | 12 inch queen doll with royal garments and crown  |

9 rows in set (0.00 sec)

mysql> select vend_id, count(*) as num_prods from products where prod_price >= 4 group by vend_id;

| vend_id | num_prods |
|---------|-----------|
| BRS01   |         3 |
| DLL01   |         1 |
| FNG01   |         2 |

3 rows in set (0.00 sec)

mysql> select vend_id, count(*) as num_prods from products where prod_price >= 4 group by vend_id having count(*) >= 2;

| vend_id | num_prods |
|---------|-----------|
| BRS01   |         3 |
| FNG01   |         2 |

2 rows in set (0.00 sec)

mysql> select vend_id, count(*) as num_prods from products group by vend_id having count(*) >= 2;

| vend_id | num_prods |
|---------|-----------|
| BRS01   |         3 |
| DLL01   |         4 |
| FNG01   |         2 |


3 rows in set (0.00 sec)

mysql> select * from orderitems;
| order_num | order_item | prod_id | quantity | item_price |
|-----------|------------|---------|----------|------------|
|     20005 |          1 | BR01    |      100 |       5.49 |
|     20005 |          2 | BR03    |      100 |      10.99 |
|     20006 |          1 | BR01    |       20 |       5.99 |
|     20006 |          2 | BR02    |       10 |       8.99 |
|     20006 |          3 | BR03    |       10 |      11.99 |
|     20007 |          1 | BR03    |       50 |      11.49 |
|     20007 |          2 | BNBG01  |      100 |       2.99 |
|     20007 |          3 | BNBG02  |      100 |       2.99 |
|     20007 |          4 | BNBG03  |      100 |       2.99 |
|     20007 |          5 | RGAN01  |       50 |       4.49 |
|     20008 |          1 | RGAN01  |        5 |       4.99 |
|     20008 |          2 | BR03    |        5 |      11.99 |
|     20008 |          3 | BNBG01  |       10 |       3.49 |
|     20008 |          4 | BNBG02  |       10 |       3.49 |
|     20008 |          5 | BNBG03  |       10 |       3.49 |
|     20009 |          1 | BNBG01  |      250 |       2.49 |
|     20009 |          2 | BNBG02  |      250 |       2.49 |
|     20009 |          3 | BNBG03  |      250 |       2.49 |

mysql> select order_num, count(*) as items from orderitems group by order_num;
| order_num | items |
|-----------|-------|
|     20005 |     2 |
|     20006 |     3 |
|     20007 |     5 |
|     20008 |     5 |
|     20009 |     3 |
5 rows in set (0.00 sec)

mysql> select order_num, count(*) as items from orderitems group by order_num having count(*) > 3;
| order_num | items |
|-----------|-------|
|     20007 |     5 |
|     20008 |     5 |
2 rows in set (0.00 sec)

mysql> select order_num, count(*) as items from orderitems group by order_num having count(*) >= 3 order by items, order_num;
| order_num | items |
|-----------|-------|
|     20006 |     3 |
|     20009 |     3 |
|     20007 |     5 |
|     20008 |     5 |
4 rows in set (0.00 sec)
