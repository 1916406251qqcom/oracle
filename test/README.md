# 实验二：用户管理 - 掌握管理角色、权根、用户的能力，并在用户之间共享对象
## 第1步：以system登录到pdborcl，创建角色con_res_view和用户new_user，并授权和分配空间：
<pre>
SQL*Plus: Release 12.1.0.2.0 Production on 星期三 10月 24 08:51:07 2018
Copyright (c) 1982, 2014, Oracle.  All rights reserved.
上次成功登录时间: 星期三 10月 24 2018 08:50:49 +08:00
连接到:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing opt                                                                     ions
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
