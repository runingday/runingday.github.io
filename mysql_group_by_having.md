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
