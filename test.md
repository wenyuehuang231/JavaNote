@[TOC](各类数据库sql时间类型比较大小)
# 背景

> 测试各类数据库时间类型比较大小，整理了该篇详尽博客。

# 测试
## Mysql
**库表创建**
```sql
CREATE TABLE `test_time` (
  `id` int(10) NOT NULL,
  `date001` date DEFAULT NULL,
  `datetime001` datetime DEFAULT NULL,
  `timestamp001` timestamp NULL DEFAULT NULL,
  `time001` time DEFAULT NULL,
  `year001` year(4) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

BEGIN;
INSERT INTO `test_time` VALUES (1, '2021-07-29', '2021-07-29 16:19:28', '2021-07-29 16:19:30', '16:19:33', 2010);
INSERT INTO `test_time` VALUES (2, '2021-09-15', '2021-09-15 16:19:57', '2021-09-15 16:20:10', '16:20:21', 2022);
INSERT INTO `test_time` VALUES (3, '2021-08-18', '2021-08-18 16:20:36', '2021-08-18 16:20:45', '16:20:50', 2019);
COMMIT;
```

**时间比较**
```sql
SELECT * FROM test_time WHERE date001 > '2021-08-18';
SELECT * FROM test_time WHERE datetime001 > '2021-08-18 16:20:36';
SELECT * FROM test_time WHERE timestamp001 > '2021-08-18 16:20:45';
SELECT * FROM test_time WHERE time001 > '16:20:00';
SELECT * FROM test_time WHERE year001 > '2018';
```
## Oracle
**库表创建**
```sql
CREATE TABLE "test"."test_time" (
  "ID" NUMBER,
  "DATE001" DATE,
  "TIMESTAMP001" TIMESTAMP(6),
  "YEARTOMONTH001" INTERVAL YEAR(2) TO MONTH,
  "DAYTOSECOND001" INTERVAL DAY(2) TO SECOND(6)
)

INSERT INTO "test"."test_time" ("ID", "DATE001", "TIMESTAMP001", "YEARTOMONTH001", "DAYTOSECOND001") VALUES ('1', TO_DATE('2021-07-29 19:09:40', 'SYYYY-MM-DD HH24:MI:SS'), TO_TIMESTAMP('2021-07-29 19:09:43.000000', 'SYYYY-MM-DD HH24:MI:SS:FF6'), TO_YMINTERVAL('+10-01'), TO_DSINTERVAL('+10 09:08:07.660000'));
INSERT INTO "test"."test_time" ("ID", "DATE001", "TIMESTAMP001", "YEARTOMONTH001", "DAYTOSECOND001") VALUES ('2', TO_DATE('2021-09-16 19:16:28', 'SYYYY-MM-DD HH24:MI:SS'), TO_TIMESTAMP('2021-09-16 19:16:47.000000', 'SYYYY-MM-DD HH24:MI:SS:FF6'), TO_YMINTERVAL('+08-02'), TO_DSINTERVAL('+01 01:01:01.010000'));
INSERT INTO "test"."test_time" ("ID", "DATE001", "TIMESTAMP001", "YEARTOMONTH001", "DAYTOSECOND001") VALUES ('3', TO_DATE('2021-08-26 19:18:34', 'SYYYY-MM-DD HH24:MI:SS'), TO_TIMESTAMP('2021-08-26 19:18:39.000000', 'SYYYY-MM-DD HH24:MI:SS:FF6'), TO_YMINTERVAL('+06-02'), TO_DSINTERVAL('+05 05:05:05.550000'));
```
**时间比较**

> INTERVAL YEAR TO MONTH 和 INTERVAL DAY TO SECOND 为特殊字段，存储的是一段间隔时间，不纳入比较范围。
> 参考：https://www.yiibai.com/oracle/oracle-interval.html

```sql
SELECT * FROM test."test_time" WHERE DATE001 > TO_DATE('2021-09-16 19:16:20', 'yyyy-mm-dd hh24:mi:ss');
SELECT * FROM test."test_time" WHERE TIMESTAMP001 > TO_DATE('2021-08-26 19:18:39', 'yyyy-mm-dd hh24:mi:ss');
```
## Db2
**库表创建**

```sql
CREATE TABLE "test"."TEST_TIME"  (
  "ID" BIGINT , 
  "TIME001" TIME , 
  "TIMESTAMP001" TIMESTAMP , 
  "DATE001" DATE
)   
IN "USERSPACE1"  
ORGANIZE BY ROW;
## 授权
GRANT CONTROL ON TABLE "test"."TEST_TIME" TO USER "DB2INST1";

INSERT INTO TEST_TIME(ID, TIME001, TIMESTAMP001, DATE001)
VALUES(1, '16:19:33', '2021-07-29 16:19:30.000000', '2021-07-29');
INSERT INTO TEST_TIME(ID, TIME001, TIMESTAMP001, DATE001)
VALUES(2, '16:20:21', '2021-09-15 16:20:10', '2021-09-15');
INSERT INTO TEST_TIME(ID, TIME001, TIMESTAMP001, DATE001)
VALUES(3, '16:20:50', '2021-08-18 16:20:36', '2021-08-18');
```

**时间比较**

> ①DATE：DATE 是一个由三部分组成的值（年、月和日）。年份部分的范围是从 0001 到 9999。月份部分的范围是从 1 到 12。日部分的范围是从 1 到 n，其中 n 的值取决于月份。DATE 列长 10 个字节。
> ②TIME：TIME 是一个由三部分组成的值（小时、分钟和秒）。小时部分的范围是从 0 到 24。分钟和秒部分的范围都是从 0 到 59。如果小时为 24，分钟和秒的值都是 0。TIME 列长 8 个字节。
> ③TIMESTAMP：TIMESTAMP 是一个由七部分组成的值（年、月、日、小时、分钟、秒和微秒）。年份部分的范围是从 0001 到 9999。月份部分的范围是从 1 到 12。日部分的范围是从 1 到 n，其中 n 的值取决于月份。小时部分的范围是从 0 到 24。分钟和秒部分的范围都是从 0 到 59。微秒部分的范围是从 000000 到 999999。如果小时是 24，那么分钟值、秒的值和微秒的值都是 0。TIMESTAMP 列长 26 个字节。

```sql
SELECT * FROM test.TEST_TIME WHERE DATE001 > '2021-08-18';
SELECT * FROM test.TEST_TIME WHERE TIMESTAMP001 > '2021-08-18 16:20:45';
SELECT * FROM test.TEST_TIME WHERE TIME001 > '16:20:00';
```
## SqlServer
**库表创建**

```sql
CREATE TABLE [test].[test_time] (
  [id] int  NULL,
  [date001] date  NULL,
  [datetime001] datetime  NULL,
  [datetime2001] datetime2(7)  NULL,
  [datetimeoffset001] datetimeoffset(7)  NULL,
  [smalldatetime001] smalldatetime  NULL,
  [time001] time(7)  NULL,
  [timestamp001] timestamp  NOT NULL
)
GO

BEGIN TRANSACTION
GO
INSERT INTO [test].[test_time] VALUES (N'1', N'2021-07-29', N'2021-07-30 14:19:48.000', N'2021-07-30 14:19:48.0000000', N'2021-07-30 14:19:48.0000000 +00:00', N'2021-07-30 14:20:00', N'16:19:33.0000000', 0x00000000000007DD)
GO
INSERT INTO [test].[test_time] VALUES (N'2', N'2021-09-15', N'2021-09-30 14:19:59.000', N'2021-09-30 14:19:59.0000000', N'2021-09-30 14:19:59.0000000 +00:00', N'2021-09-30 14:20:00', N'16:20:22.0000000', 0x00000000000007E1)
GO
INSERT INTO [test].[test_time] VALUES (N'3', N'2021-08-18', N'c.000', N'2021-08-26 14:20:14.0000000', N'2021-08-26 14:20:14.0000000 +00:00', N'2021-08-24 14:20:00', N'16:20:50.0000000', 0x00000000000007DF)
GO
COMMIT
GO
```

> CONVERT() 函数可以涵盖绝大部分MSSQL日期类型比较大小
> CONVERT() 函数参考：https://www.w3school.com.cn/sql/func_convert.asp

> timestamp 属于特殊数据类型，特别注意的是**这个字段是会根据你插入和更新自动更新值（时间戳）**，并且比较方式与其他日期类型有所区别。
> timestamp 参考（了解概念）：https://www.cnblogs.com/OpenCoder/p/10411186.html

**时间比较**

```sql
SELECT * FROM test.test_time WHERE datetime001 < convert(datetime,'2021-08-27 14:20:14');
SELECT * FROM test.test_time WHERE date001 < convert(date,'2021-08-17');
SELECT * FROM test.test_time WHERE datetime2001 < convert(datetime2,'2021-09-06 14:19:59');
SELECT * FROM test.test_time WHERE datetimeoffset001 < convert(datetimeoffset,'2021-08-20 14:19:59');
SELECT * FROM test.test_time WHERE smalldatetime001 < convert(smalldatetime,'2021-08-26 14:20:00');
SELECT * FROM test.test_time WHERE timestamp001 < 0x00000000000007DF;
```
## PostgreSQL
**库表创建**
```sql
CREATE TABLE test.test_time (
	id int2 NULL,
	abstime001 abstime NULL,
	date001 date NULL,
	reltime001 reltime NULL,
	time001 time(0) NULL,
	time_stamp001 information_schema."time_stamp" NULL,
	timestamp001 timestamp(0) NULL,
	timestamptz001 timestamptz(0) NULL,
	timetz001 timetz NULL
);

INSERT INTO test_time (id, abstime001, date001, reltime001, time001, time_stamp001, timestamp001, timestamptz001, timetz001)
VALUES(3, '2021-08-29', '2021-08-29', '18:19:33'::reltime, '18:19:33', '2021-08-29 19:09:43.000', '2021-08-29 19:09:43.000', '2021-08-29 19:09:43.000', '18:19:33');
INSERT INTO test_time (id, abstime001, date001, reltime001, time001, time_stamp001, timestamp001, timestamptz001, timetz001)
VALUES(1, '2021-07-29', '2021-07-29', '16:19:33'::reltime, '16:19:33', '2021-07-30 16:51:04.280', '2021-07-30 16:51:04.000', '2021-07-30 16:51:04.000', '16:19:33');
INSERT INTO test_time (id, abstime001, date001, reltime001, time001, time_stamp001, timestamp001, timestamptz001, timetz001)
VALUES(2, '2021-09-29', '2021-09-29', '20:19:33'::reltime, '20:19:33', '2021-09-30 16:56:00.000', '2021-09-30 16:56:00.000', '2021-09-30 16:56:00.000', '20:19:33');
```

> 这里我发现了俩个很有意思的字段：timestamp 和 timestamptz
> timestamp 数据类型可以同时存储日期和时间，但它不存储时区；
> timestamptz 数据类型在存储日期和时间的同时还能正确处理时区。
> 变更方式：修改数据库服务器所在的时区，timestamptz也会随之变更。
> 参考：https://www.jianshu.com/p/b3cfc0912328

**时间比较**
```sql
select * from test.test_time where abstime001 < '2021-08-29';
select * from test.test_time where date001 < '2021-08-29';
select * from test.test_time where reltime001 < '18:19:33';
select * from test.test_time where time001 < '18:19:33';
select * from test.test_time where time_stamp001 < '2021-08-29 19:09:43.000';
select * from test.test_time where timestamp001 < '2021-08-29 19:09:43.000';
select * from test.test_time where timestamptz001 < '2021-08-29 19:09:43.000';
select * from test.test_time where timetz001 < '18:19:33';
```
## Greenplum
> 值得一说的是：GP是在开源的 PostgreSQL 基础上开发的。GP是商业版的，PostgreSQL是开源的。
> 所以这俩种数据库是很相近的，大致上没什么区别。
> https://gp-docs-cn.github.io/docs/admin_guide/intro/arch_overview.html

**库表创建**
```sql
CREATE TABLE test.test_time (
	id int2 NULL,
	abstime001 abstime NULL,
	date001 date NULL,
	reltime001 reltime NULL,
	time001 time(0) NULL,
	time_stamp001 information_schema.time_stamp NULL,
	timestamp001 timestamp(0) NULL,
	timestamptz001 timestamptz(0) NULL,
	timetz001 timetz NULL
);
INSERT INTO test.test_time(id, abstime001, date001, reltime001, time001, time_stamp001, timestamp001, timestamptz001, timetz001)
VALUES(3, '2021-08-29 00:00:00+08'::abstime, '2021-08-29', '18:19:33'::reltime, '18:19:33', '2021-08-29 19:09:43.0'::time_stamp, '2021-08-29 19:09:43.000', '2021-08-29 19:09:43.000', '18:19:33');
INSERT INTO test.test_time(id, abstime001, date001, reltime001, time001, time_stamp001, timestamp001, timestamptz001, timetz001)
VALUES(1, '2021-07-29 00:00:00+08'::abstime, '2021-07-29', '16:19:33'::reltime, '16:19:33', '2021-07-30 16:51:04.28'::time_stamp, '2021-07-30 16:51:04.000', '2021-07-30 16:51:04.000', '16:19:33');
INSERT INTO test.test_time(id, abstime001, date001, reltime001, time001, time_stamp001, timestamp001, timestamptz001, timetz001)
VALUES(2, '2021-09-29 00:00:00+08'::abstime, '2021-09-29', '20:19:33'::reltime, '20:19:33', '2021-09-30 16:56:00.0'::time_stamp, '2021-09-30 16:56:00.000', '2021-09-30 16:56:00.000', '20:19:33');
```

**时间比较**

```sql
select * from test.test_time where abstime001 < '2021-08-29';
select * from test.test_time where date001 < '2021-08-29';
select * from test.test_time where reltime001 < '18:19:33';
select * from test.test_time where time001 < '18:19:33';
select * from test.test_time where time_stamp001 < '2021-08-29 19:09:43.000';
select * from test.test_time where timestamp001 < '2021-08-29 19:09:43.000';
select * from test.test_time where timestamptz001 < '2021-08-29 19:09:43.000';
select * from test.test_time where timetz001 < '18:19:33';
```

## Informix
**库表创建**
```sql
CREATE TABLE test.test_time (
	id bigint,
	date001 date,
	datetime001 datetime year to fraction(5)
);

INSERT INTO test.test_time(id, date001, datetime001)
VALUES(1, '2021-07-29', '2021-07-30 14:19:48.000');
INSERT INTO test.test_time(id, date001, datetime001)
VALUES(2, '2021-09-15', '2021-09-30 14:19:59.000');
INSERT INTO test.test_time(id, date001, datetime001)
VALUES(3, '2021-08-18', '2021-08-26 14:20:14.000');

```
**时间比较**
```sql
SELECT * FROM test.test_time WHERE date001 < '2021-08-17';
SELECT * FROM test.test_time WHERE datetime001 < '2021-09-06 14:19:59';
```
## Sybase
**库表创建**
```sql
CREATE TABLE test.test_time (
	id bigint NULL,
	date001 date NULL,
	datetime001 datetime NULL,
	time001 time(3) NULL,
	smalldatetime001 smalldatetime NULL
) GO;
INSERT INTO test.test_time(id, date001, datetime001, time001, smalldatetime001)
VALUES(1, '2021-07-29', {ts '2021-07-30 14:19:48.000000'}, '16:19:33.000', {ts '2021-07-30 14:20:00.000000'});
INSERT INTO test.test_time(id, date001, datetime001, time001, smalldatetime001)
VALUES(2, '2021-09-15', {ts '2021-09-30 14:19:59.000000'}, '20:19:33.000', {ts '2021-09-30 14:20:00.000000'});
INSERT INTO test.test_time(id, date001, datetime001, time001, smalldatetime001)
VALUES(3, '2021-08-18', {ts '2021-08-26 14:20:14.000000'}, '18:19:33.000', {ts '2021-08-26 14:20:00.000000'});

```
**时间比较**
```sql
SELECT * FROM test.test_time WHERE date001 < '2021-08-17';
SELECT * FROM test.test_time WHERE datetime001 < '2021-09-06 14:19:59';
SELECT * FROM test.test_time where time001 < '18:19:33';
SELECT * FROM test.test_time where smalldatetime001 < '2021-09-06 14:19:59';
```
## MaxCompute（odps）
**库表创建**
```sql
CREATE TABLE IF NOT EXISTS `test`.`test_time` (
  `id` BIGINT,
  `datetime001` DATETIME,
  `timestamp001` TIMESTAMP
);

insert into test_time(id, datetime001, timestamp001) 
VALUES (1, to_date('2021-07-30 14:19:48', 'yyyy-mm-dd hh:mi:ss'), from_utc_timestamp('2021-07-30 09:19:48.0','CST'));
insert into test_time(id, datetime001, timestamp001) 
VALUES (2, to_date('2021-09-30 14:19:59', 'yyyy-mm-dd hh:mi:ss'), from_utc_timestamp('2021-09-30 14:19:59.619','CST'));
insert into test_time(id, datetime001, timestamp001) 
VALUES (3, to_date('2021-08-26 14:20:14', 'yyyy-mm-dd hh:mi:ss'), from_utc_timestamp('2021-08-26 14:20:14.2','CST'));

```
**时间比较**
```sql
select * from test_time where datetime001 < '2021-09-06 14:19:59';
select * from test_time where timestamp001 < '2021-09-06 14:19:59';
```

## Kingbase8
**库表创建**
```sql
create table TEST.TEST_TIME
(
	ID INT2(5) not null constraint TEST_TIME_PK primary key,
	DATE001 DATE(13),
	TIMESTAMP001 TIMESTAMP(29,6),
	TIME001 TIME(15,6)
);

insert into TEST.TEST_TIME(id, date001, time001, timestamp001)
VALUES (1, '2021-07-29', '16:19:33', '2021-07-30 14:20:00.000000');
insert into TEST.TEST_TIME(id, date001, time001, timestamp001)
VALUES (2, '2021-09-15', '18:19:33', '2021-09-30 14:20:00.000000');
insert into TEST.TEST_TIME(id, date001, time001, timestamp001)
VALUES (3, '2021-08-17', '20:19:33', '2021-08-26 14:20:00.000000');

```
**时间比较**
```sql
SELECT * FROM TEST.TEST_TIME WHERE date001 < '2021-08-17';
SELECT * FROM TEST.TEST_TIME where time001 < '18:19:33';
SELECT * FROM TEST.TEST_TIME where TIMESTAMP001 < '2021-09-06 14:19:59';
```
