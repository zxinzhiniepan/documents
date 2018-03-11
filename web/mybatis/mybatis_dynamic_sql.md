#  mybatis_dynamic_sql.md
 - AUTHOR: xinzhiniepan
 - DATE: 2018-03-05 10:01

## 动态SQL

### 1. if

```xml
<select id="getUser" resultMap="test">
    select * from tb
    <where>
        <if test="id != null">
            id=#{id}
        </if>
        <if test="name != null">
            and name=#{name}
        </if>
    </where>
</select>
```


### 2. choose,when,otherwise( 类似switch语法)
```xml
 <select id="getUser" resultMap="Test">
     select * from tb where id = 1
     <choose>
         <!-- 相当与case -->
         <when test="name != null">
             and name like concat('%',#{name}, '%')
         </when>
         <when test="password != null">
             and password=#{password}
         </when>
         <!-- 相当于default -->
         <otherwise>
             and featured = 1
         </otherwise>
     </choose>
 </select>
```


### 3. trim, where, set
- where(自动添加where和该标签内多余的and,or等)
    * where标签
    ````xml
    <select id="getUser" resultMap="User">
        select * from tb
        <where>
            <if test="id != null">
                id=#{id}
            </if>
            <if test="name != null">
                and name like #{name}
            </if>
        </where>
    </select>
    ````

    * 等价的trim
    ```xml
    <trim prefix="WHERE" prefixOverrides="AND | OR ">
        ...
    </trim>
    ```
- set(消除无关的逗号)
    * set标签
    ```xml
    <update id="updateOne">
    update Author
        <set>
            <if test="username != null">username=#{username},</if>
            <if test="password != null">password=#{password},</if>
            <if test="introduce != null">introduce=#{introduce}</if>
        </set>
        where id=#{id}
    </update>
    ```

    * 对应的trim
    ```xml
    <trim prefix="SET" suffixOverrides=",">
        ...
    </trim>
    ```

- trim标签
    * 四个属性
        > prefix: 前缀, 通过识别是否有返回值，来在trim前添加前缀</br>
        > suffix: 后缀, 作用在trim包含的内容上添加后缀</br>
        > prefixOverrides: 对于trim包含的内容首部进行指定内容的截取</br>
        > suffixOverrides: 对于trim包含的内容尾部进行指定内容的截取</br>


### foreach(对集合进行遍历, 一般在IN中使用)

```xml
<select>
    select * from tb where id in
    <!-- java foreach: for(Item item:collection) -->
    <!-- item: 进行迭代时的别名，相当于java foreach中的item -->
    <!-- index: 指定一个名字，用于在迭代过程中，每次迭代到的位置 -->
    <!-- open: 表示该语句从什么地方开始 -->
    <!-- separator: 表示每次迭代之间用什么符号分隔 -->
    <!-- close: 表示该语句从什么地方结束 -->
    <!--
    在使用foreach的时候最关键的也是最容易出错的就是collection属性，
    该属性是必须指定的，但是在不同情况下，该属性的值是不一样的，主要有一下3种情况：
    1.如果传入的是单参数且参数类型是一个List的时候，collection属性值为list
    2.如果传入的是单参数且参数类型是一个array数组的时候，collection的属性值为array
    3.如果传入的参数是多个的时候，我们就需要把它们封装成一个Map或者Object。
    -->
    <foreach item="item" index="index" collection="collection"
        open="(" separator="," close=")">
        #{item}
    </foreach>
</select>
```

### bind(bind 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文。)

```xml
<select id="testbind" resultType="User">
    <bind name="tbind" value="'%' + user.name + '%'"/>
    select * from tb where name like tbind
</select>
```

### Multi-db vendor support
一个配置了“_databaseId”变量的 databaseIdProvider 对于动态代码来说是可用的，这样就可以根据不同的数据库厂商构建特定的语句。比如下面的例子：

```xml
<insert id="insert">
<selectKey keyProperty="id" resultType="int" order="BEFORE">
    <if test="_databaseId == 'oracle'">
        select seq_users.nextval from dual
    </if>
    <if test="_databaseId == 'db2'">
        select nextval for seq_users from sysibm.sysdummy1"
    </if>
    </selectKey>
        insert into users values (#{id}, #{name})
</insert>
```
