### 1. 找出各部门薪酬前三的员工

LeetCode：[185. 部门工资前三高的所有员工](https://leetcode-cn.com/problems/department-top-three-salaries/)

解析：部门工资前三的所有员工，这里注意是 每个部门工资前三，不是所有员工工资前三，只需根据子查询查询大于当前员工工资的人数 小于3即可。

这里 `distinct(Salary)`  表示去除重复的薪酬比较，比如 该部门有五人，工资分别是 `7000 8000 8000 9000 6000`，使用 `distinct()` 则会查出 `7000 8000 8000 9000`，不使用 `distinct()` 则会查出 `8000 8000 9000`

```sql
select * from Employee e1 
		where 3 > (select count(DISTINCT e2.Salary) 
                		from Employee e2 where e1.Salary < e2.Salary 
               						and e1.DepartmentId = e2.DepartmentId)
```

<br>

### 2. 删除重复的电子邮箱

LeetCode：[196. 删除重复的电子邮箱](https://leetcode-cn.com/problems/delete-duplicate-emails/)

```sql
delete p1 from Person p1 join Person p2 on p1.Email = p2.Email where p1.Id > p2.Id
```

在 [DELETE 官方文档](https://dev.mysql.com/doc/refman/8.0/en/delete.html)中，给出了这一用法，比如下面这个DELETE语句👇

```sql
delete t1 from t1 left join t2 on t1.id=t2.id where t2.id is NULL;
```

这种 `DELETE` 方式很陌生，竟然和 `SELETE` 的写法类似。它涉及到 `t1` 和 `t2` 两张表，`DELETE t1` 表示要删除 `t1` 的一些记录，具体删哪些，就看 `WHERE` 条件，满足就删；

这里删的是t1表中，跟t2匹配不上的那些记录。

**拓展：**查询重复的记录

```sql
select * from Person group by Email having count(*) > 1 
```

<br>

### 3. 分数排名

LeetCode： [178. 分数排名](https://leetcode-cn.com/problems/rank-scores/)

解析：首先解析 **“排名” ** ，根据分数排名，即当前排名就是当前有多少人分数高于或者低于，假设分数 100 最高，小于 100 的 可以写作 `where score < 100`， 再根据 `count()` 算出排名。

```sql
select a.Score "Score", 
	(select count(distinct b.Score) from Scores b where a.Score <= b.Score ) as "Rank" 
		from Scores a order by a.Score DESC
```















