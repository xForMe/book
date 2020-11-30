# mysql

## 容器化

```shell
-- 获取镜像
docker pull mysql
-- 运行镜像
docker run --name mysql_test -p 3306:3306 -e MYSQL_ROOT_PASSWORD=12345 -d mysql

```

## 常用脚本

### ddl

```sql
-- 创建数据库
CREATE DATABASE IF NOT EXISTS sweet
DEFAULT CHARACTER SET utf8
DEFAULT COLLATE utf8_general_ci;
```

### 系统查询

```sql
-- 查看缓存是否开启
SHOW VARIABLES LIKE '%cache%';

```



###  

## in和exist

```sql
t1:1000000/t2:2000000
-- 6s653ms 小表在左，大表在右
select * from t1 where id in (select id from t2);

-- 执行 时间 6s684ms 小表在左，大表在右
select * from t1 where exists (select 1 from t2 where t1.id=t2.id);

-- 1s521ms 大表在左，小表在右
select * from t2 where id in (select id from t1);
-- 1s509ms
select * from t2 where exists (select 1 from t1 where t1.id=t2.id);
```

### exists

在8的版本中exists的子查询，SELECT * 和SELECT 1的效果是等价的，mysql会忽略查找的column list。

```sql
SELECT column1 FROM t1 WHERE EXISTS (SELECT * FROM t2);
```

**attention** 

t2中存在row但是每一列都是null，exists依旧会返回true

## 常见异常

### 错误码1418

```sql
因为CREATE PROCEDURE, CREATE FUNCTION, ALTER PROCEDURE,ALTER FUNCTION,CALL, DROP PROCEDURE, DROP FUNCTION等语句都会被写进二进制日志,然后在从服务器上执行。但是，一个执行更新的不确定子程序(存储过程、函数、触发器)是不可重复的，在从服务器上执行(相对与主服务器是重复执行)可能会造成恢复的数据与原始数据不同，从服务器不同于主服务器的情况。
为了解决这个问题，MySQL强制要求：
在主服务器上，除非子程序被声明为确定性的或者不更改数据，否则创建或者替换子程序将被拒绝。这意味着当创建一个子程序的时候，必须要么声明它是确定性的，要么它不改变数据。
```

解决办法

1. 则在DBA、DBB创建函数时，必须明确指明***\*DETERMINISTIC\****

```sql
delimiter //
drop function if exists insert_datas2//
create function insert_datas2(in_start int(11),in_len int(11)) returns int(11)
DETERMINISTIC
begin
  declare cur_len int(11) default 0;
  declare cur_id int(11);
  set cur_id = in_start;

  while cur_len < in_len do
     insert into t2 values(cur_id,cur_id,'北京');
  set cur_len = cur_len + 1;
  set cur_id = cur_id + 1;
  end while;
  return cur_len;
end
//
delimiter ;
```

2. 

信任子程序的创建者，禁止创建、修改子程序时对SUPER权限的要求，设置log_bin_trust_routine_creators全局系统变量为1。

```sql
SET GLOBAL log_bin_trust_function_creators = 1;
```



## 造数据脚本

```sql
--信任子程序的创建者，禁止创建、修改子程序时对SUPER权限的要求，设置log_bin_trust_routine_creators全局系统变量为1
SET GLOBAL log_bin_trust_function_creators = 1;

delimiter //
drop function if exists insert_datas1//
create function insert_datas1(in_start int(11),in_len int(11)) returns int(11)
begin  declare cur_len int(11) default 0;
declare cur_id int(11);
set cur_id = in_start;
while cur_len < in_len do     insert into t1 values(cur_id,cur_id,'北京');
set cur_len = cur_len + 1;
set cur_id = cur_id + 1;
end while;
return cur_len;
end//
delimiter ; 

```

## 索引

索引数据结构：hash、B+、全文

[教学视频地址](https://www.bilibili.com/video/BV14i4y1V7Nk?p=3)

### B+

非叶子结点不存储数据、叶子结点包含所有字段

![image-20201115205034189](/Users/xforme/Library/Application Support/typora-user-images/image-20201115205034189.png)

#### B+存储索引大小

*每个页大小16K*

```text
每个节点16KB，对id索引则可以存储索引为 16KB/(8+6) = 1170 

第0层 1170 

第一层 1170 * 1170

第三层 主键索引为聚集索引会带上完整的数据记录，假设每行1K，则一个节点可存放16条

总共存储 1170 * 1170 *16 = 21902400
```

#### 聚簇索引和普通索引

聚簇索引叶子结点会带上完整的数据记录，通过普通索引查找到的记录会进行**回表**

**为什么聚簇索引推荐整型并自增**

- int占8个字节

- int比较大小效率高

- 自增方便范围查找

### 联合索引

Key: name+age+position

![image-20201115205913549](/Users/xforme/Library/Application Support/typora-user-images/image-20201115205913549.png)



比较顺序 name->age->dev
