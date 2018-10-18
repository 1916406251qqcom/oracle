# 实验一：分析SQL执行计划，执行SQL语句的优化指导
## 查询语句1:
<pre>
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
from hr.departments d，hr.employees e
where d.department_id = e.department_id
and d.department_name in ('IT'，'Sales')
GROUP BY department_name;
</pre>
## 执行查询语句1的结果:
![](https://github.com/llwaves/oracle/blob/master/test1/sql1.PNG)
![](https://github.com/llwaves/oracle/blob/master/test1/sql1_1.PNG)
## 查询语句2：
<pre>
SELECT d.department_name，count(e.job_id)as "部门总人数"，
avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT'，'Sales');
</pre>
## 执行查询语句2的结果:
![](https://github.com/llwaves/oracle/blob/master/test1/sql2.PNG)
![](https://github.com/llwaves/oracle/blob/master/test1/sql2_1.PNG)
## 执行结果分析
执行上面的两个查询语句后得知，两个查询语句查询结果相同，但查询语句1查询时间更少，而且consistent on要小于查询语句2，所有查询语句1更优。
## 查询语句1通过sqldeveloper的优化指导工具进行优化指导结果
![](https://github.com/llwaves/oracle/blob/master/test1/sql1_2.PNG)
通过Sqldeveloper优化指导工具进行指导优化后，发现已经是最佳优化了。

