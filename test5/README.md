# 实验5：PL/SQL编程

## Oracle用户名:llwaves

## 实验步骤
1. 创建一个包(Package)，包名是MyPack。
<pre>
CREATE or REPLACE PACKAGE MyPack IS
  /*
  本实验以实验4为基础。
  包MyPack中有：
  一个函数:Get_SaleAmount_Department(V_DEPARTMENT_ID NUMBER)
  一个函数:Get_SaleAmount_Employee(V_DEPARTMENT_ID NUMBER)
  一个过程:Get_Employees(V_EMPLOYEE_ID NUMBER)
  */
  FUNCTION Get_SaleAmount_Department(V_DEPARTMENT_ID NUMBER) RETURN NUMBER;
  FUNCTION Get_SaleAmount_Employee(V_EMPLOYEE_ID NUMBER) RETURN NUMBER;
  PROCEDURE Get_Employees(V_EMPLOYEE_ID NUMBER);
END MyPack;
</pre>

2. 在MyPack中创建一个函数Get_SaleAmount_Department ，查询部门表，统计每个部门的销售总金额，每个部门的销售额是由该部门的员工(ORDERS.EMPLOYEE_ID)完成的销售额之和。函数SaleAmount要求输入的参数是部门号，输出部门的销售金额。
* 创建函数Get_SaleAmount_Department输出部门的销售金额。
<pre>
CREATE or REPLACE PACKAGE BODY MyPack IS

...

  FUNCTION Get_SaleAmount_Department(V_DEPARTMENT_ID NUMBER) RETURN NUMBER --创建函数Get_SaleAmount_Department,，获取部门的销售金额
  AS
    N NUMBER(20,2); --注意，订单ORDERS.TRADE_RECEIVABLE的类型是NUMBER(8,2),汇总之后，数据要大得多。
    BEGIN
      SELECT SUM(O.TRADE_RECEIVABLE) into N  FROM ORDERS O,EMPLOYEES E
      WHERE O.EMPLOYEE_ID=E.EMPLOYEE_ID AND E.DEPARTMENT_ID =V_DEPARTMENT_ID;
      RETURN N;
    END;
END MyPack;

...

END MyPack;
</pre>
* 测试语句
<pre>
SELECT count(*) FROM orders;
SELECT MyPack.Get_SaleAmount(11) AS 部门11应收金额,MyPack.Get_SaleAmount(12) AS 部门12应收金额 FROM dual;
</pre>
* 脚本输出
<pre>
  COUNT(*)
----------
     10000


  部门11应收金额   部门12应收金额
---------- ----------
70733729.5 69991326.5
</pre>
3.在MyPack中创建一个过程，在过程中使用游标，递归查询某个员工及其所有下属，子下属员工。过程的输入参数是员工号，输出员工的ID,姓名，销售总金额。信息用dbms_output包中的put或者put_line函数。输出的员工信息用左添加空格的多少表示员工的层次（LEVEL）。
* 首先需创建一个函数Get_SaleAmount_Employee获取员工的销售总金额。
<pre>
CREATE or REPLACE PACKAGE BODY MyPack IS

...

 FUNCTION Get_SaleAmount_Employee(V_EMPLOYEE_ID NUMBER) RETURN NUMBER --创建函数Get_SaleAmount_Employee，获取员工的销售金额
  AS
    N NUMBER(20,2); --注意，订单ORDERS.TRADE_RECEIVABLE的类型是NUMBER(8,2),汇总之后，数据要大得多。
    BEGIN
      SELECT SUM(O.TRADE_RECEIVABLE) into N  FROM ORDERS O
      WHERE O.EMPLOYEE_ID=V_EMPLOYEE_ID;
      RETURN N;
    END;
    
...

END MyPack;
<pre>
* 创建过程GET_EMPLOYEES，输出员工的ID,姓名，销售总金额。
<pre>
CREATE or REPLACE PACKAGE BODY MyPack IS

...

PROCEDURE GET_EMPLOYEES(V_EMPLOYEE_ID NUMBER) --创建过程GET_EMPLOYEES
  AS
    LEFTSPACE VARCHAR(2000);
    begin
      --通过LEVEL判断递归的级别
      LEFTSPACE:=' ';
      --使用游标
      for v in
      (SELECT LEVEL,EMPLOYEE_ID,NAME,MANAGER_ID,Get_SaleAmount_Employee(EMPLOYEE_ID) as s FROM employees
      START WITH EMPLOYEE_ID = V_EMPLOYEE_ID
      CONNECT BY PRIOR EMPLOYEE_ID = MANAGER_ID)
      LOOP
        DBMS_OUTPUT.PUT_LINE(LPAD(LEFTSPACE,(V.LEVEL-1)*4,' ')||
                             V.EMPLOYEE_ID||' '||v.NAME||' '||v.s);
      END LOOP;
    END;
        
...

END MyPack;
</pre>
<pre>
set serveroutput on
DECLARE
  V_EMPLOYEE_ID NUMBER;    
BEGIN
  V_EMPLOYEE_ID := 1;
  MYPACK.Get_Employees (  V_EMPLOYEE_ID => V_EMPLOYEE_ID) ;  
  V_EMPLOYEE_ID := 11;
  MYPACK.Get_Employees (  V_EMPLOYEE_ID => V_EMPLOYEE_ID) ;    
END;
</pre>
* 输出
<pre>
1 李董事长 
    11 张总 35034887.23
        111 吴经理 35269982.17
        112 白经理 35463747.34
    12 王总 34998099.78
        121 赵经理 35053968.88
        122 刘经理 34937357.6
11 张总 35034887.23
    111 吴经理 35269982.17
    112 白经理 35463747.34

PL/SQL 过程已成功完成。
</pre>


