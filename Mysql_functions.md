# Mysql function 查看mysql自身支持的函数

##查看所有支持函数的操作 
<pre>
mysql> help  FUNCTIONS;
You asked for help about help category: "Functions"
For more information, type 'help <item>', where <item> is one of the following
categories:
   Bit Functions
   Comparison operators
   Control flow functions
   Date and Time Functions
   Encryption Functions
   Information Functions
   Logical operators
   Miscellaneous Functions
   Numeric Functions
   String Functions
 </pre>
 
##查看字符串函数类型  
<pre>
mysql> help  string functions;
You asked for help about help category: "String Functions"
For more information, type 'help <item>', where <item> is one of the following
topics:
   ASCII
   BIN
   BINARY OPERATOR
   BIT_LENGTH
   CAST
   CHAR FUNCTION
   CHARACTER_LENGTH
   CHAR_LENGTH
   CONCAT
   CONCAT_WS
   CONVERT
   ELT
   EXPORT_SET
   EXTRACTVALUE
   FIELD
   FIND_IN_SET
   FORMAT
   FROM_BASE64
   HEX
   INSERT FUNCTION
   INSTR
   LCASE
   LEFT
   LENGTH
   LIKE
   LOAD_FILE
   LOCATE
   LOWER
   LPAD
   LTRIM
   MAKE_SET
   MATCH AGAINST
   MID
   NOT LIKE
   NOT REGEXP
   OCT
   OCTET_LENGTH
   ORD
   POSITION
   QUOTE
   REGEXP
   REPEAT FUNCTION
   REPLACE FUNCTION
   REVERSE
   RIGHT
   RPAD
   RTRIM
   SOUNDEX
   SOUNDS LIKE
   SPACE
   STRCMP
   SUBSTR
   SUBSTRING
   SUBSTRING_INDEX
   TO_BASE64
   TRIM
   UCASE
   UNHEX
   UPDATEXML
   UPPER
   WEIGHT_STRING
</pre>

##查看某个类型有哪些函数

<pre>
mysql> help  Date and Time Functions  #help 指定要查的类型
You asked for help about help category: "Date and Time Functions"
For more information, type 'help <item>', where <item> is one of the following
topics:
   ADDDATE
   ADDTIME
   CONVERT_TZ
   CURDATE
   CURRENT_DATE
   CURRENT_TIME
   CURRENT_TIMESTAMP
   CURTIME
   DATE FUNCTION
   DATEDIFF
   DATE_ADD
   DATE_FORMAT
   DATE_SUB
   DAY
   DAYNAME
   DAYOFMONTH
   DAYOFWEEK
   DAYOFYEAR
   EXTRACT
   FROM_DAYS
   FROM_UNIXTIME
   GET_FORMAT
   HOUR
   LAST_DAY
   LOCALTIME
   LOCALTIMESTAMP
   MAKEDATE
   MAKETIME
   MICROSECOND
   MINUTE
   MONTH
   MONTHNAME
   NOW
   PERIOD_ADD
   PERIOD_DIFF
   QUARTER
   SECOND
   SEC_TO_TIME
   STR_TO_DATE
   SUBDATE
   SUBTIME
   SYSDATE
   TIME FUNCTION
   TIMEDIFF
   TIMESTAMP FUNCTION
   TIMESTAMPADD
   TIMESTAMPDIFF
   TIME_FORMAT
   TIME_TO_SEC
   TO_DAYS
   TO_SECONDS
   UNIX_TIMESTAMP
   UTC_DATE
   UTC_TIME
   UTC_TIMESTAMP
   WEEK
   WEEKDAY
   WEEKOFYEAR
   YEAR
   YEARWEEK
   NULLIF
</pre>

##查看函数的帮助

<pre>
mysql> help 'ADDDATE'  #help '函数名'
Name: 'ADDDATE'
Description:
Syntax:
ADDDATE(date,INTERVAL expr unit), ADDDATE(expr,days)

When invoked with the INTERVAL form of the second argument, ADDDATE()
is a synonym for DATE_ADD(). The related function SUBDATE() is a
synonym for DATE_SUB(). For information on the INTERVAL unit argument,
see the discussion for DATE_ADD().

mysql> SELECT DATE_ADD('2008-01-02', INTERVAL 31 DAY);
        -> '2008-02-02'
mysql> SELECT ADDDATE('2008-01-02', INTERVAL 31 DAY);
        -> '2008-02-02'

When invoked with the days form of the second argument, MySQL treats it
as an integer number of days to be added to expr.

URL: http://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html

Examples:
mysql> SELECT ADDDATE('2008-01-02', 31);
        -> '2008-02-02'
        
</pre>
