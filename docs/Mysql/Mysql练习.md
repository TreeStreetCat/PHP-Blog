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



