<h1 align = "center">MySQL慢sql设置和查询</h1>

# 查询慢sql是否开启

```sql
show variables like 'slow_query_log';
```

# 慢查询所规定的时间

```sql
show variables like 'long_query_time';
```

# 开启慢sql查询(重启后生效)

```sql
set global slow_query_log='ON';
```

# 设置慢sql时间为1s(重启后生效)

```sql
set global long_query_time=1;
```

# 查询慢sql日志文件保存位置

```sql
show variables like 'slow_query_log_file';
```

# 设置慢sql日志文件保存位置

```sql
set global slow_query_log_file='/var/lib/mysql/test_1116.log;
```

# 查看日志文件

```li
sudo subl /var/lib/mysql/test_1116.log
```

