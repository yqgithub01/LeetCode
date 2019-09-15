# LeetCode-database题解

## 182. 查找重复的电子邮箱

## 题目描述

编写一个 SQL 查询，查找Person表中所有重复的电子邮箱。

示例：


| Id | Email   |
|---|---|
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |

根据以上输入，你的查询应返回以下结果：

| Email   |
|---|
| a@b.com |

说明：所有电子邮箱都是小写字母。

## 实现

```sql
select Email
from Person
group by Email
having count(*) > 1;
```

## 595. 大的国家

## 题目描述

这里有张World 表

    +-----------------+------------+------------+--------------+---------------+
    | name            | continent  | area       | population   | gdp           |
    +-----------------+------------+------------+--------------+---------------+
    | Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
    | Albania         | Europe     | 28748      | 2831741      | 12960000      |
    | Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
    | Andorra         | Europe     | 468        | 78115        | 3712000       |
    | Angola          | Africa     | 1246700    | 20609294     | 100990000     |
    +-----------------+------------+------------+--------------+---------------+
如果一个国家的面积超过300万平方公里，或者人口超过2500万，那么这个国家就是大国家。

编写一个SQL查询，输出表中所有大国家的名称、人口和面积。

例如，根据上表，我们应该输出:

    +--------------+-------------+--------------+
    | name         | population  | area         |
    +--------------+-------------+--------------+
    | Afghanistan  | 25500100    | 652230       |
    | Algeria      | 37100000    | 2381741      |
    +--------------+-------------+--------------+


## 实现

```sql
select name, population, area
from World
where area > 3000000 or population > 25000000;
```

## 620. 有趣的电影

## 题目描述

某城市开了一家新的电影院，吸引了很多人过来看电影。该电影院特别注意用户体验，专门有个 LED显示板做电影推荐，上面公布着影评和相关电影描述。

作为该电影院的信息部主管，您需要编写一个 SQL查询，找出所有影片描述为**非**boring(不无聊)的并且 **id 为奇数**的影片，结果请按等级 rating 排列。


例如，下表 cinema:

    +---------+-----------+--------------+-----------+
    |   id    | movie     |  description |  rating   |
    +---------+-----------+--------------+-----------+
    |   1     | War       |   great 3D   |   8.9     |
    |   2     | Science   |   fiction    |   8.5     |
    |   3     | irish     |   boring     |   6.2     |
    |   4     | Ice song  |   Fantacy    |   8.6     |
    |   5     | House card|   Interesting|   9.1     |
    +---------+-----------+--------------+-----------+
对于上面的例子，则正确的输出是为：

    +---------+-----------+--------------+-----------+
    |   id    | movie     |  description |  rating   |
    +---------+-----------+--------------+-----------+
    |   5     | House card|   Interesting|   9.1     |
    |   1     | War       |   great 3D   |   8.9     |
    +---------+-----------+--------------+-----------+


## 实现

```sql
select *
from cinema
where (id % 2) = 1 and description != 'boring'
order by rating desc;
```

# 627. 交换工资

给定一个 salary 表，如下所示，有 m = 男性 和 f = 女性 的值。交换所有的 f 和 m 值（例如，将所有 f 值更改为 m，反之亦然）。要求只使用一个更新（Update）语句，并且没有中间的临时表。

注意，您必只能写一个 Update 语句，请不要编写任何 Select 语句。

例如：

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | m   | 2500   |
| 2  | B    | f   | 1500   |
| 3  | C    | m   | 5500   |
| 4  | D    | f   | 500    |
运行你所编写的更新语句之后，将会得到以下表:

| id | name | sex | salary |
|----|------|-----|--------|
| 1  | A    | f   | 2500   |
| 2  | B    | m   | 1500   |
| 3  | C    | f   | 5500   |
| 4  | D    | m   | 500    |



```sql
# 方法1
# if函数：if(expr, a, b)：如果表达式expr为真，返回a，否则返回b
update salary
set sex = if(sex = 'm', 'f', 'm');

# 方法2
# case
update salary
set sex = (case 
           when sex = 'm' then 'f'
           else 'm'
           end);

# update salary
# set sex = (case sex
#            when 'm' then 'f'
#            else 'm'
#            end);
```

如果不太明白，点击链接：
[菜鸟教程](https://www.runoob.com/mysql/mysql-functions.html)

# 175. 组合两个表

## 题目

表1: Person


| 列名         | 类型     |
|----|------|-----|--------|
| PersonId    | int     |
| FirstName   | varchar |
| LastName    | varchar |

PersonId 是上表主键

表2: Address

| 列名         | 类型    |
|----|------|-----|--------|
| AddressId   | int     |
| PersonId    | int     |
| City        | varchar |
| State       | varchar |

AddressId 是上表主键
 

编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：
FirstName, LastName, City, State

## 思路

这里考察左外连接left join(left outer join)中on和where的不同。
左连接：
		select 字段列表
		from 表1 left [outer] join 表2
		on 条件;
* on：如果条件不匹配，也会返回左表的信息，右表为空NULL。
* where：如果条件不匹配，左表右表的信息都不返回。

## 代码

```sql
select FirstName, LastName, City, State
from Person left join Address
on Person.PersonId = Address.PersonId;
```

[如果还是不明白，详细的左外连接，点击这里](https://www.runoob.com/sql/sql-join-left.html)

# 181. 超过经理收入的员工

## 题目

Employee 表包含所有员工，他们的经理也属于员工。每个员工都有一个 Id，此外还有一列对应员工的经理的 Id。


| Id | Name  | Salary | ManagerId |
|----|-----|-----|------|
| 1  | Joe   | 70000  | 3         |
| 2  | Henry | 80000  | 4         |
| 3  | Sam   | 60000  | NULL      |
| 4  | Max   | 90000  | NULL      |

给定 Employee 表，编写一个 SQL 查询，该查询可以获取收入超过他们经理的员工的姓名。在上面的表格中，Joe 是唯一一个收入超过他的经理的员工。

| Employee |
|----|
| Joe      |


## 思路

使用自身查询，使用where，不是on，效果是让生成的表左边是非经理的员工，表的右边是经理，不会让表的左边有经理的信息。where条件是非经理员工的经理id等于经理的id，同时非经理员工的工资多于他的经理的工资。

## 代码

```sql
select e1.Name Employee
from Employee e1, Employee e2
where e1.ManagerId = e2.Id and e1.Salary > e2.Salary;
```

# 626. 换座位

## 题目描述

小美是一所中学的信息科技老师，她有一张 seat 座位表，平时用来储存学生名字和与他们相对应的座位 id。
其中纵列的 id 是连续递增的
小美想改变相邻俩学生的座位。
你能不能帮她写一个 SQL query 来输出小美想要的结果呢？

示例：

|    id   | student |
|---|----|
|    1    | Abbot   |
|    2    | Doris   |
|    3    | Emerson |
|    4    | Green   |
|    5    | Jeames  |

假如数据输入的是上表，则输出结果如下：
|    id   | student |
|---|----|
|    1    | Doris   |
|    2    | Abbot   |
|    3    | Green   |
|    4    | Emerson |
|    5    | Jeames  |

注意：

如果学生人数是奇数，则不需要改变最后一个同学的座位。

## 代码

### 方法1(case方式)
```sql
select (case
        when (id = (select count(*) from seat) and MOD(id, 2) = 1) then id
        when MOD(id, 2) = 1 then id + 1
        else id - 1
        end
       ) id, student
from seat
order by id;
```

MOD(x, y)：求x除以y的余数

### 方法2(if方式)

```sql
select (if(id % 2 = 1, 
           if(id = (select count(*) from seat), id, id + 1), 
           id - 1)) id, student
from seat
order by id;
```

# 183. 从不订购的客户

## 题目描述

某网站包含两个表，Customers 表和 Orders 表。编写一个 SQL 查询，找出所有从不订购任何东西的客户。

Customers 表：

| Id | Name  |
|----|----|
| 1  | Joe   |
| 2  | Henry |
| 3  | Sam   |
| 4  | Max   |

Orders 表：
| Id | CustomerId |
|----|----|
| 1  | 3          |
| 2  | 1          |

例如给定上述表格，你的查询应返回：

| Customers |
|----|
| Henry     |
| Max       |

## 实现

### 实现1
在Customers表中查找顾客，它的id不在Orders表的CustomerId中

```sql
select Name Customers
from Customers
where id not in(select CustomerId from Orders);
```

### 实现2
左连接，先左连接Customers和Orders表，on条件是顾客的id = orders表的CustomerId，生成一个顾客的id = orders表的CustomerId的临时表；where条件是临时表中取出Orders的id是NULL的客户。

```sql
select c.Name Customers
from Customers c left join Orders o
on c.Id = o.CustomerId
where o.Id IS NULL;
```

[SQL JOIN 中 on 与 where 的区别](https://www.runoob.com/w3cnote/sql-join-the-different-of-on-and-where.html)

# 178. 分数排名

## 题目

编写一个 SQL 查询来实现分数排名。如果两个分数相同，则两个分数排名（Rank）相同。请注意，平分后的下一个名次应该是下一个连续的整数值。换句话说，名次之间不应该有“间隔”。

| Id | Score |
|----|----|
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |

例如，根据上述给定的 Scores 表，你的查询应该返回（按分数从高到低排列）：

| Score | Rank |
|----|----|
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |


## 实现

这里最主要的问题是怎样实现Rank列，这里使用一个select语句生成排名列Rank，count函数中的distinct是为了防止出现上面说的“间隔”。
如果需要产生间隔，去除这个distinct就可以了。

```sql
select s1.Score Score, 
        (select count(distinct s2.Score) from Scores s2 where s2.Score >= s1.Score) Rank
from Scores s1
order by Rank;
```

# 196. 删除重复的电子邮箱

## 题目描述

编写一个 SQL 查询，来删除 Person 表中所有重复的电子邮箱，重复的邮箱里只保留 Id 最小 的那个。

| Id | Email            |
|----|----|
| 1  | john@example.com |
| 2  | bob@example.com  |
| 3  | john@example.com |
Id 是这个表的主键。

例如，在运行你的查询语句之后，上面的 Person 表应返回以下几行:
| Id | Email            |
|----|----|
| 1  | john@example.com |
| 2  | bob@example.com  |

## 代码实现

```sql
delete from Person
where Id not in (select P.Id
                 from (select min(Id) Id from Person group by Email) P
                )
```

### 代码分析

>(select P.Id
from (select min(Id) Id from Person group by Email) P
)
作用是先根据Email分组，找出每个Email中最小的Id。

>找出来的这些Id是不删除的，其他要删除，所以使用not in

# 180. 连续出现的数字

## 题目描述

编写一个 SQL 查询，查找所有至少连续出现三次的数字。
| Id | Num |
|----|----|
| 1  |  1  |
| 2  |  1  |
| 3  |  1  |
| 4  |  2  |
| 5  |  1  |
| 6  |  2  |
| 7  |  2  |

例如，给定上面的 Logs 表， 1 是唯一连续出现至少三次的数字。
| ConsecutiveNums |
|----|----|
| 1               |

## 实现

### 方法1（三表连接查询）

>思路：Logs表中是否有3行，Id的关系是id2=id1+1，id3=id2+1；并且Num相同。

```sql
select distinct a.Num ConsecutiveNums
from Logs a, Logs b, Logs c
where a.Num = b.Num and b.Num = c.Num and b.Id = (a.Id + 1) and c.Id = (b.Id + 1);
```

### 方法2（in）

>思路：对于Logs表的每一行，是否存在下面的2行(id+1和id+2)，Num相同

```sql
select distinct Num ConsecutiveNums
from Logs
where (id + 1, Num) in (select * from Logs)
        and (id + 2, Num) in (select * from Logs);
```

# 197. 上升的温度

## 题目描述

给定一个 Weather 表，编写一个 SQL 查询，来查找与之前（昨天的）日期相比温度更高的所有日期的 Id。
| Id(INT) | RecordDate(DATE) | Temperature(INT) |
|----|----|----|
|       1 |       2015-01-01 |               10 |
|       2 |       2015-01-02 |               25 |
|       3 |       2015-01-03 |               20 |
|       4 |       2015-01-04 |               30 |

例如，根据上述给定的 Weather 表格，返回如下 Id:
| Id |
|----|
|  2 |
|  4 |

## 实现

>计算天数的差可以使用DATEDIFF函数：DATEDIFF(d1, d2): 日期d1 - 日期d2 的天数

```sql
select w1.Id Id
from Weather w1, Weather w2
where DATEDIFF(w1.RecordDate, w2.RecordDate) = 1
        and w1.Temperature > w2.Temperature;
```

# 262. 行程和用户

## 题目描述

Trips 表中存所有出租车的行程信息。每段行程有唯一键 Id，Client_Id 和 Driver_Id 是 Users 表中 Users_Id 的外键。Status 是枚举类型，枚举成员为 (‘completed’, ‘cancelled_by_driver’, ‘cancelled_by_client’)。

| Id | Client_Id | Driver_Id | City_Id |        Status      |Request_at|
|----|----|----|----|----|----|
| 1  |     1     |    10     |    1    |     completed      |2013-10-01|
| 2  |     2     |    11     |    1    | cancelled_by_driver|2013-10-01|
| 3  |     3     |    12     |    6    |     completed      |2013-10-01|
| 4  |     4     |    13     |    6    | cancelled_by_client|2013-10-01|
| 5  |     1     |    10     |    1    |     completed      |2013-10-02|
| 6  |     2     |    11     |    6    |     completed      |2013-10-02|
| 7  |     3     |    12     |    6    |     completed      |2013-10-02|
| 8  |     2     |    12     |    12   |     completed      |2013-10-03|
| 9  |     3     |    10     |    12   |     completed      |2013-10-03| 
| 10 |     4     |    13     |    12   | cancelled_by_driver|2013-10-03|

Users 表存所有用户。每个用户有唯一键 Users_Id。Banned 表示这个用户是否被禁止，Role 则是一个表示（‘client’, ‘driver’, ‘partner’）的枚举类型。

| Users_Id | Banned |  Role  |
|----|----|----|
|    1     |   No   | client |
|    2     |   Yes  | client |
|    3     |   No   | client |
|    4     |   No   | client |
|    10    |   No   | driver |
|    11    |   No   | driver |
|    12    |   No   | driver |
|    13    |   No   | driver |

写一段 SQL 语句查出 2013年10月1日 至 2013年10月3日 期间非禁止用户的取消率。基于上表，你的 SQL 语句应返回如下结果，取消率（Cancellation Rate）保留两位小数。

|     Day    | Cancellation Rate |
|----|----|
| 2013-10-01 |       0.33        |
| 2013-10-02 |       0.00        |
| 2013-10-03 |       0.50        |

## 实现

>round函数：round(a, b)：对数字a四舍五入，保留小数点后b位。

```sql
select Request_at Day,
        round(count(if(Status != 'completed', Status, NULL)) / count(*), 2) 'Cancellation Rate'
from Trips
where Request_at between '2013-10-01' and '2013-10-03'
        and Client_Id in (select Users_Id from Users where Banned = 'No') 
        and Driver_Id in (select Users_Id from Users where Banned = 'No')
group by Request_at;
```

>注意：在设置别名的时候，如果别名不止一个单词(别名有空格)，例如上面的Cancellation Rate，这时候要用单引号 '' 括住别名，如 'Cancellation Rate' 。

# 601. 体育馆的人流量

## 题目描述

X 市建了一个新的体育馆，每日人流量信息被记录在这三列信息中：序号 (id)、日期 (visit_date)、 人流量 (people)。

请编写一个查询语句，找出人流量的高峰期。高峰期时，至少连续三行记录中的人流量不少于100。

例如，表 stadium：
| id   | visit_date | people    |
|----|----|----|
| 1    | 2017-01-01 | 10        |
| 2    | 2017-01-02 | 109       |
| 3    | 2017-01-03 | 150       |
| 4    | 2017-01-04 | 99        |
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-08 | 188       |

对于上面的示例数据，输出为：
| id   | visit_date | people    |
|----|----|----|
| 5    | 2017-01-05 | 145       |
| 6    | 2017-01-06 | 1455      |
| 7    | 2017-01-07 | 199       |
| 8    | 2017-01-08 | 188       |

提示：
每天只有一行记录，日期随着 id 的增加而增加。

## 实现

### 实现1
>左连接，五表连接，右边的四个表分别是第一个表的前第二个、前第一个、后第一个和后第二个。
```sql
select a.*
from stadium a left join stadium b on a.id = b.id - 2
        left join stadium c on a.id = c.id - 1
        left join stadium d on a.id = d.id + 1
        left join stadium e on a.id = e.id + 2
where (a.people >= 100 and b.people >= 100 and c.people >= 100) or
        (a.people >= 100 and c.people >= 100 and d.people >= 100) or
        (a.people >= 100 and d.people >= 100 and e.people >= 100)
order by a.id;
```


### 实现2

>三表连接，把符合条件的三行连接在一起，但是有可能在同一天有不止一种情况能判断这一天是高峰期，如id=6这一天，所以要用distinct去除重复。
```sql
select distinct a.*
from stadium a, stadium b, stadium c
where a.people >= 100 and b.people >= 100 and c.people >= 100 and
        ((a.id = b.id - 1 and a.id = c.id - 2) or
        (a.id = b.id + 1 and a.id = c.id - 1) or
        (a.id = b.id + 1 and a.id = c.id + 2))
order by a.id;
```

### 实现3
>使用case方式。
>case
>when 第一天：
>when 第二天：
>when 倒数第二天：
>when 最后一天：
>其他情况：
>end
```sql
select *
from stadium s1
where s1.people >= 100 and (case
                         when s1.id = 1 then (select people from stadium where id = 2) >= 100 and 
                                        (select people from stadium where id = 3) >= 100
                         when s1.id = 2 then ((select people from stadium where id = 1) >= 100 and 
                                          (select people from stadium where id = 3) >= 100) or
                                            ((select people from stadium where id = 3) >= 100 and
                                            (select people from stadium where id = 4) >= 100)
                         when s1.id = (select count(*) from stadium) - 1 then 
                                            ((select people from stadium s2 where s2.id = s1.id - 1) >= 100 and 
                                          (select people from stadium s2 where s2.id = s1.id + 1) >= 100) or
                                            ((select people from stadium s2 where s2.id = s1.id - 1) >= 100 and
                                            (select people from stadium s2 where s2.id = s1.id - 2) >= 100)
                         when s1.id = (select count(*) from stadium) then 
                                        (select people from stadium s2 where s2.id = s1.id - 1) >= 100 and 
                                        (select people from stadium s2 where s2.id = s1.id - 2) >= 100
                         else ((select people from stadium s2 where s2.id = s1.id - 1) >= 100 and 
                                (select people from stadium s2 where s2.id = s1.id + 1) >= 100) or
                                ((select people from stadium s2 where s2.id = s1.id - 1) >= 100 and
                                (select people from stadium s2 where s2.id = s1.id - 2) >= 100) or
                                ((select people from stadium s2 where s2.id = s1.id + 1) >= 100 and
                                (select people from stadium s2 where s2.id = s1.id + 2) >= 100)
                         end
                        );
```

# 177. 第N高的薪水
## 题目描述

编写一个 SQL 查询，获取 Employee 表中第 n 高的薪水（Salary）。
| Id | Salary |
|----|----|
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

例如上述 Employee 表，n = 2 时，应返回第二高的薪水 200。如果不存在第 n 高的薪水，那么查询应返回 null。
| getNthHighestSalary(2) |
|----|
| 200                    |

## 实现

```sql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
    declare M INT;
    set M = N - 1;
  RETURN (
      # Write your MySQL query statement below.
   
      select distinct Salary
      from Employee
      order by Salary desc
      limit M, 1
  );
END
```

>注意：limit M, 1的后面不能加分号;

# 184. 部门工资最高的员工

## 题目描述

Employee 表包含所有员工信息，每个员工有其对应的 Id, salary 和 department Id。
| Id | Name  | Salary | DepartmentId |
|----|----|----|----|
| 1  | Joe   | 70000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |

Department 表包含公司所有部门的信息。
| Id | Name     |
|----|----|
| 1  | IT       |
| 2  | Sales    |

编写一个 SQL 查询，找出每个部门工资最高的员工。例如，根据上述给定的表格，Max 在 IT 部门有最高工资，Henry 在 Sales 部门有最高工资。
| Department | Employee | Salary |
|----|----|----|
| IT         | Max      | 90000  |
| Sales      | Henry    | 80000  |

## 实现

```sql
select d.Name Department, e.Name Employee, e.Salary Salary 
from Employee e, Department d
where e.DepartmentId = d.Id and 
        e.Salary = (select max(e2.Salary) from Employee e2 
        			where e2.DepartmentId = e.DepartmentId)
```

# 185. 部门工资前三高的所有员工

## 题目描述

Employee 表包含所有员工信息，每个员工有其对应的工号 Id，姓名 Name，工资 Salary 和部门编号 DepartmentId 。
| Id | Name  | Salary | DepartmentId |
|----|----|----|----|
| 1  | Joe   | 85000  | 1            |
| 2  | Henry | 80000  | 2            |
| 3  | Sam   | 60000  | 2            |
| 4  | Max   | 90000  | 1            |
| 5  | Janet | 69000  | 1            |
| 6  | Randy | 85000  | 1            |
| 7  | Will  | 70000  | 1            |

Department 表包含公司所有部门的信息。
| Id | Name     |
|----|----|
| 1  | IT       |
| 2  | Sales    |

编写一个 SQL 查询，找出每个部门获得前三高工资的所有员工。例如，根据上述给定的表，查询结果应返回：
| Department | Employee | Salary |
|----|----|----|
| IT         | Max      | 90000  |
| IT         | Randy    | 85000  |
| IT         | Joe      | 85000  |
| IT         | Will     | 70000  |
| Sales      | Henry    | 80000  |
| Sales      | Sam      | 60000  |

解释：
IT 部门中，Max 获得了最高的工资，Randy 和 Joe 都拿到了第二高的工资，Will 的工资排第三。销售部门（Sales）只有两名员工，Henry 的工资最高，Sam 的工资排第二。

## 实现

>思路：对于每条记录，选择出这个记录所在的部门的==工资==大于这个记录的工资的数量最多为2个。

```sql
select d.Name Department, e.Name Employee, e.Salary Salary
from Employee e, Department d
where e.DepartmentId = d.Id and
        (select count(distinct e2.Salary) from Employee e2
         where e.DepartmentId = e2.DepartmentId and e.Salary < e2.Salary) < 3
order by d.Name, e.Salary desc;
```

# 596. 超过5名学生的课

## 题目描述

有一个courses 表 ，有: student (学生) 和 class (课程)。

请列出所有超过或等于5名学生的课。

例如,表:
| student | class      |
|----|----|
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |

应该输出:
| class   |
|----|
| Math    |

Note:
学生在每个课中不应被重复计算。

## 实现

>先以class分组，再用count函数计算每门课程的学生数。

```sql
select class
from courses
group by class
having count(distinct student) >= 5;
```

# 176. 第二高的薪水

## 题目描述

编写一个 SQL 查询，获取 Employee 表中第二高的薪水（Salary） 。
| Id | Salary |
|----|----|
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |

例如上述 Employee 表，SQL查询应该返回 200 作为第二高的薪水。如果不存在第二高的薪水，那么查询应返回 null。
| SecondHighestSalary |
|----|
| 200                 |

## 实现

>ifnull函数：ifnull(v1, v2)：如果v1不为null，返回v1，否则返回v2

### 实现1
```sql
select ifnull(
    (select distinct e.Salary
    from Employee e
    where (select count(distinct e2.Salary) from Employee e2
           where e.Salary < e2.Salary) = 1),
    null
) SecondHighestSalary
```

### 实现2

```sql
select ifnull(
    (select distinct Salary from Employee
     order by Salary desc limit 1, 1),
    null
) SecondHighestSalary;
```

[ifnull函数](https://www.runoob.com/mysql/mysql-functions.html)

