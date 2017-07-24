
# 数据去重

<pre>
create table event_p4_0714 like event_p4;

insert into event_p4_0714 select distinct * from event_p4 where date between '2017-07-07' and '2017-07-12' and event_id in (84,85);

select count(1) from event_p4 where date between '2017-07-07' and '2017-07-12' and event_id in (84,85); 查看第一条及第二条之间的差值

commit

delete from event_p4 where date between '2017-07-07' and '2017-07-12' and event_id in (84,85);  删除有重复的数据

insert into event_p4 select * from event_p4_0714; 插入去重后的数据
</pre>
