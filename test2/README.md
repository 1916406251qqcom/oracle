# 实验二：用户管理 - 掌握管理角色、权根、用户的能力，并在用户之间共享对象
## 实验步骤
* 第1步：以system登录到pdborcl，创建角色con_res_view_l和用户llwaves，并授权和分配空间：
<pre>
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 08:51:07 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
上次成功登录时间: 星期三 10月 24 2018 08:50:49 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
SQL> create role con_res_view_l;
角色已创建。
SQL> grant connect,resource,create view to con_res_view_l;
授权成功。
SQL> create user llwaves IDENTIFIED BY 123 DEFAULT TABLESPACE users TEMPORARY TABLESPACE temp;
用户已创建。
SQL> ALTER USER llwaves QUOTA 50M ON users;
用户已更改。
SQL> grant con_res_view_l to llwaves;
授权成功。
SQL> exit
</pre>
> 创建的用户名为：***llwaves***。语句“ALTER USER llwaves QUOTA 50M ON users;”是指授权llwaves用户访问users表空间，空间限额是50M。
* 第2步：新用户llwaves连接到pdborcl，创建表mytable和视图myview，插入数据，最后将myview的SELECT对象权限授予hr用户。
<pre>
[oracle@deep02 ~]$ sqlplus llwaves/123@pdborcl
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 09:01:02 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options
SQL> show user;
USER 为 "LLWAVES"
SQL> CREATE TABLE mytable(id number,name varchar(50));
表已创建。
SQL> INSERT INTO mytable (id,name)VALUES(1,'zhang');
已创建 1 行。
SQL> INSERT INTO mytable (id,name)VALUES(1,'wang');
已创建 1 行。
SQL> CREATE VIEW myview AS SELECT name FROM mytable;
视图已创建。
SQL> SELECT * FROM myview;
NAME
--------------------------------------------------------------------------------
zhang
wang

SQL> GRANT SELECT ON myview TO hr;
授权成功。
SQL> exit
</pre>
> 创建一个名为mytable的表，并向表中插入相关值，并将查询权限赋给hr用户,让hr用户可以查询表中的值。
* 第三步：用户hr连接到pdborcl，查询llwaves授予它的视图myview。
<pre>
[oracle@deep02 ~]$ sqlplus hr/123@pdborcl
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 09:05:18 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
上次成功登录时间: 星期三 10月 24 2018 09:04:47 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> SELECT * FROM llwaves,myview;
SQL> SELECT * FROM llwaves.myview;
NAME
--------------------------------------------------------------------------------
zhang
wang

SQL> exit
</pre>
* 第四步：查看数据库的使用情况
<pre>
[oracle@deep02 ~]$ sqlplus system/123@pdborcl

SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 09:41:12 2018

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

上次成功登录时间: 星期三 10月 24 2018 09:40:56 +08:00

连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> SELECT tablespace_name,FILE_NAME,BYTES/1024/1024 MB,MAXBYTES/1024/1024 MAX_MB,autoextensible FROM dba_data_files  WHERE  tablespace_name='USERS ';

TABLESPACE_NAME
--------------------------------------------------------------------------------
FILE_NAME
--------------------------------------------------------------------------------
        MB     MAX_MB AUTOEXTEN
---------- ---------- ---------
USERS
/home/oracle/app/oracle/oradata/orcl/pdborcl/SAMPLE_SCHEMA_users01.dbf
         5 32767.9844 YES


SQL> SELECT a.tablespace_name "表空间名",Total/1024/1024 "大小MB",
free/1024/1024 "剩余MB",( total - free )/1024/1024 "使用MB",
Round(( total - free ) / total,4)* 100 "使用率%"
from (SELECT tablespace_name,Sum(bytes)free
FROM   dba_free_space group  BY tablespace_name)a,
(SELECT tablespace_name,Sum( bytes)total FROM dba_data_files
group  BY tablespace_name)b where  a.tablespace_name = b.tablespace_name;

表空间名
--------------------------------------------------------------------------------
    大小MB     剩余MB     使用MB    使用率%
---------- ---------- ---------- ----------
SYSAUX
       630    38.9375   591.0625      93.82

USERS
         5        .75       4.25         85

SYSTEM
       270     3.5625   266.4375      98.68


表空间名
--------------------------------------------------------------------------------
    大小MB     剩余MB     使用MB    使用率%
---------- ---------- ---------- ----------
EXAMPLE
  1281.875      62.25   1219.625      95.14


SQL> exit
</pre>
* autoextensible是显示表空间中的数据文件是否自动增加。
* MAX_MB是指数据文件的最大容量。
## 总结分析
<pre>
1. 数据库pdborcl中包含了自己和其他同学的角色和用户
2. 所有同学的用户都使用表空间users存储表的数据。
3. 表空间中存储了很多相同名称的表mytable和视图myview，但分别属性于不同的用户，不会引起混淆。
4 .随着用户往表中插入数据，表空间的磁盘使用量会增加。
</pre>
