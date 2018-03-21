#  page.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-15 10:28

##

## 分页查询
##### 没有limit使用伪列rownum
```sql
-- 其中rownum<=10为下限，rn>5是上限
SELECT * FROM (SELECT emp.*, rownum rn FROM emp WHERE rownum <= 10 ORDER BY empno ASC) WHERE rn > 5  ;
```r
