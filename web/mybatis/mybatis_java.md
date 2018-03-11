#  mybatis_java.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-05 00:16

## 创建SqlSessionFactoty工厂的（工具类）
- java代码(工具类)
    ```java
        ...
        import org.apache.io.Resources;
        import org.apache.session.SqlSession;
        import org.apache.session.SqlSessionFactory;
        import org.apache.session.SqlSessionFactoryBuilder;
        public class Utils
        {
            private static SqlSessionFactory sqlSessionFactory;
            static
            {
                try
                {
                    String resource = "mybatis-config.xml";
                    InputStream inputStream = Resources.getResourceAsStream(resource);
                    SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
                    sqlSessionFactory = builder.build(inputStream);
                }
                catch(IOException e)
                {
                    logger.error(e);
                }
            }
        }
    ```
- java代码(引用)
    ```java
        ...
        SqlSession sqlSession = Utils.openSession(true);//开启自动提交事务管理
        SqlSession sqlSession = Utils.openSession(false);//关闭自动提交事务处理
    ```
