为查询到的结果设置默认值
    关键：IFNULL(查询结果， [为空时的默认值])
    例如：SELECT a.username,a.sch_name,IFNULL(b.state,0) state  FROM tschooluser a LEFT JOIN tschoolinfor b ON a.username=b.userName  WHERE a.area_id =1
