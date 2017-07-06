# 查询

mysql> select * from orders;

| order_num | order_date          | cust_id    |
|-----------|---------------------|------------|
|     20005 | 2012-05-01 00:00:00 | 1000000001 |
|     20006 | 2012-01-12 00:00:00 | 1000000003 |
|     20007 | 2012-01-30 00:00:00 | 1000000004 |
|     20008 | 2012-02-03 00:00:00 | 1000000005 |
|     20009 | 2012-02-08 00:00:00 | 1000000001 |

5 rows in set (0.01 sec)

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

18 rows in set (0.00 sec)

mysql> select * from customers;

| cust_id    | cust_name     | cust_address         | cust_city | cust_state | cust_zip | cust_country | cust_contact       | cust_email            |
|------------|---------------|----------------------|-----------|------------|----------|--------------|--------------------|-----------------------|
| 1000000001 | Village Toys  | 200 Maple Lane       | Detroit   | MI         | 44444    | USA          | John Smith         | sales@villagetoys.com |
| 1000000002 | Kids Place    | 333 South Lake Drive | Columbus  | OH         | 43333    | USA          | Michelle Green     | NULL                  |
| 1000000003 | Fun4All       | 1 Sunny Place        | Muncie    | IN         | 42222    | USA          | Jim Jones          | jjones@fun4all.com    |
| 1000000004 | Fun4All       | 829 Riverside Drive  | Phoenix   | AZ         | 88888    | USA          | Denise L. Stephens | dstephens@fun4all.com |
| 1000000005 | The Toy Store | 4545 53rd Street     | Chicago   | IL         | 54545    | USA          | Kim Howard         | NULL                  |

5 rows in set (0.00 sec)

<pre> 
Now suppose you wanted a list of all customers who ordered item RGAN01. What would you have to do to retrieve this information?
1. Retrieve the order numbers of all orders containing item RGAN01.
2. Retrieve the customer ID of all the customers who have orders listed in the order numbers returned in the previous step. 
3. Retrieve the customer information for all the customer IDs returned in the previous step. 
</pre>

## 1. Retrieve the order numbers of all orders containing item RGAN01.

mysql> select order_num from orderItems where prod_id='RGAN01';

| order_num |
|-----------|
|     20007 |
|     20008 |

2 rows in set (0.00 sec)

## 2. 
mysql> select cust_id from orders where order_num in (20007, 20008);

| cust_id    |
|------------|
| 1000000004 |
| 1000000005 |

2 rows in set (0.00 sec)

## 3. 
mysql> select cust_name, cust_contact from customers where cust_id in (1000000004,1000000005);

| cust_name     | cust_contact       |
|---------------|--------------------|
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |

2 rows in set (0.01 sec)
mysql> select cust_name, cust_contact from customers where cust_id in (select cust_id from orders where order_num in (select order_num from orderItems where prod_id='RGAN01'));

| cust_name     | cust_contact       |
|---------------|--------------------|
| Fun4All       | Denise L. Stephens |
| The Toy Store | Kim Howard         |

2 rows in set (0.00 sec)
