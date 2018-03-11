#  mybatis_mapper.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-04 19:27

## 入参
#### 1. 单参入参()
- 使用parameterType传入基础数据类型
     ```xml
        <select id="test" parameterType="int">
            select * from tb where id = #{id}
        </select>
     ```
- 使用parameterType传入复杂数据类型(传入对象)
     ```xml
        <select id="test" parameterType="object">
            select * from tb
        </select>
     ```


#### 2. 多参入参
- 使用注解进行多参入参
    * mybatis代码(相应的省略了parameterType属性)
        ```xml
        <select id="test" resultType="user">
            select * from tb where id=#{id} and name=#{name}
        </select>
        ```

    * java代码(对应的接口中对应的方法:需要导入org.apache.ibatis.annotations.Param)
        ```java
            public User test(@Param("id")int u_id, @Param("name")String u_name);
        ```

- 将多个参数封装成一个对象进行入参(大于3个参数时)
    * mybatis代码(相应的省略了parameterType属性)
        ```xml
        <select id="test" resultType="user">
            <!-- #{对象的属性名} -->
            select * from tb where id=#{id} and name=#{name}
        </select>
        ```

    * java代码(对应的接口中对应的方法:需要导入org.apache.ibatis.annotations.Param)
        ```java
            public User test(JaveBean javabean);
        ```

    * javabean
        ```java
            public class JavaBean
            {
                private int id;
                private String name;
            ...//省略getter 和 setter方法
            }
        ```

- 将多个参数传入Map集合中进行入参
    * mybatis代码(相应的省略了parameterType属性)
        ```xml
        <select id="test" resultType="user" parameterType="Map">
            <!-- #{Map中的key值} -->
            select * from tb where id=#{id} and name=#{name}
        </select>
        ```
    * java代码(对应的接口中对应的方法:需要导入org.apache.ibatis.annotations.Param)
        ```java
            public User test(Map map);
        ```
        ```java
            Map map = new HashMap();
            map.put("id", 12);
            map.put("name", "zhanghua");
        ```


## sql(定义可以重用的代码段)
- sql元素
   ```xml
   <sql id="sqlColumn">
        ${alias}.id, ${alias}.name
   </sql>
   ```
- 包含sql元素
    ```xml
    <select id="select" resultType="User">
        selct <include refid="sqlColumn"/>
        from tb
    </select>
    ```
- \#{} 和 ${}的区别
默认情况下,使用#{}格式的语法会导致 MyBatis 创建预处理语句属性并安全地设置值（比如?）。这样做更安全，更迅速，通常也是首选做法，不过有时你只是想直接在 SQL 语句中插入一个不改变的字符串。比如，像 ORDER BY，你可以这样来使用：
    ```xml
    ORDER BY ${columnName}
    ```


## Result Maps
#### 1. 将结果自动映射到HashMap中, map中的key值为查询到的列名
```xml
<!-- 则将查询结果放在map中: (key:userName, value: userName的结果) -->
<select id="selectUsers" resultType="map">
    select id as id, name as userName from tb
    where id = #{id}
</select>
```

#### 2. 将结果自动映射到JavaBean中, 其中javabean中的属性名应该对应相应的列名值
```xml
<!-- 则将查询结果放在JavaBean中: (key:队形的属性名, value: userName的结果) -->
<select id="selectUsers" resultType="com.app.pojo.User">
    select id as id, name as userName from tb
    where id = #{id}
</select>
```
使用别名简化
```xml
<!-- in mybatis-config.xml -->
<typeAliases>
    <typeAlias type="com.app.pojo.User" alias="User"/>
</typeAliases>


<!-- in SQL Mapping XML file -->
<select id="selectUsers" resultType="User">
    select id as id, name as userName from tb
    where id = #{id}
</select>
```

这些情况下,MyBatis 会在幕后自动创建一个 ResultMap,基于属性名来映射列到 JavaBean 的属性上。如果列名没有精确匹配,你可以在列名上使用 select 字句的别名(一个 基本的 SQL 特性)来匹配标签。比如:
```xml
<select id="selectUsers" resultType="User">
    select
        user_id             as "id",
        user_name           as "userName",
        hashed_password     as "hashedPassword"
    from some_table
    where id = #{id}
</select> 
```

#### 3. resultMap
子标签
- constructor – 类在实例化时，用来注入结果到构造方法
    - idArg – ID参数,标记结果作为ID可以提升整体效能
    - arg – 注入到构造的一个普通结果
- id  – 一个ID结果，标记结果ID可以提升整体效能
- result –  注入到字段或者JavaBean的属性的普通结果
- association – 一个复杂的类型关联;许多结果将包成这种类型
    - 嵌入结果映射 – 结果映射自身的关联,或者参考一个
- collection – 复杂类型的集
    - 嵌入结果映射 – 结果映射自身的集,或者参考一个
- discriminator – 使用结果值来决定使用哪个结果映射
    - case – 基于某些值的结果映射
        - 嵌入结果映射 – 这种情形结果也映射它本身,因此可以包含很多相 同的元素,或者它可以参照一个外部的结果映射。

###### ResultMap Attributes

| 属性 | 描述 |
| ---- | ---- |
| id | 当前命名空间中的一个唯一标识，用于标识一个result map. |
| type | 类的全限定名, 或者一个类型别名 (内置的别名可以参考上面的表格). |
| autoMapping | 如果设置这个属性，MyBatis将会为这个ResultMap开启或者关闭自动映射。这个属性会覆盖全局的属性autoMappingBehavior。默认值为：unset。使用bool值（true|false） |

##### id & result
```xml
<resultMap id="当前命名空间的一个唯一标识，用于表示一个result map" autoMapping="true"/>
    <!-- 
        id和result标签的属性:
        property:映射到列结果的字段或属性。
            如果匹配的是存在的,和给定名称相同的JavaBeans 的属性,那么就会使用。
            否则 MyBatis 将会寻找给定名称 property 的字段。
            这两种情形你可以使用通常点式的复杂属性导航。
            比如,你 可以这样映射一些东西: “username” ,或者映射到一些复杂的东西: “address.street.number” 。 
        column: 从数据库中得到的列名,或者是列名的重命名标签。这也是通常和会 传递给 resultSet.getString(columnName)方法参数中相同的字符串。
        javaType:一个 Java 类的完全限定名,或一个类型别名(参考上面内建类型别名 的列表) 。
            如果你映射到一个 JavaBean,MyBatis 通常可以断定类型。
            然而,如果你映射到的是 HashMap,那么你应该明确地指定 javaType 来保证所需的行为。
        jdbcTyep:JDBC 类型是仅 仅需要对插入,更新和删除操作可能为空的列进行处理。
            这是 JDBC jdbcType 的需要,而不是 MyBatis 的。
            如果你直接使用 JDBC 编程,你需要指定 这个类型-但仅仅对可能为空的值。
        typeHeadler:我们在前面讨论过默认的类型处理器。
            使用这个属性,你可以覆盖默 认的类型处理器。
            这个属性值是类的完全限定名或者是一个类型处理 器的实现,或者是类型别名。
    -->

    <id property="id" column="u_id"/>
    <result property="userName" column="u_userName"/>
</resultMap>
```

##### 支持的JDBC类型(详情参照官方文档)

##### 构造方法(参照官方文档)
javabean的构造方法
```java
public class User
{
    public User(int id, String userName, int age)
    {
        ...
    }
}
```

对应的xml文件
```xml
<resultMap id="testUser" type="User">
    <constructor>
        <!-- 与顺序有关 -->
        <idArg column="id" javaType="int"/>
        <arg column="username" javaType="String"/>
        <arg column="age" javaType="_int"/>
    </constructor>
</resultMap>
```

```xml
<resultMap id="testUser" type="User">
    <constructor>
        <!-- 
        column: 来自数据库的类名,或重命名的列标签。
            这和通常传递给 resultSet.getString(columnName)方法的字符串是相同的。
        javaType: 一个 Java 类的完全限定名,或一个类型别名(参考上面内建类型别名的列表)。 
            如果你映射到一个 JavaBean,MyBatis 通常可以断定类型。
            然而,如 果你映射到的是 HashMap,那么你应该明确地指定 javaType 来保证所需的 行为。
        jdbcType: 在这个表格之前的所支持的 JDBC 类型列表中的类型。
            JDBC 类型是仅仅 需要对插入, 更新和删除操作可能为空的列进行处理。
            这是 JDBC 的需要, jdbcType 而不是 MyBatis 的。
            如果你直接使用 JDBC 编程,你需要指定这个类型-但 仅仅对可能为空的值。
        typeHandler: 我们在前面讨论过默认的类型处理器。使用这个属性,你可以覆盖默认的 类型处理器。 
            这个属性值是类的完全限定名或者是一个类型处理器的实现, 或者是类型别名。
        select: 用于加载复杂类型属性的映射语句的ID,从column中检索出来的数据，将作为此select语句的参数。
            具体请参考Association标签。
        resultMap: ResultMap的ID，可以将嵌套的结果集映射到一个合适的对象树中，
            功能和select属性相似，它可以实现将多表连接操作的结果映射成一个单一的ResultSet。
            这样的ResultSet将会将包含重复或部分数据重复的结果集正确的映射到嵌套的对象树中。
            为了实现它, MyBatis允许你 “串联”
            ResultMap,以便解决嵌套结果集的问题。想了解更多内容，请参考下面的Association元素。
        name: 构造方法形参的名字。通过指定具体的名字你可以以任意顺序写入arg元素。参看上面的解释。从3.4.3版本起。
        -->
        <!-- 参数元素不匹配 -->
        <idArg column="id" javaType="int" name="id" />
        <arg column="age" javaType="_int" name="age" />
        <arg column="username" javaType="String" name="username" />
    </constructor>
</resultMap>
```

#### 关联
```xml
<resultMap id="testblog" type="Blog">
    <!-- 
        property: 映射到列结果的字段或属性。
            如果匹配的是存在的,和给定名称相同的property JavaBeans的属性,那么就会使用。
            否则 MyBatis 将会寻找给定名称的字段。
            这两种情形你可以使用通常点式的复杂属性导航。
            比如,你可以这样映射一些东西 :“username”,或者映射到一些复杂的东西:“address.street.number” 。
        javaType:  一个 Java类的完全限定名,或一个类型别名(参考上面内建类型别名的列表)。
            如果你映射到一个JavaBean,MyBatis通常可以断定类型。
            然而,如javaType果你映射到的是HashMap,那么你应该明确地指定javaType来保证所需的行为。
        jdbcType:在这个表格之前的所支持的 JDBC 类型列表中的类型。
            JDBC 类型是仅仅 需要对插入, 更新和删除操作可能为空的列进行处理。
            这是JDBC的需要,jdbcType而不是MyBatis的。
            如果你直接使用 JDBC 编程,你需要指定这个类型-但 仅仅对可能为空的值。
        typeHandler: 我们在前面讨论过默认的类型处理器。
            使用这个属性,你可以覆盖默认的 typeHandler 类型处理器。 
            这个属性值是类的完全限定名或者是一个类型处理器的实现, 或者是类型别名。
    -->
    <association property="author" column="blog_author_id" javaType="Author">
        <id property="id" column="author_id"/>
        <result property="username" column="author_username"/>
    </association>
</resultMap>
```

关联元素处理“有一个”类型的关系。比如,在我们的示例中,一个博客有一个用户。 关联映射就工作于这种结果之上。你指定了目标属性,来获取值的列,属性的 java 类型(很 多情况下 MyBatis 可以自己算出来) ,如果需要的话还有 jdbc 类型,如果你想覆盖或获取的 结果值还需要类型控制器。

关联中不同的是你需要告诉 MyBatis 如何加载关联。MyBatis 在这方面会有两种不同的 方式:
- 嵌套查询:通过执行另外一个 SQL 映射语句来返回预期的复杂类型。
- 嵌套结果:使用嵌套结果映射来处理重复的联合结果的子集。首先,然让我们来查看这个元素的属性。所有的你都会看到,它和普通的只由select和resultMap属性的结果映射不同。

##### 关联的嵌套查询
```xml
<resultMap id="blogResult" type="Blog">
    <!-- 
        column: 来自数据库的类名,或重命名的列标签。
            这和通常传递给 resultSet.getString(columnName)方法的字符串是相同的。 
            column注意:要处理复合主键,你可以指定多个列名通过column=”{prop1=col1,prop2=col2}”
            这种语法来传递给嵌套查询语 句。这会引起 prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句。
        select: 另外一个映射语句的 ID,可以加载这个属性映射需要的复杂类型。
            获取的在列属性中指定的列的值将被传递给目标 select 语句作为参数。
            表格后面 有一个详细的示例。 
            select注意:要处理复合主键,你可以指定多个列名通过column=”{prop1=col1,prop2=col2}”
            这种语法来传递给嵌套查询语 句。这会引起 prop1 和 prop2 以参数对象形式来设置给目标嵌套查询语句。
        fetchType: 可选的。有效值为lazy和eager。如果使用了，它将取代全局配置参数lazyLoadingEnabled。
    -->
    <association property="author" column="author_id" javaType="Author" select="selectAuthor"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
    select * from blog where id=!{id}
</select>

<select id="selectAuthor" resultType="Author">
    select * from author wherr id=!{id}
</select>
```

##### 关联的嵌套结果
```xml
<resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <association property="author" column="blog_author_id" javaType="Author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
    select
        B.id            as blog_id,
        B.title         as blog_title,
        B.author_id     as blog_author_id,
        A.id            as author_id,
        A.username      as author_username,
        A.password      as author_password,
        A.email         as author_email,
        A.bio           as author_bio
    from Blog B left outer join Author A on B.author_id = A.id
    where B.id = #{id}
 </select>
```
非常重要: id元素在嵌套结果映射中扮演着非 常重要的角色。你应该总是指定一个或多个可以唯一标识结果的属性。实际上如果你不指定它的话, MyBatis仍然可以工作,但是会有严重的性能问题。在可以唯一标识结果的情况下, 尽可能少的选择属性。主键是一个显而易见的选择（即使是复合主键）。

```xml
<resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <association property="author" javaType="Author">
        <id property="id" column="author_id"/>
        <result property="username" column="author_username"/>
        <result property="password" column="author_password"/>
        <result property="email" column="author_email"/>
        <result property="bio" column="author_bio"/>
    </association>
</resultMap>
```

```xml
<resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <!--association:
        resultMap: 这是结果映射的ID,可以映射关联的嵌套结果到一个合适的对象图中。
            这是一种替代方法来调用另外一个查询语句。
            这允许你联合多个表来合成到 resultMap 一个单独的结果集。
            这样的结果集可能包含重复,数据的重复组需要被分 解,合理映射到一个嵌套的对象图。
            为了使它变得容易,MyBatis 让你“链 接”结果映射,来处理嵌套结果。    
        columnPrefix: 当连接多表时，你将不得不使用列别名来避免ResultSet中的重复列名。
            指定columnPrefix允许你映射列名到一个外部的结果集中。 请看后面的例子。
        notNullColumn: 默认情况下，子对象仅在至少一个列映射到其属性非空时才创建。
            通过对这个属性指定非空的列将改变默认行为，这样做之后Mybatis将仅在这些列非空时才创建一个子对象。
            可以指定多个列名，使用逗号分隔。默认值：未设置(unset)。
        autoMapping: 如果使用了，当映射结果到当前属性时，
            Mybatis将启用或者禁用自动映射。 该属性覆盖全局的自动映射行为。 
            注意它对外部结果集无影响，所以在select or resultMap属性中这个是毫无意义的
            。 默认值：未设置(unset)。
    -->
    <association property="author"
        resultMap="authorResult" />
    <association property="coAuthor"
        resultMap="authorResult"
        columnPrefix="co_" />
</resultMap>

<resultMap id="authorResult" type="Author">
    <id property="id" column="author_id"/>
    <result property="username" column="author_username"/>
    <result property="password" column="author_password"/>
    <result property="email" column="author_email"/>
    <result property="bio" column="author_bio"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
    select
        B.id            as blog_id,
        B.title         as blog_title,
        A.id            as author_id,
        A.username      as author_username,
        A.password      as author_password,
        A.email         as author_email,
        A.bio           as author_bio,
        CA.id           as co_author_id,
        CA.username     as co_author_username,
        CA.password     as co_author_password,
        CA.email        as co_author_email,
        CA.bio          as co_author_bio
    from Blog B
    left outer join Author A on B.author_id = A.id
    left outer join Author CA on B.co_author_id = CA.id
    where B.id = #{id}
</select>
```

#### 集合
##### 集合的嵌套查询
```xml
<resultMap id="blogResult" type="Blog">
    <collection property="posts" javaType="ArrayList" 
    column="id" ofType="Post" select="selectPostsForBlog"/>
</resultMap>

<select id="selectBlog" resultMap="blogResult">
    SELECT * FROM BLOG WHERE ID = #{id}
</select>

<select id="selectPostsForBlog" resultType="Post">
    SELECT * FROM POST WHERE BLOG_ID = #{id}
</select>
```

##### 集合的嵌套结果
```xml
<!--其属性与关联差不多 
    ofType: 集合包含的类型
-->
<select id="selectBlog" resultMap="blogResult">
    select
        B.id as blog_id,
        B.title as blog_title,
        B.author_id as blog_author_id,
        P.id as post_id,
        P.subject as post_subject,
        P.body as post_body,
    from Blog B
    left outer join Post P on B.id = P.blog_id
    where B.id = #{id}
</select>

<resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <collection property="posts" ofType="Post">
        <id property="id" column="post_id"/>
        <result property="subject" column="post_subject"/>
        <result property="body" column="post_body"/>
    </collection>
</resultMap>

<resultMap id="blogResult" type="Blog">
    <id property="id" column="blog_id" />
    <result property="title" column="blog_title"/>
    <collection property="posts" ofType="Post" resultMap="blogPostResult" columnPrefix="post_"/>
</resultMap>

<resultMap id="blogPostResult" type="Post">
    <id property="id" column="id"/>
    <result property="subject" column="subject"/>
    <result property="body" column="body"/>
</resultMap>
```

#### 鉴别器
有时一个单独的数据库查询也许返回很多不同 (但是希望有些关联) 数据类型的结果集。 鉴别器元素就是被设计来处理这个情况的, 还有包括类的继承层次结构。 鉴别器非常容易理 解,因为它的表现很像 Java 语言中的 switch 语句。定义鉴别器指定了 column 和 javaType 属性。 列是 MyBatis 查找比较值的地方。 JavaType 是需要被用来保证等价测试的合适类型(尽管字符串在很多情形下都会有用)。比如:
```xml
<resultMap id="vehicleResult" type="Vehicle">
    <id property="id" column="id" />
    <result property="vin" column="vin"/>
    <result property="year" column="year"/>
    <result property="make" column="make"/>
    <result property="model" column="model"/>
    <result property="color" column="color"/>
    <discriminator javaType="int" column="vehicle_type">
        <case value="1" resultMap="carResult"/>
        <case value="2" resultMap="truckResult"/>
        <case value="3" resultMap="vanResult"/>
        <case value="4" resultMap="suvResult"/>
    </discriminator>
</resultMap>

```

另外一 种语法来做简洁的映射风格。
```xml
<resultMap id="vehicleResult" type="Vehicle">
    <id property="id" column="id" />
    <result property="vin" column="vin"/>
    <result property="year" column="year"/>
    <result property="make" column="make"/>
    <result property="model" column="model"/>
    <result property="color" column="color"/>
    <discriminator javaType="int" column="vehicle_type">
        <case value="1" resultType="carResult">
            <result property="doorCount" column="door_count" />
        </case>
        <case value="2" resultType="truckResult">
            <result property="boxSize" column="box_size" />
            <result property="extendedCab" column="extended_cab" />
        </case>
        <case value="3" resultType="vanResult">
            <result property="powerSlidingDoor" column="power_sliding_door" />
        </case>
        <case value="4" resultType="suvResult">
            <result property="allWheelDrive" column="all_wheel_drive" />
        </case>
    </discriminator>
</resultMap>

```

## 4. 自动映射
有三种自动映射等级：
- NONE - 禁用自动映射。仅设置手动映射属性。
- PARTIAL - 将自动映射结果除了那些有内部定义内嵌结果映射的(joins).
- FULL - 自动映射所有。
默认值是PARTIAL，这是有原因的。当使用FULL时，自动映射会在处理join结果时执行，并且join取得若干相同行的不同实体数据，因此这可能导致非预期的映射。下面的例子将展示这种风险：
```xml
<select id="selectBlog" resultMap="blogResult">
    select
        B.id,
        B.title,
        A.username,
    from Blog B left outer join Author A on B.author_id = A.id
    where B.id = #{id}
</select>

<resultMap id="blogResult" type="Blog">
    <association property="author" resultMap="authorResult"/>
</resultMap>

<resultMap id="authorResult" type="Author">
    <result property="username" column="author_username"/>
</resultMap>
    ```

## 5. 缓存(详情查看官方文档)
