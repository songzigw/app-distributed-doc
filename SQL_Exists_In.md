## exists
exists 强调的是是否返回结果集，不要求知道返回什么, 比如：
```
select name from student where sex = 'm' and mark exists(select 1 from grade where ...)
```
只要exists引导的子句有结果集返回，那么exists这个条件就算成立了,大家注意返回的字段始终为1，如果改成“select 2 from grade where ...”，那么返回的字段就是2，这个数字没有意义，所以exists子句不在乎返回什么，而是在乎是不是有结果集返回。

## exists 与 in 的区别
in引导的子句只能返回一个字段，比如：
```
select name from student where sex = 'm' and mark in (select 1,2,3 from grade where ...)  
```
in子句返回了三个字段，这是不正确的，exists子句是允许的，但in只允许有一个字段返回，在1，2，3中随便去了两个字段即可。


### not exists 和 not in 分别是 exists 和 in 的对立面

exists（sql返回结果集为真） not exists（sql不返回结果集为真）

有如下数据表结构
```
表A
ID     NAME  
1      A1
2      A2
3      A3

表B
ID    AID    NAME
1     1      B1
2     2      B2  
3     2      B3
```

表A和表B是1对多的关系 A.ID => B.AID

1）先看EXISTS
SELECT ID, NAME FROM A WHERE EXISTS (SELECT * FROM B WHERE A.ID = B.AID)
执行结果为
1  A1
2  A2
原因可以按照如下分析
SELECT ID, NAME FROM A WHERE EXISTS (SELECT * FROM B WHERE B.AID=1)
--->SELECT * FROM B WHERE B.AID=１有值返回真所以有数据

SELECT ID, NAME FROM A WHERE EXISTS (SELECT * FROM B WHERE B.AID=2)
--->SELECT * FROM B WHERE B.AID=２有值返回真所以有数据

SELECT ID, NAME FROM A WHERE EXISTS (SELECT * FROM B WHERE B.AID=3)
--->SELECT * FROM B WHERE B.AID=３无值返回真所以没有数据

2）NOT EXISTS和上面的sql正好是反过来的
SELECT ID, NAME FROM A WHERE　NOT EXIST (SELECT * FROM B WHERE A.ID = B.AID)
执行结果为
3  A3

### exists 和 in

EXISTS 和 IN，意思相同不过语法上有点点区别，好像使用IN效率要差点，应该是不会执行索引的原因
SELECT ID, NAME FROM A WHERE ID IN (SELECT AID FROM B)

NOT EXISTS 和 NOT IN，意思相同不过语法上有点点区别
SELECT ID, NAME FROM A WHERE ID NOT IN (SELECT AID FROM B)

## 其它例子

有时候我们会遇到要选出某一列不重复，某一列作为选择条件，其他列正常输出的情况

如下面的表table
```
Id   Name    Class   Count  Date
 1   苹果    水果    10     2011-7-1
 1   桔子    水果    20     2011-7-2
 1   香蕉    水果    15     2011-7-3
 2   白菜    蔬菜    12     2011-7-1
 2   青菜    蔬菜    19     2011-7-2
 ```

如果想要得到下面的结果：（Id唯一，Date选最近的一次）
```
1    香蕉    水果    15     2011-7-3
2    青菜    蔬菜    19     2011-7-2
```
正确的SQL语句是:
```
SELECT Id, Name, Class, Count, Date
FROM table t
WHERE (NOT EXISTS
          (SELECT Id, Name, Class, Count, Date FROM table 
         WHERE Id = t.Id AND Date > t.Date))
```
如果用distinct，得不到这个结果, 因为distinct是作用与所有列的
```
SELECT DISTINCT Id, Name, Class, Count, Date FROM table
```
结果是表table的所有不同列都显示出来，如下所示:
```
 1   苹果    水果    10     2011-7-1
 1   桔子    水果    20     2011-7-2
 1   香蕉    水果    15     2011-7-3
 2   白菜    蔬菜    12     2011-7-1
 2   青菜    蔬菜    19     2011-7-2
```
如果用Group by也得不到需要的结果,因为Group by 要和聚合函数共同使用,所以对于Name,Class和Count列要么使用Group by,要么使用聚合函数. 如果写成
```
SELECT Id, Name, Class, Count, MAX(Date) FROM table GROUP BY Id, Name, Class, Count
```
得到的结果是
```
 1   苹果    水果    10    2011-7-1
 1   桔子    水果    20     2011-7-2
 1   香蕉    水果    15     2011-7-3
 2   白菜    蔬菜    12     2011-7-1
 2   青菜    蔬菜    19     2011-7-2
```
如果写成
```
SELECT Id, MAX(Name), MAX(Class), MAX(Count), MAX(Date) FROM table GROUP BY Id
```
得到的结果是:
```
 1   香蕉    水果    20     2011-7-3
 2   青菜    蔬菜    19     2011-7-2
```
如果用in有时候也得不到结果,（有的时候可以得到，如果Date都不相同（没有重复数据），或者是下面得到的Max（Date）只有一个值）
```
SELECT DISTINCT Id, Name, Class, Count, Date FROM table
WHERE (Date IN
          (SELECT MAX(Date)
         FROM table
         GROUP BY Id))
```
得到的结果是：（因为MAX(Date)有两个值2011-7-2，2011-7-3）
```
 1   桔子    水果    20     2011-7-2
 1   香蕉    水果    15     2011-7-3
 2   青菜    蔬菜    19     2011-7-2
```
注意in只允许有一个字段返回

有一种方法可以实现：
```
SELECT Id, Name, Class, COUNT, Date
FROM table1 t
WHERE (Date =
          (SELECT MAX(Date)
         FROM table1
         WHERE Id = t .Id))
```
