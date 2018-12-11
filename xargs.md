使用xargs 参数传递批量删除表
```

  impala-shell -d default -B -q 'show tables' | grep "production_*" | xargs -I '{}' impala-shell -d default -q "drop table {}"
  
```
