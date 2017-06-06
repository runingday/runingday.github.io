## Mysql 笔记
### 修改同一个表中某个字段的值（关联到同一个表中的其中一个字段及字段值+字符串）
update email_report set email_report.subject=concat(email_report.name, "${data_date}");
