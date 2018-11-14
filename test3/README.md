# 实验3：创建分区表

## Oracle用户名：llwaves

## 实验目的

掌握分区表的创建方法，掌握各种分区方式的使用场景。

## 实验步骤

主表orders和从表order_details之间建立引用分区。在用户中创建两个表：orders（订单表）和order_details（订单详表），两个表通过列order_id建立主外键关联。orders表按范围分区进行存储，order_details使用引用分区进行存储。<br>
1. 登录system用户，用system用户授权自己的账号访问USERS02,USERS03表空间。
<pre>
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 11月 7 09:40:22 2018

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

上次成功登录时间: 星期三 11月 07 2018 08:44:05 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
SQL> ALTER USER llwaves QUOTA 50M ON users02;
用户已更改。
SQL> ALTER USER llwaves QUOTA 50M ON users03;
用户已更改。
SQL> exit
</pre>
2. 登录自己的账号：llwaves去创建表。
<pre>
[oracle@deep02 ~]$ sqlplus llwaves/123@pdborcl

SQL*Plus: Release 12.1.0.2.0 Production on 星期三 11月 7 09:38:22 2018

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

上次成功登录时间: 星期三 11月 07 2018 08:44:05 +08:00

连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL>
</pre>
3. 创建orders表语句及结果。
<pre>
SQL> CREATE TABLE orders 
(
 order_id   NUMBER(10, 0)  primary key NOT NULL 
 , customer_name VARCHAR2(40 BYTE) NOT NULL 
 , customer_tel VARCHAR2(40 BYTE) NOT NULL 
 , order_date DATE NOT NULL 
 , employee_id NUMBER(6, 0) NOT NULL 
 , discount NUMBER(8, 2) DEFAULT 0 
 , trade_receivable NUMBER(8, 2) DEFAULT 0 
) 
TABLESPACE USERS 
PCTFREE 10 INITRANS 1 
STORAGE (   BUFFER_POOL DEFAULT ) 
NOCOMPRESS NOPARALLEL 
PARTITION BY RANGE (order_date) 
(
 PARTITION PARTITION_BEFORE_2016 VALUES LESS THAN (
 TO_DATE(' 2016-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
 'NLS_CALENDAR=GREGORIAN')) 
 NOLOGGING 
 TABLESPACE USERS 
 PCTFREE 10 
 INITRANS 1 
 STORAGE 
( 
 INITIAL 8388608 
 NEXT 1048576 
 MINEXTENTS 1 
 MAXEXTENTS UNLIMITED 
 BUFFER_POOL DEFAULT 
) 
NOCOMPRESS NO INMEMORY  
, PARTITION PARTITION_BEFORE_2017 VALUES LESS THAN (
TO_DATE(' 2017-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS', 
'NLS_CALENDAR=GREGORIAN')) 
NOLOGGING 
TABLESPACE USERS02 
PCTFREE 10
INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
MINEXTENTS 1
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
)
NOCOMPRESS NO INMEMORY
, PARTITION PARTITION_BEFORE_2018 VALUES LESS THAN (
TO_DATE(' 2018-01-01 00:00:00', 'SYYYY-MM-DD HH24:MI:SS',
'NLS_CALENDAR=GREGORIAN'))
NOLOGGING
TABLESPACE USERS03
PCTFREE 10
INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
MINEXTENTS 1
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
)
);

表已创建。

SQL>

</pre>
4. 创建order_details表语句及结果。
<pre>
SQL> CREATE TABLE order_details 
(
id NUMBER(10, 0) NOT NULL 
, order_id NUMBER(10, 0) NOT NULL
, product_id VARCHAR2(40 BYTE) NOT NULL 
, product_num NUMBER(8, 2) NOT NULL 
, product_price NUMBER(8, 2) NOT NULL 
, CONSTRAINT order_details_fk1 FOREIGN KEY  (order_id)
REFERENCES orders(order_id)
ENABLE 
) 
TABLESPACE USERS 
PCTFREE 10 INITRANS 1 
STORAGE (   BUFFER_POOL DEFAULT ) 
NOCOMPRESS NOPARALLEL
PARTITION BY REFERENCE (order_details_fk1)
(
PARTITION PARTITION_BEFORE_2016 
NOLOGGING 
TABLESPACE USERS 
PCTFREE 10
INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
MINEXTENTS 1
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
) 
NOCOMPRESS NO INMEMORY, 
PARTITION PARTITION_BEFORE_2017 
NOLOGGING 
TABLESPACE USERS02
PCTFREE 10
INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
MINEXTENTS 1
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
) 
NOCOMPRESS NO INMEMORY, 
PARTITION PARTITION_BEFORE_2018
NOLOGGING
TABLESPACE USERS03
PCTFREE 10
INITRANS 1
STORAGE
(
INITIAL 8388608
NEXT 1048576
MINEXTENTS 1
MAXEXTENTS UNLIMITED
BUFFER_POOL DEFAULT
)
);

表已创建。

SQL>

</pre>

5. 给用户分配可查询的权限的语句。
<pre>
grant SELECT on "LLWAVES"."ORDERS" to "LLWAVES" ;
grant SELECT on "LLWAVES"."ORDER_DETAILS" to "LLWAVES" ;
</pre>
6. 向表中插入一万条数据
* 向orders表中插入一万条数据。
<pre>
begin
for i in 1..10000
loop
 insert into ORDERS(ORDER_ID,CUSTOMER_NAME,CUSTOMER_TEL,ORDER_DATE,EMPLOYEE_ID,DISCOUNT) VALUES(i ,'llwaves','151xxxxxxxx',to_date('2017-02-14','yyyy-mm-dd'),1,2);
end loop;
commit;
end;
</pre>
* 向order_details表中插入一万条数据。
<pre>
begin
for i in 1..10000
loop
insert into order_details(ID,ORDER_ID,PRODUCT_ID,PRODUCT_NUM,PRODUCT_PRICE) VALUES(i,i,'01-01',i,1000);
end loop;
commit;
end;
</pre>
7. 联合查询语句。
<pre>
SELECT * FROM orders,order_details  Where orders.order_id = order_details.order_id AND customer_tel like '%151%'
</pre>
8. 对查询语句进行分析语句的执行计划。
![](https://github.com/llwaves/oracle/blob/master/test3/3_1.PNG)

## 总结

* 进行分区后的表查询效率更高。
* 掌握了分区表的创建和使用。

