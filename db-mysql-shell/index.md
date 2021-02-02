# MySQL Shell 常规命令汇总


记录一些MySQL在Linux环境中的运维是可能用到的命令.

<!--more-->

## 使用sh脚本操作MySQL

### 一般性操作

包括数据库的创建以及数据库表的操作

```shell
#!/bin/bash
HOSTNAME="127.0.0.1"
PORT="3306"
USERNAME="root"
PASSWORD=""
DBNAME="test_db"
TABLENAME="test_table" 

#创建数据库
create_db_sql="create database IF NOT EXISTS ${DBNAME}"
mysq l -h${HOSTNAME}   -P${PORT}    -u${USERNAME}  -p${PASSWORD}   -e"${create_db_sql}"
 
#创建表
create_table_sql="create table IF NOT EXISTS ${TABLENAME} ( name varchar(20), id int(11) default 0 )"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD}   ${DBNAME}   -e"${create_table_sql}"
 
#插入数据
insert_sql="insert into ${TABLENAME} values('billchen',2)"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD}  ${DBNAME}   -e"${insert_sql}"
 
#查询
select_sql="select * from ${TABLENAME}"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD} ${DBNAME} -e"${select_sql}"
 
#更新数据
update_sql="update ${TABLENAME} set id=3"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD} ${DBNAME} -e"${update_sql}"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD} ${DBNAME} -e"${select_sql}"
 
#删除数据
delete_sql="delete from ${TABLENAME}"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD} ${DBNAME} -e"${delete_sql}"
mysql -h${HOSTNAME}  -P${PORT}  -u${USERNAME} -p${PASSWORD} ${DBNAME} -e"${select_sql}"
```

### 特殊操作

shell中的data作为参数, 执行MySQL语句

```shell
#!/bin/bash
# get new order createdtime
now=`date -d'-2 minutes' '+%G-%m-%d %H:%M:%S'`

# mysql命令
orderTime=$(mysql -h127.0.0.1 -uroot test -e "select count(*) from tablename where created_at > '${now}';")

# 执行mysql命令
echo $orderTime

# 获取结果
arr=(${orderTime})

# 进行结果处理
echo ${arr[1]}
if [ ${arr[1]} -gt 0 ];then
echo YES
else
echo NO
fi

# check time with now
now=`date '+%G-%m-%d %H:%M:%S'`
echo $ expr '(' $orderTime - $now ')'
```
