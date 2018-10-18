# 实验一：分析SQL执行计划，执行SQL语句的优化指导
## 查询语句1
<pre>
SELECT d.department_name，count(e.job_id)as "部门总人数",avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
and d.department_name in ('IT'，'Sales')
GROUP BY department_name;
</pre>
## 执行查询语句1的结果
![](https://github.com/llwaves/oracle/blob/master/test1/sql1.PNG)
![](https://github.com/llwaves/oracle/blob/master/test1/sql1_3.PNG)
![](https://github.com/llwaves/oracle/blob/master/test1/sql1_1.PNG)</br>
查询语句1的consistent gets=5，cost成本=2。
## 查询语句2
<pre>
SELECT d.department_name，count(e.job_id)as "部门总人数",avg(e.salary)as "平均工资"
FROM hr.departments d，hr.employees e
WHERE d.department_id = e.department_id
GROUP BY department_name
HAVING d.department_name in ('IT'，'Sales');</pre>
## 执行查询语句2的结果
![](https://github.com/llwaves/oracle/blob/master/test1/sql2.PNG)
![](https://github.com/llwaves/oracle/blob/master/test1/sql2_2.PNG)
![](https://github.com/llwaves/oracle/blob/master/test1/sql2_1.PNG)
</br>
查询语句2的consistent gets=8，cost成本=5。
## 执行结果分析
执行上面的两个查询语句后得知，两个查询语句查询结果相同，但查询语句1查询时间更少，而且consistent gets及cost成本都要小于查询语句2，所有查询语句1更优。
## 查询语句1通过sqldeveloper的优化指导工具进行优化指导结果
![](https://github.com/llwaves/oracle/blob/master/test1/sql1_2.PNG)
通过Sqldeveloper优化指导工具进行指导优化后，发现已经是最佳优化。
## 自己的查询语句
<pre>
SELECT d.department_name,e.salary,l.city, count(*) 
FROM hr.employees e,hr.departments d ,hr.locations l
WHERE e.department_id=d.department_id and d.location_id=l.location_id and e.salary>3000
GROUP BY department_name, d.department_name, e.salary, l.city
HAVING count(*)>2;
</pre>
此查询语句是查询部门中工资高于3000的员工数量超过2人的部门，显示部门名字、地区名称，工资。count计数工资高于3000的员工数量，最后由HAVING判断员工数量大于2的部门。查询结果为：</br></br>
![](https://github.com/llwaves/oracle/blob/master/test1/sql3.PNG)

