相关子查询与非相关子查询

1：标量子查询(相对于多值子查询)：

只有标量子查询返回的是单个记录或者不返回，就是有效的子查询。

Ex1:select OrderId From Orders where EmployeeId=

(select EmployeeId From employees where lastName like N'Davolio')

将’Davolio’改为'D%'时,这个时候子查询中返回结果为2行,等号右边此时为多值,查询失败.将’=’改为in谓词.查询才能通过.

employees表中无lastname=’jason’,外部查询将返回null.


2：非相关子查询（嵌套子查询）

一个select...From...Where查询语句块可以嵌套在另一个select...From...Where查询块的Where子句
中，称为嵌套查询。外层查询称为父查询，主查询。内层查询称为子查询，从查询。
子查询可以嵌套多层，子查询查询到的结果又成为父查询的条件。子查询中不能有order by分组语句。
先处理子查询，再处理父查询。 
细分如下：

1。 简单嵌套查询 

     查询选修课程号为'101'并且成绩高于学生号为'9501101'的所有学生的成绩.
select * from sclass 
where cno='101' and degree>=
(select degree from sclass where sno='9501101'and cno='101')  

当子查询跟随在 =、!=、<、<=、>、>= 之后， 子查询的返回值只能是一个， 否则应在外层where子句中用
一个in限定符,即要返回多个值，要用in或者not in

2。 带[not] in的嵌套查询 

     只要主查询中列或运算式是在（不在）子查询所得结果列表中的话，则主查询的结果为我们要的数据
    select sales_id,tot_amt
    from sales 
    where sale _id  in(select sale_id from employee where sex='F')
where sno not in(Select distinct sno from sclass)

3。 带exists的嵌套查询
 子查询的结果至少存在一条数据时，则主查询的结果为我们要的数据。（exists）或自查询的结果找不到数据时，则主查询的结果为我们要的数据(not exists)
 我们经常查询的两个表有多少重复的记录就用这个
 以下范例让你找出滞销的产品，也就是尚未有任何销售记录的库存产品。此范例主要是查询以库文件中的每一条产品代码到销售明细表中去查询，如果查询不到任何一条，表示该产品未曾卖出任何一件。
  select * from stock a
  where not exists(select * from sale_item b
                          where a.prod_id=b.prod_id and a.stup_id=b.stup_id)

4。select ... where 列或运算式 比较运算运算【any|all](子查询）


   只要主查询中列或运算式与子查询所得结果中任一（any）或全部（all）数据符合比较条件的话则主查询的结果为我们要的数据

  select sale_id,tot_amt
    from sales 
    where tot_amt>any(select tot_amt from sales where sale_id='e0013'and 'order_date='1996/11/10')

       选出不同的人金额最高的订单
    select *  from  sales a
    where tomat=(select max(totmat) from sales  where name=a.name)


3：相关子查询（多值子查询）

1>非相关子查询是独立于外部查询的子查询，子查询总共执行一次，执行完毕后将值传递给外部查询。 
2>相关子查询的执行依赖于外部查询的数据，外部查询执行一行，子查询就执行一次。

  查询中再查询,通常是以一个查询作为条件来供另一个查询使用
      例:有work表和部门表
         A:检索出在部门表中登记的所有部门的职工基本资料
           select * from work where 部门编号 in [not in](select 部门编号 from dbo.部门) 
         B:检索出在work表中每一个部门的最高基本工资的职工资料
           select * from work a where 基本工资=(select max(基本工资) from work b where a.部门名称=b.部门名称) 
           说明:由外查询提供一个部门名称给内查询,内查询利用这个部门名称找到该部门的最高基本工资,然后外查询根据基本工资判断是否等于最高工资,如果是的,则显示出来.
           相当于:select * from work,(select 部门名称,max(基本工资) as 基本工资 from work group by 部门名称 as t) where work.基本工资=t.基本工资 and work.部门名称=t.部门名称
         C:用嵌套work表和嵌套部门表,在嵌套work表中检索出姓名和职工号都在嵌套部门存在的职工资料 
           select * from 嵌套work where 职工号 in (select 职工号 from 嵌套部门) and 姓名 in (select 姓名 from 嵌套部门) [察看结果,分析原因] （错误，因为这两个in不是一对一
           改:select * from 嵌套work a,嵌套部门 b where a.职工号=b.职工号 and a.姓名=b.姓名 
           改:select * from 嵌套work where 职工号=(select 职工号 from 嵌套部门) and 姓名=(select 姓名 from 嵌套部门) [行吗?为什么,分析原因?] 不能后面的select得到的结果不是一个值而又跟在=后必然出错

在嵌套中使用exists关键字[存在]
例:1:用嵌套work表和嵌套部门表,在嵌套work表中检索出姓名和职工号都在嵌套部门存在的职工资料 
     select * from 嵌套work a where exists (select * from 嵌套部门 b where a.姓名=b.姓名 and a.职工号=b.职工号)
   2:在work表检索出在部门表没有的职工
     select * from work where not exists (select * from 部门 where 部门.部门编号=work.部门编号)
     能否改成:select * from work where exists (select * from 部门 where 部门.部门编号<>work.部门编号) 是不能的，
在列清单中使用select
例:1:在work1表和部门表中检索出所有部门的部门名称和基本工资总和
    select 部门名称,(select sum(基本工资) from work1 b where a.部门编号=b.部门编号) from 部门 a
   2:检索各部门的职工人数
    select 部门编号,部门名称,(select count(职工号) from work1 a where a.部门编号=b.部门编号) as 人数 from 部门 b
   3:在商品表和销售表中查询每一职工的姓名,所属部门,销售总量                   
    select 姓名,所属部门,(select sum(销售量) from 商品销售 a where a.职工号=b.职工号) as 销售总量 from 嵌套部门 b

说明：都是相关子查询的特殊情况，外层的查询是内层查询的条件如：a.职工号=b.职工号，内层条件成立则得到的些行记录是并入外层查询的最终结果，否则不记录入最后结果

许多查询都可以通过执行一次子查询并将得到的值代入外部查询的 WHERE 子句中进行计算。在包括相关子查询（也称为重复子查询）的查询中，子查询依靠外部查询获得值。这意味着子查询是重复执行的，为外部查询可能选择的每一行均执行一次。

此查询在 SalesPerson 表中检索奖金为 5000 且雇员标识号与 Employee 和 SalesPerson 表中的标识号相匹配的雇员的名和姓的一个实例。

USE AdventureWorks2008R2;
GO
SELECT DISTINCT c.LastName, c.FirstName, e.BusinessEntityID 
FROM Person.Person AS c JOIN HumanResources.Employee AS e
ON e.BusinessEntityID = c.BusinessEntityID 
WHERE 5000.00 IN
    (SELECT Bonus
    FROM Sales.SalesPerson sp
    WHERE e.BusinessEntityID = sp.BusinessEntityID) ;
GO
下面是结果集：

LastName FirstName BusinessEntityID

-------------------------- ---------- ------------

Ansman-Wolfe Pamela 280

Saraiva José 282

(2 row(s) affected)

该语句中前面的子查询无法独立于外部查询进行计算。它需要 Employee.BusinessEntityID 值，但是此值随 SQL Server 检查 Employee 中的不同行而改变。

下面准确说明了如何计算此查询：SQL Server 通过将每一行的值代入内部查询，考虑 Employee 表中的每一行是否都包括在结果中。例如，如果 SQL Server 首先检查 Syed Abbas 行，那么变量 Employee.BusinessEntityID 将取值 285，SQL Server 将该值代入内部查询。

USE AdventureWorks2008R2;
GO
SELECT Bonus
FROM Sales.SalesPerson
WHERE BusinessEntityID = 285;
结果为 0（Syed Abbas 没有收到奖金，因为他不是销售人员），因此外部查询计算为：

 
USE AdventureWorks2008R2;
GO
SELECT LastName, FirstName
FROM Person.Person AS c JOIN HumanResources.Employee AS e
ON e.BusinessEntityID = c.BusinessEntityID 
WHERE 5000 IN (0.00)
引用外部查询列的子查询。逻辑上讲，子查询会为外部查询的每行计算一次。

Ex4:查询每个员工employee最近的一个订单

思路:--需要附加属性(Tiebreaker)Max(OrderDate)最大订单日期和Max(OrderId)最大订单号来确定每名员工的最近订单状况

select OrderId,CustomerId,EmployeeId,OrderDate From Orders As o1 where OrderDate=

(

    select Max(OrderDate) From Orders As o2 where o2.EmployeeId=o1.EmployeeId--得到每名员工的最近订单日期

)

and OrderId=

(

    select Max(OrderId) From Orders As o2 Where o2.EmployeeId=o1.EmployeeId and o2.OrderDate=o1.OrderDate--得到每名员工每个订单日期的最大OrderId

)
