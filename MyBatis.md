## 基本配置

1. src 目录下放conf.xml 配置信息

   ```
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
   
   	<properties resource="db.properties"/>
   	
   	<!-- 配置实体类的别名 -->
   	<typeAliases>
   		<!-- <typeAlias type="com.atguigu.day03_mybaits.test1.User" alias="_User"/> -->
   		<package name="com.atguigu.day03_mybaits.bean"/>
   	</typeAliases>
   <!-- 
   	development : 开发模式
   	work : 工作模式
    -->
   	<environments default="development">
   		<environment id="development">
   			<transactionManager type="JDBC" />
   			<dataSource type="POOLED">  <!-- 设置为池化 -->
   				<property name="driver" value="com.mysql.jdbc.Driver" />
   				<property name="url" value="jdbc:mysql://localhost:3306/testmybatis/" />
   				<property name="username" value="root" />
   				<property name="password" value="759486" />
   			</dataSource>
   		</environment>
   	</environments>
   	
   	<mappers>
   		<mapper resource="com/atguigu/day03_mybaits/test1/userMapper.xml"/>
   		<mapper resource="com/atguigu/day03_mybaits/test2/userMapper.xml"/>
   		<mapper class="com.atguigu.day03_mybaits.test3.UserMapper"/>
   		...
   	</mappers>
   </configuration>
   ```

2. 定义操作表的sql映射文件xxMapper.xml

   ```
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
   "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   
   <mapper namespace="com.atguigu.day03_mybaits.test1.userMapper">
   
   	 <select id="getUser" parameterType="int" resultType="com.atguigu.day03_mybaits.bean.User">
   	 	select * from users where id=#{id}
   	 	<!-- select语句 -->
   	 </select>
   	 
   </mapper>
   ```


3.在conf.xml中注册xxmMapper.xml文件

- conf.xml应放在包的同级目录下！

```
<mapper resource="com/atguigu/day03_mybaits/test1/userMapper.xml"/>
```

4.构建SqlSessionFactory（写为Mybatis工具类）

```
String resource = "conf.xml";
InputStream is = Test.class.getClassLoder().getResourceAsStream(resource); //构建输入流
SqlSessionFactory factory = new SqlSessionBuilder().build(is); //把流输入factory 
SqlSession session = factory.openSession();//相当于queryRunner

String statement = "..."; //...为mapper中的namespace+id
User user = seession.selectOne(stament, 2); //获取user对象
```

## CRUD操作

### 基于xml（更优）：

### 添加： 

```
<insert id="addUser" parameterType="cn.medwin.test.User">
	insert into user(name. age) values(#{name}, #{age})
	<!-- 用到了反射 -->
</insert>
```

```
int insert = session.insert(statement，new User（..）);//执行操作并用int保存被影响行数
```



### 删除：

```
<delete id="deleteUser" paremeterType=“int”>
	delete from users where id = #{id}
	<!-- {}内内容可随意写，但一般写属性名-->
</delete>
```

### 更新:

```
<update id="updateUser" parameterType="cn.medwin.test.User">
	update user set name = #{name}, age = #{age} where id = #{id}
</update>
```

### 查询：

```
<select id="getAllUsers" resultType="cn.medwin.test.User">
	select * from users
</select>
```

- session默认手动提交，因此在insert操作后需要加上,表中才会插入

  ```
  session.commit();
  
  session.close();//记得关闭资源
  ```

  要自动添加，将factory.openSession()参数改为true

- 为什么查询单个时不自动提交也没commit会得到结果？

### 基于注解：

需要有xxxMapper接口

```
public interface UserMapper(){

	@insert("此处为xml法中<>之间语句，如下")
    int add(User user);
    
    @delete(delete from users where id = #{id})
    int deleteById(int id);
    
    ...
    int update(User user);
    
    User getById(int id);
    
    List<User> getAll();
}
```

```
test中：

UserMapper mapper = session.getMapper(UserMapper.class);
//得到产生的动态对象

int add = mapper.add(new User(id, name, age))
```

同样需要在conf.xml注册，写法：

```

<mapper class="com.atguigu.day03_mybaits.test3.UserMapper"/>
```



## 配置文件中的别名以及mapper中的namespace

我们可以通过在主配置文件中配置**别名**，就不再需要指定完整的包名了 

别名的基本用法： 

```
<configuration>  
    <typeAliases>  
      <typeAlias type="com.domain.Student" alias="Student"/>  
  </typeAliases>  
  ......  
</configuration>  

<!-- 另外一种形式:用包名 -->
<package name="com.medwin.mybatis.test1">
```

但是如果每一个实体类都这样配置还是有点麻烦这时我们可以**直接指定package的名字**， mybatis会自动扫描指定包下面的javabean，并且默认设置一个别名，默认的名字为： javabean 的首字母小写的非限定类名来作为它的别名（其实别名是**不分大小写**的）。

```
<typeAliases>  
    <package name="com.domain"/>  
</typeAliases>  
```

也可在javabean 加上注解@Alias 来自定义别名， 例如： @Alias(student) 

这样，在Mapper中我们就不用每次配置都写类的全名了，但是**有一个例外，那就是namespace**。 

### namespace属性

在MyBatis中，Mapper中的namespace用于绑定Dao接口的，即面向接口编程。

它的好处在于当使用了namespace之后就可以不用写接口实现类，业务逻辑会直接通过这个绑定寻找到相对应的SQL语句进行对应的数据处理

```
student = (Student) session.selectOne("com.domain.Student.selectById", new Integer(10));  
```

```
<mapper namespace="com.domain.StudentMapper">    
  
    <select id="selectById" parameterType="int" resultType="student">    
       select * from student where id=#{id}    
    </select>  
       
</mapper>    
```

## 可优化的地方

### 将数据库配置单独放在一个properties文件中，如db.properties

此时conf.xml加上

```
<properties resource="db.properties">
```

原dataSource中内容改为

```
<property name="driver" value="${driverClassName}" />
<property name="url" value="${url}" />
<property name="username" value="${username}" />
<property name="password" value="${password}" />
```

## 解决字段名与属性名不一致冲突

**将sql语句写为取别名形式**

如select写为：

```
select origin_name1 alias1, origin_name2 alias2... FROM orders WHERE ...
```

origin_name为数据库表中字段名，alias为javabean中属性名

**或写为resultMap：**	

		<resultMap type="Order" id="getOrder2Map">
		<id property="id" column="order_id"/>
		<result property="orderNo" column="order_no"/>
		<result property="price" column="order_price"/>
	</resultMap>
其中，id属性专门针对主键

- 但是！妙馨老大发现不改也行？？？

## 一对一关联

老师和班级的一对一关系（class中有teacher属性）：

1. #### 联表查询

   ```
   select * FROM class c,teacher t WHERE c.teacher_id=t.t_id AND c.c_id=1
   ```

2. **执行两次查询**

   ```
   select * from class...
   select * from teacher...
   ```

 **方式一：嵌套结果**: 使用嵌套结果映射来处理重复的联合结果的子集（封装数据并去重复）

联表查询的resultMap下加上<association>：

```
<!-- 
		根据班级id查询班级信息(带老师的信息)
		##1. 联表查询
		SELECT * FROM class c,teacher t WHERE c.teacher_id=t.t_id AND c.c_id=1;
		
		##2. 执行两次查询
		SELECT * FROM class WHERE c_id=1;  //teacher_id=1
		SELECT * FROM teacher WHERE t_id=1;//使用上面得到的teacher_id
	 -->
	<!-- 
		方式一：嵌套结果：使用嵌套结果映射来处理重复的联合结果的子集
		         封装联表查询的数据(去除重复的数据)
		select * from class c, teacher t where c.teacher_id=t.t_id and  c.c_id=1
	-->
	
	<select id="getClass" parameterType="int" resultMap="getClassMap">
		SELECT * FROM class c,teacher t WHERE c.teacher_id=t.t_id AND c.c_id=#{id}
	</select>
	
	<resultMap type="Classes" id="getClassMap">
		<id property="id" column="c_id"/>
		<result property="name" column="c_name"/>
		
		<association property="teacher" javaType="Teacher">
			<id property="id" column="t_id"/>
			<result property="name" column="t_name"/>
		</association>
		
	</resultMap>
```

**方式二： 嵌套查询**：通过执行另外一个sql映射语句来返回预期的复杂类型（两次查询两个Mapper）

```
	<!-- 
	方式二：嵌套查询：通过执行另外一个SQL映射语句来返回预期的复杂类型
		SELECT * FROM class WHERE c_id=1;
		SELECT * FROM teacher WHERE t_id=1   //1 是上一个查询得到的teacher_id的值
	-->
	<select id="getClass2" parameterType="int" resultMap="getClass2Map">
		SELECT * FROM class WHERE c_id=#{id}
	</select>
	
	<select id="getTeacher" parameterType="int" resultType="Teacher">
		SELECT t_id id, t_name name FROM teacher WHERE t_id=#{id}
	</select>
	
	<resultMap type="Classes" id="getClass2Map">
		<id property="id" column="c_id"/>
		<result property="name" column="c_name"/>
		<association property="teacher" column="teacher_id" select="getTeacher">
		</association>
	</resultMap>
```

## 一对多关联

班级和学生的一对多关系（班级中有List<Student> 属性）

一对多时，用<collection>

**嵌套结果**（List有ofType属性）：

```
	<!-- 
		根据classId查询对应的班级信息,包括学生,老师
	 -->
	<!-- 
	方式一: 嵌套结果: 使用嵌套结果映射来处理重复的联合结果的子集
	SELECT * FROM class c, teacher t,student s WHERE c.teacher_id=t.t_id AND c.C_id=s.class_id AND  c.c_id=1
	 -->
	
	<select id="getClass" parameterType="int" resultMap="getClassMap">
		SELECT * FROM class c, teacher t,student s WHERE c.teacher_id=t.t_id AND c.C_id=s.class_id AND  c.c_id=#{id}
	</select>
	
	<resultMap type="Classes" id="getClassMap">
		<id property="id" column="c_id"/>
		<result property="name" column="c_name"/>
		
		<association property="teacher" javaType="Teacher">
			<id property="id" column="t_id"/>
			<result property="name" column="t_name"/>
		</association>
		
		<collection property="students" ofType="Student">
			<id property="id" column="s_id"/>
			<result property="name" column="s_name"/>
		</collection>
	</resultMap>
```

**嵌套查询**(联系几个表就有几个select)：

```
<!-- 
		方式二：嵌套查询：通过执行另外一个SQL映射语句来返回预期的复杂类型
			SELECT * FROM class WHERE c_id=1;
			SELECT * FROM teacher WHERE t_id=1   //1 是上一个查询得到的teacher_id的值
			SELECT * FROM student WHERE class_id=1  //1是第一个查询得到的c_id字段的值
	 -->
	
	<select id="getClass2" resultMap="getClass2Map">
		SELECT * FROM class WHERE c_id=#{id}
	</select>
	<select id="getTeacher" resultType="Teacher">
		SELECT t_id id, t_name name FROM teacher WHERE t_id=#{id}
	</select>
	<select id="getStudent" resultType="Student">
		SELECT s_id id, s_name name FROM student WHERE class_id=#{id}
	</select>
	
	<resultMap type="Classes" id="getClass2Map">
		<id property="id" column="c_id"/>
		<result property="name" column="c_name"/>
		
		<association property="teacher" column="teacher_id" select="getTeacher"></association>
		<collection property="list" column="c_id" select="getStudent"></collection>
	</resultMap>
```

##  动态SQL与模糊查询

需要多写一个Condition类来存放条件：name、minAge、maxAge

Mapper：

		<!-- 
	
		实现多条件查询用户(姓名模糊匹配, 年龄在指定的最小值到最大值之间)
	
		 --> 
	
	<select id="getUser" parameterType="ConditionUser" resultType="User">
		select * from d_user where 
		<!-- 在select中使用jstl语法判断 -->
		<if test='name != "%null%"'>
			 name like #{name} and 
		</if>
	
		age between #{minAge} and #{maxAge}
	</select>
test类：	

		String statement = "com.atguigu.day03_mybaits.test7.userMapper.getUser";
		String name = "o";
		name = null;
		
		//输入条件
		ConditionUser parameter = new ConditionUser("%"+name+"%", 13, 18);
		List<User> list = session.selectList(statement, parameter);
		System.out.println(list);
name模糊查询，**要判断name是否为空**： 

在select中使用jstl语法判断 。

- 完整sql语句：

```
select * from d_user where name like #{name} and age between #{minAge} and #{maxAge}
```



## 调用存储过程

eg：在数据库创建存储过程，查询得到男性或女性的数量，如果传入的是0就是女性否则是男性

<select>有statement参数（指定preparedstmt或stmt）

<parameter>有jdbcType、mode参数。jdbcType参数，是jdbc要求的而不是mybatis（删除或更新可能为空的列）

## Mybatis缓存

如大多数持久型框架一样，mybatis提供了一级缓存和二级缓存的支持

- **一级缓存**是SqlSession级别的缓存。在操作数据库时需要构造 **sqlSession对象**，在对象中有一个(内存区域)数据结构（**HashMap**）用于存储缓存数据。不同的sqlSession之间的缓存数据区域（HashMap）是**互相不影响**的。

  一级缓存的作用域是**同一个SqlSession**，在同一个sqlSession中两次执行相同的sql语句，第一次执行完毕会将数据库中查询的数据写到缓存（内存），**第二次**会从缓存中获取数据将**不再从数据库查询**，从而提高查询效率。当一个sqlSession结束后该sqlSession中的一级缓存也就不存在了。**Mybatis默认开启一级缓存。**

- **二级缓存**是mapper级别的缓存（同一个mapper.xml范围内），多个SqlSession去-----操作**同一个Mapper**的sql语句，多个SqlSession去操作数据库得到数据会存在二级缓存区域，多个SqlSession可以共用二级缓存，二级缓存是**跨SqlSession**的。（默认不开启）

  二级缓存是多个SqlSession共享的，其作用域是**mapper的同一个namespace**，并可以自定数据源，如Ehcache

- 缓存数据更新机制：当某一个作用域（一级session、二级namespace）进行了C(insert)/U/D操作后，或调用了session.clearCache（）。则 默认该作用域下所有select中的缓存将被清空

**开启二级缓存**：在mapper.xml中加<cache>标签，实体类要实现Serializable接口

