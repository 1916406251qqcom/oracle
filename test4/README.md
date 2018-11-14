# 实验4：对象管理

## 此次实验删除了实验3所创建的表。Oracle用户名：llwaves。

## 实验步骤
1. 登录system账号，为自己的账号llwaves分配相关权限。
<pre>
[oracle@deep02 ~]$ sqlplus system/123@pdborcl

SQL*Plus: Release 12.1.0.2.0 Production on 星期三 11月 14 09:44:08 2018

Copyright (c) 1982, 2014, Oracle.  All rights reserved.

上次成功登录时间: 星期三 11月 14 2018 09:43:16 +08:00

连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options                                                         

SQL> ALTER USER llwaves QUOTA UNLIMITED ON USERS;

用户已更改。

SQL> ALTER USER llwaves QUOTA UNLIMITED ON USERS02;

用户已更改。

SQL> ALTER USER llwaves ACCOUNT UNLOCK;

用户已更改。

SQL> GRANT "CONNECT" TO llwaves WITH ADMIN OPTION;

授权成功。

SQL> GRANT "RESOURCE" TO llwaves WITH ADMIN OPTION;

授权成功。

SQL> ALTER USER llwaves DEFAULT ROLE "CONNECT","RESOURCE";

用户已更改。

SQL> GRANT CREATE VIEW TO llwaves WITH ADMIN OPTION;

授权成功。

SQL> exit

</pre>
