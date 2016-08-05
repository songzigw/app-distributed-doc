## 多表连接查询（内，外，交叉连接）

连接查询实际上是通过各个表之间共同列的关联性来查询数据的,它是关系数据库查询最主要的特征.
select 表1.字段名1，表2.字段名2，... 
from 表1，表2
where 连接条件
SQL-92标准所定义的FROM子句的连接语法格式为： 
FROM 表名 join_type 表名 [ON (连接条件)]
连接操作中的ON (连接条件) 子句指出连接条件，它由被连接表中的列和比较运算符、逻辑运算符等构成。
连接查询分类：
1.自连接查询，对同一个表进行连接操作（可以理解为两个不同表的内连接，有时可与嵌套查询等价）
2.内连接查询，<又分为：自然连接、等值连接、不等值连接三种>
3.外连接查询，<又分为：左外连接、右外连接、全外连接三种>
4.交叉连接查询，也作无条件查询。
5.联合查询

一．自连接查询

一个表自己与自己建立连接称为自连接或自身连接。
进行自连接就如同两个分开的表一样，可以把一个表的某一行与同一表中的另一行连接起来。
例：
查询选学“101”课程的成绩高于“9505201”号学生成绩的所有学生记录，
并按成绩从高到低排列。
select x.* from sclass x,sclass y
where x.cno=''101'' and x.degree>y.degree and y.sno=''9505201'' and y.cno=''101''
order by x.degree desc

二．内连接查询

内连接(INNER JOIN)使用比较运算符进行表间某(些)列数据的比较操作，并列出这些表中与连接条件相匹配的数据行。根据所使用的比较方式不同，内连接又分为等值连接、自然连接和不等连接三种。

1、等值连接：
所谓等值连接，是指表之间通过“等于”关系连接起来，产生一个临时表，
然后对该临时表进行处理后生成最终结果。其查询结果中列出被连接表中的所有列，
包括其中的重复列。

SELECT * 
FROM authors AS a INNER JOIN publishers AS p 
ON a.city=p.city

我们可以有两种方式，这两种是等效的 
一种是：SELECT e.employeeid,e.employeename,d.deptname FROM EmployeeTB AS e,DeptTB AS d WHERE e.deptid=d.deptid 
另外一个是：SELECT e.employeeid,e.employeename,d.deptname FROM EmployeeTB AS e INNER JOIN DeptTB AS d ON e.deptid=d.deptid 

3、自然连接：
在等值连接中消除重复列就是自然连接。（state,city在两个表中都存在）

SELECT a.*,p.pub_id,p.pub_name,p.country 
FROM authors AS a INNER JOIN publishers AS p 
ON a.city=p.city

三．外连接查询 （左外连接、右外连接、全外连接）

左向外联接的结果集包括 LEFT OUTER 子句中指定的左表的所有行，而不仅仅是联接列所匹配的行。如果左表的某行在右表中没有匹配行，则在相关联的结果集行中右表的所有选择列表列均为空值

右向外联接是左向外联接的反向联接。将返回右表的所有行。如果右表的某行在左表中没有匹配行，则将为左表返回空值。

A left join B 的连接的记录数与A表的记录数同
A right join B 的连接的记录数与B表的记录数同  这种说法是错误的，只有当表A与表B是一对一时才成立。

首先我们做两张表：员工信息表和部门信息表，在此，表的建立只为讲述连接的概念，所以字段非常的简单 
EmployeeTB（员工信息表）： 

employeeid employeename deptid 
0001  张三  01 
0002  李四  01 
0003  王五  02 
0004  赵六  02 
0005  郑七  NULL 

DeptTB（部门信息表） 
deptid  deptname 
01  技术部 
02  市场部 
03  工程部 

1、左外联结 
但是有些情况下，我们需要知道所有员工的信息，即使他不属于任何部门。这样我们就可以采用外连接，在这里为左外连接，也就是连接中的左表的表中的记录，无论能不能在右表中找到匹配的项，都要检索，如果没有匹配的项目，那么右表中的字段值为NULL（空），在这里就代表，此员工不属于任何部门。 
检索语句为： 
SELECT e.employeeid,e.employeename,d.deptname FROM EmployeeTB AS e LEFT OUTER JOIN DeptTB AS d ON e.deptid=d.deptid 
检索的结果都是： 

employeeid employeename deptname 
0001  张三 技术部 
0002  李四 技术部 
0003  王五 市场部 
0004  赵六 市场部 
0005  郑七  NULL 

但是在这里，工程部同样不会被检索，因为，deptname是在连接的右边的表中，“工程部”在左表中不存在任何的记录，所以不会被检索。这里关注的是“连接中的左边的表” 

2、右外连接 
有时，我们需要知道，全部部门的信息，即使它没有任何的员工。在我们的查询中部门表在连接的右边，如果我们想知道右边表中的所有记录信息，那么就可以采用右外连接，如果此记录在左边的表中找不到匹配项，则相应字段（employeeid,employeename)为NULL 
检索语句为： 
SELECT e.employeeid,e.employeename,d.deptname FROM EmployeeTB AS e RIGHT OUTER JOIN DeptTB AS d ON e.deptid=d.deptid 
检索的结果都是： 

employeeid employeename deptname 
0001  张三 技术部 
0002  李四 技术部 
0003  王五 市场部 
0004  赵六 市场部 
NULL  NULL  工程部 

但在这里，郑七是不会被检索了，因为它在右表中找不到匹配项，这里关注的是“连接中的右边的表” 

3、完全外连接 
如果我们想知道所有的记录呢？无论员工有没有部门，部门有没有员工，我们都需要检索。这里就可以使用完全外连接。关注连接中的两部分。如果没有部门，部门为空，没有员工，员工信息为空。 
检索语句为： 
SELECT e.employeeid,e.employeename,d.deptname FROM EmployeeTB AS e FULL OUTER JOIN DeptTB AS d ON e.deptid=d.deptid 
检索的结果都是： 

employeeid employeename deptname 
0001  张三 技术部 
0002  李四 技术部 
0003  王五 市场部 
0004  赵六 市场部 
0005  郑七  NULL 
NULL  NULL  工程部 

四．交叉连接

交叉连接不带WHERE 子句，它返回被连接的两个表所有数据行的笛卡尔积，返回到结果集合中的数
据行数等于第一个表中符合查询条件的数据行数乘以第二个表中符合查询条件的数据行数。
