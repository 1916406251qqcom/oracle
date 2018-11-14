# 实验4：对象管理

## 此次实验删除了实验3所创建的表。Oracle用户名：llwaves。

## 实验步骤
* 登录system账号，为自己的账号llwaves分配相关权限。
<pre>
[oracle@deep02 ~]$ sqlplus system/123@pdborcl                                                       
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
* 登录自己的账号：llwaves去创建相关的表。
1. 创建部门表DEPARTMENTS,表空间：USERS。
