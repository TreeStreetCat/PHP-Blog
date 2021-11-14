### 1. 找出各部门薪酬前三的员工

LeetCode：[185. 部门工资前三高的所有员工](https://leetcode-cn.com/problems/department-top-three-salaries/)

```sql
select * from Employee e1 
		where 3 > (select count(DISTINCT e2.Salary) 
                		from Employee e2 where e1.Salary < e2.Salary 
               						and e1.DepartmentId = e2.DepartmentId)
```



