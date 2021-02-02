# Mysql 相关汇总


<!--more-->

## MySql

### **登录**

mysql -u username -p

### **执行SQL文件**

source [path][filename]

### **定向输出到文件**

开启：pager cat >> [path][filename]

关闭：nopager

### **插入更新**

insert ... on duplicate key update ... 当没有时insert，存在时update

### **时间函数**

now() 返回SQL执行的时间 ，不是动态的当前时间

### **ibd文件**

mysql中的ibd文件是数据文件和索引文件，无法直接读取

### **转换数据库**

use \<dataBase>

### **显示表**

show tables

### **显示指定表的表结构**

desc \<tablename>

### **格式化显示**

\\G

### **ContOS7 离线安装mysql**

[安装mysql](https://blog.csdn.net/u012802702/article/details/68070547)

### **备份表结构和数据**

```sql
mysqldump -h\<IP> -u\<name> -p\<pwd> \<dbname> \<tablename> > /root/trdSymbols.sql
--where=" exchange='aaa'  过滤条件
--extented-insert=false  分成多条
```

### **限制显示条数**

select ... limit n   显示n条

select ... limit m n   从m+1条后面显示n条

### **generator**

```sql
--eclipse中生成：maven build ...  ->  mybatis-generator:generate
--注释乱码问题：xml文件中<commentGenerator>里面添加<property name="javaFileEncoding" value="UTF-8"/>  ->  exlipse.ini 文件，最末尾加上 -Dfile.encoding=UTF-8
```

### **隔离级别**

[隔离级别](http://www.cnblogs.com/zhoujinyi/p/3437475.html)

### **replace into 识别同一条记录**

[URL](https://www.cnblogs.com/c-961900940/p/6197878.html)

### **设置默认值**

```sql
alter table trd_orders alter column margin drop default; (若本身存在默认值，则先删除)
alter table trd_orders alter column margin set default '';(若本身不存在则可以直接设定)
```

### **查看建表语句**

```sql
show create table table_name;
```

### **更改线上数据库**

```sql
begin;

select count(*) from trd_orders where exchange <> 'BITMEX';

update trd_orders set SecurityType = 0 where exchange <> 'BITMEX';

commit;
```

### **插入列**

```sql
ALTER table `trd_symbols` ADD `position_currency` char(10) CHARACTER SET utf8 COLLATE utf8_unicode_ci DEFAULT NULL COMMENT '期货，持仓货币';
```

### **修改列类型**

```sql
ALTER table <tablename> modify  column <columnname> <type> DEFAULT NULL COMMENT <comment>;
-- 正常，能修改字段类型、类型长度、默认值、注释
```

### **修改列默认值**

```sql
alter table 表名 alter column 字段名 drop default; (若本身存在默认值，则先删除)
alter table 表名 alter column 字段名 set default 默认值;(若本身不存在则可以直接设定)
```

### **增加唯一键索引**

```sql
alter table trd_orders add UNIQUE index `un_cl_order_id` (`cl_ord_id`,`account_id`) USING HASH;
```

### **替换字符串**

```sql
replace(object, search,replace)
```

### **bash执行mysql**

```sql
-- 通过EOF

mysql -h10.35.174.248 -uroot -pnextfintech@2018 tradingcenter << EOF
  
  use tradingcenter
  
  source /root/symbolJob/sql/bitmex.sql;
  
  exit

EOF

-- 通过 -e

mysql -h<ip> -u<username> -p<password> <dbname> -e "sql语句" >> <path><filename>

mysql -h10.35.174.248 -uroot -pnextfintech@2018 tradingcenter -e "select count(*) from trd_orders where strategy_id = '410eacec-34f7-11e9-afbc-00ff38c49b16' and created_at > '2019-02-25 12:00' and created_at < '2019-02-26 12:00'\G" >> /home/sqlite
```

### **自增序列 auto_increment**

可以在表的基本信息中查看

```sql
SELECT * FROM information_schema.tables WHERE table_name = <tableName> ;
```

如果为AUTO_INCREMENT字段明确指定了一个数值，则会出现两种情况

1. 如果插入的值与已有的编号重复，则会出现出 错信息，因为AUTO_INCREMENT数据列的值必须是唯一的

2. 如果插入的值大于已编号的值，则会把该插入到数据列中，并使在下一个编号将从这个新值开始递增

### **联合索引**

```sql
--复合索引 <emp_no, title, from_date>

select * from table where emp_no = '1' and title = 'aaa' and from_date = '1990' ;   --全值匹配

select * from table where title = 'aaa';   --不遵循最左前缀, 不明中索引

select * from table where emp_no > '1';  --命中索引, 范围查找.

select * from table where emp_no > '1' and title = 'aaa';  --emp_no命中索引, 但是范围查找, title不能走索引.

select * from table where emp_no > '1' order by title;    --emp_no命中索引, 同样title不能走索引;
```

### **其他数据结构**

二叉树作为索引有可能会出现单边增长的情况.  O(log2n)

红黑树会自平衡, 不会出现单边增长的情况. JDK1.8 的 hashmap 是用的红黑树. 数据量大的时候, 深度不可控.

HASH 如果是定位到某一行,通过 hash 算法计算出地址,是很快, 不满足范围查找.

### **BTREE**

1. 度(Degree)是每个节点存储数据的最大个数

2. 叶子节点具有相同的深度

3. 叶子阶段的指针为空

4. 叶子节点的数据从左到右顺序排列

每个节点内的顺序查找实在内存中查找, 几乎可以忽略不计

Degree 可以设计为磁盘一次I/O读取的数据数

BTREE 的每个节点中除了存储键值, 还有data数据, 但是如果一行的col太多, data也会很大, 也不适合.  所以引入了BTREE的变种 B+TREE.

### **B+TREE**

1. 非叶子节点不存储data, 之存储key , 在容量和表大小确定的情况下, 可以增大Degree, 减少深度, 也就减少 I/O 次数.

2. B+ TREE的Degree一般会在100以上, h一般在3-5之间.

3. 叶子节点增加了顺序访问指针, 提高范围查找性能. 不用返回上层,减少了I/O操作.

**预读**: 磁盘在一次 I/O 中, 会把要读取的数据相邻的局部数据一起存入内存, 并且是页的整倍数(4K). BTREE的一个节点的大小一般一会设置为4K.新建节点的时候会直接申请一页的内存, 使得一个节点在物理上也存储在一个页里面.

### **聚簇索引**

1. 叶子节点中将索引键值和data存放在一起.

2. 会自动选择为主键.

3. 非主键索引不是聚簇索引

### **存储引擎**

1. 是表级别

### **InnoDB**

1. 主键索引和非主键索引的存储结构不一样

2. 数据文件本身就是索引文件, 主键聚簇索引, 内存不能全部存储一个索引文件, 所以会产生I/O, B+TREE结构可以减少I/O次数.

3. Secondary key 不是聚簇索引, 但是叶子节点会存储Primary key

4. 主键推荐是整型的自增序列, 因为InnoDB的存储结构, 是B+ TREE的索引文件, 自增整型检索速度比UUID要快, 并且影响二级索引的大小

### **索引策略**

1. 索引必须是独立的列, 不能用表达式或者函数的入参.

2. 遵循最左前缀原则.

3. 复合索引中, 范围查找影响索引.比如like和in还有><这种.

## Oracle

### **查看表空间**

```sql
select tablespace_name, file_id, file_name,round(bytes/(1024*1024),0) total_space from dba_data_files order by tablespace_name;
```

### **创建表空间**

```sql
create tablespace hussar2 datafile 'G:\hussar01.DBF' size 500M autoextend on next 100M maxsize unlimited
```

### **删除表空间**

```sql
DROP TABLESPACE hussar2 INCLUDING CONTENTS AND DATAFILES;
```

### **扩展表空间**

```sql
ALTER TABLESPACE hussar2 add DATAFILE 'G:\hussar02.DBF' size 500M AUTOEXTEND ON NEXT 100M MAXSIZE unlimited;
```

### **计算100天之前的日期**

```sql
select to_date('2017-06-01', 'yyyy-mm-dd') - 100 as time from dual;
```

### **case when**

在检索列中的case when列，不能在where条件中直接用，可以用with as临时表将其提出来。

### **decode**

decode(条件,值1,返回值1,值2,返回值2,...值n,返回值n,缺省值)，相当于IF语句，用值和条件对比，替换成返回值。

### **instr**

instr（源字符串 , 目标字符串 ,开始位置,第几次出现）

### **递归查询**

```sql
--树节点及其子节点

select  <检索列>
  from  <table> where <condition>
  START WITH  <root>
CONNECT BY <connect condition>
  order by <sort>
```

简单说来是将一个树状结构存储在一张表里，比如一个表中存在两个字段:code，parent_code，那么通过表示每一条记录的parent是谁，就可以形成一个树状结构，用上述语法的查询可以取得这棵树的所有记录，其中:

1. 条件1 是根结点的限定语句，当然可以放宽限定条件，以取得多个根结点，实际就是多棵树。

2. 条件2 是连接条件，其中用PRIOR表示上一条记录，比如 CONNECT BY PRIOR code = parent_code；就是说上一条记录的code 是本条记录的parent_code，即本记录的父亲是上一条记录。

3. 条件3 是过滤条件，用于对返回的所有记录进行过滤。

## DB2

```sql
locate(arg1,arg2,<pos>)

-- 查找arg2中第一次出现arg1的位置，指定pos，则从arg2的pos处开始找arg1第一次出现的位置
-- 0:未找到arg1
-- 1:在第一个
```

### **order by n**

按照第n个栏位排序

### **days**

比较两个日期的大小，用days()可以做比较，

### **1 MONTH + 10 DAYS**

下个月的今天的十天以后，可以直接在日期后面加上。

### **NOT IN**

对于在某个范围内的，用 in (select...) ，如果是他的余集，可以用 NOT IN (select ...)，即不在这个范围内的结果集。

### **IN**

当用到in的时候，不得不说到从前面向后面传ID字符串来直接放在in里面，但是不能直接在字符串后面拼接“，”或者下一个ID，应该这样：id_hbzl = id_hbzl +"','"+ ress['id'];然后在后台接受的时候，也要用''单引号包起来。

### **重组表结构**

```sql
call admin_cmd('reorg table ENVIR.T_ATTACHFILE_SERVICE_XKZ_HB2');
```

### **CAST**

是一个转换格式的函数，具体用法为：cast(el as type);

### **with \<temptablename> as ()**

将要查询的内容组成一个临时表，在SQL中后面的select,update,delete中用到。

可以重复利用，尤其像UNION ALL这种查询中，可以将重复的的查询提出来。

有时候，会需要一些聚合函数会碰到group by分组的情况，如果一味的left join会使得其他的表格查的很不方便。

后面可以定义多个临时表，用逗号分开，最后一个不用。

### **问题1**

```sql
--库中查出两个用逗号连载一块的字符串id，要显示成汉字value，但是字典表中是一对一的。
SELECT   ( SELECT  
  REPLACE(REPLACE(XML2CLOB(XMLAGG(XMLELEMENT(NAME A, REMARK||','))),'<A>',''),'</A>','')
    FROM
    PLATFORM.DICT
        WHERE
            PARENT_ID IN
                (
                  SELECT
                ID
              FROM
            PLATFORM.DICT
          WHERE
        NAME = 'FILTER_TYPE')
      AND LOCATE( ''''||NAME||'''' , ''''||REPLACE(GSCCJLX,',',''',''')||'''') > 0) GSCCJLX
    FROM
  ENVIR.T_ZDL_UNIT_INDVAL
```

### **LEFT(ARG,LENGTH)、RIGHT(ARG,LENGTH)**

返回ARG字符串的左边、右边length长度的子字符串

### **NVL(ARG,0)**

返回arg，如果arg为null，返回0

