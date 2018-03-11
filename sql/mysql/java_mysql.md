模糊查询的注意事项
String sql = "select * from table where name like ? order by articleNumber desc, attentionNumber desc";
ArrayList<Subject> subjects = queryBase(sql, "%" + key + "%");
