[toc]

# 一、MyBatis简介
## 1 、MyBatis历史
MyBatis最初是Apache的一个开源项目 iBatis , 2010年 6 月这个项目由Apache Software Foundation迁移到了Google Code。随着开发团队转投Google Code旗下， iBatis3.x正式更名为MyBatis。代码于 2013 年 11 月迁移到Github。

iBatis一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。 iBatis提供的持久层框架包括SQL Maps和Data Access Objects（DAO）。

## 2 、MyBatis特性
1 ） MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的持久层框架
2 ） MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集
3 ） MyBatis可以使用简单的XML或注解用于配置和原始映射，将接口和Java的POJO（Plain Old Java Objects，普通的Java对象）映射成数据库中的记录
4 ） MyBatis 是一个 半自动的ORM（Object Relation Mapping）框架

## 3 、MyBatis下载
MyBatis下载地址：https://github.com/mybatis/mybatis-3

## 4 、和其它持久化层技术对比
- JDBC
    - SQL 夹杂在Java代码中耦合度高，导致硬编码内伤
    - 维护不易且实际开发需求中 SQL 有变化，频繁修改的情况多见
    - 代码冗长，开发效率低

- Hibernate 和 JPAd
    - 操作简便，开发效率高
    - 程序中的长难复杂 SQL 需要绕过框架
    - 内部自动生产的 SQL，不容易做特殊优化
    - 基于全映射的全自动框架，大量字段的 POJO 进行部分映射时比较困难。
    - 反射操作太多，导致数据库性能下降

- MyBatis
    - 轻量级，性能出色
    - SQL 和 Java 编码分开，功能边界清晰。Java代码专注业务、SQL语句专注数据
    - 开发效率稍逊于HIbernate，但是完全能够接受

# 二、搭建MyBatis
## 1、开发环境
IDE：idea 2024.3
构建工具：maven 3.9.9
MySQL版本：MySQL 8.0.30
MyBatis版本：MyBatis 3.5.11

## 2、创建maven工程

## a>打包方式：jar

## b>引入依赖

```xml
<dependencies>
    <!-- Mybatis核心 -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.7</version>
    </dependency>

    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>

    <!-- MySQL驱动 -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>5.1.3</version>
    </dependency>
</dependencies>
```

## 3、创建MyBatis的核心配置文件
> 习惯上命名为mybatis-config.xml，这个文件名仅仅只是建议，并非强制要求。将来整合Spring
> 之后，这个配置文件可以省略，所以大家操作时可以直接复制、粘贴。
> 核心配置文件主要用于配置连接数据库的环境以及MyBatis的全局配置信息
> 核心配置文件存放的位置是src/main/resources目录下

```XML
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--设置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url"
                          value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="123456"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <mapper resource="mappers/UserMapper.xml"/>
    </mappers>
</configuration>
```

## 4、创建mapper接口
> MyBatis中的mapper接口相当于以前的dao。但是区别在于，mapper仅仅是接口，我们不需要提供实现类。

```java
public interface UserMapper {
    /**
    * 添加用户信息
    */
    int insertUser();

}
```

## 5、创建MyBatis的映射文件
相关概念： **ORM** （ **O**bject **R**elationship **M**apping）对象关系映射。
- 对象：Java的实体类对象
- 关系：关系型数据库
- 映射：二者之间的对应关系

| Java概念 | 数据库概念 |
|----------|-------------|
| 类       | 表          |
| 属性     | 字段/列     |
| 对象     | 记录/行     |

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.mybatis.mapper.UserMapper">

    <!--int addUser();-->
    <insert id="addUser">
        insert into t_user
        values (null, '张三', '123', 23, '男', '123@qq.com')
    </insert>

</mapper>
```

> 1 、映射文件的命名规则：
> 表所对应的实体类的类名+Mapper.xml
> 例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml
> 因此一个映射文件对应一个实体类，对应一张表的操作
> MyBatis映射文件用于编写SQL，访问以及操作表中的数据
> MyBatis映射文件存放的位置是src/main/resources/mappers目录下
> 
> 2 、MyBatis中可以面向接口操作数据，要保证两个一致：
> a> mapper接口的全类名和映射文件的命名空间（namespace）保持一致
> b> mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致

## 6 、通过junit测试功能

```java
//读取MyBatis的核心配置文件
InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
//创建SqlSessionFactoryBuilder对象
SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
//通过核心配置文件所对应的字节输入流创建工厂类SqlSessionFactory，生产SqlSession对象
SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
//创建SqlSession对象，此时通过SqlSession对象所操作的sql都必须手动提交或回滚事务
//SqlSession sqlSession = sqlSessionFactory.openSession();
//创建SqlSession对象，此时通过SqlSession对象所操作的sql都会自动提交
SqlSession sqlSession = sqlSessionFactory.openSession(true);
//通过代理模式创建UserMapper接口的代理实现类对象
UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
//调用UserMapper接口中的方法，就可以根据UserMapper的全类名匹配元素文件，
// 通过调用的方法名匹配映射文件中的SQL标签，并执行标签中的SQL语句
int result = userMapper.addUser();
//sqlSession.commit();
```

> SqlSession：代表**Java程序**和**数据库**之间的会话 。（HttpSession是Java程序和浏览器之间的会话）。
> SqlSessionFactory：是“生产”SqlSession的“工厂”。
> 工厂模式：如果创建某一个对象，使用的过程基本固定，那么我们就可以把创建这个对象的相关代码封装到一个“工厂类”中，以后都使用这个工厂类来“生产”我们需要的对象。

## 7 、加入log4j2日志功能

### a>加入依赖
```xml
<!--log4j2的依赖-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.19.0</version>
</dependency>

<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.19.0</version>
</dependency>
```
### b>加入log4j2的配置文件
> log4j2的配置文件名为log4j2.xml，存放的位置是src/main/resources目录下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <!-- 控制台输出，对应原始的 STDOUT appender -->
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%-5level %d{MM-dd HH:mm:ss,SSS} %msg (%F:%L) %n" charset="UTF-8"/>
        </Console>

        <!-- 文件输出 -->
        <File name="FileAppender" fileName="logs/mybatis.log">
            <PatternLayout pattern="%-5level %d{MM-dd HH:mm:ss,SSS} %msg (%F:%L) %n" charset="UTF-8"/>
        </File>

        <!-- 滚动文件输出 -->
        <RollingFile name="RollingFileAppender"
                     fileName="logs/mybatis-rolling.log"
                     filePattern="logs/mybatis-rolling-%d{yyyy-MM-dd}-%i.log.gz">
            <PatternLayout pattern="%-5level %d{MM-dd HH:mm:ss,SSS} %msg (%F:%L) %n" charset="UTF-8"/>
            <Policies>
                <TimeBasedTriggeringPolicy />
                <SizeBasedTriggeringPolicy size="10MB"/>
            </Policies>
            <DefaultRolloverStrategy max="10"/>
        </RollingFile>
    </Appenders>

    <Loggers>
        <!-- SQL 语句日志，对应原始的 java.sql logger -->
        <Logger name="java.sql" level="DEBUG" additivity="true"/>

        <!-- MyBatis 日志级别设置，对应原始的 org.apache.ibatis logger -->
        <Logger name="org.apache.ibatis" level="INFO" additivity="true"/>

        <!-- 根日志器，对应原始的 root logger -->
        <Root level="DEBUG">
            <AppenderRef ref="STDOUT"/>
            <AppenderRef ref="FileAppender"/>
            <AppenderRef ref="RollingFileAppender"/>
        </Root>
    </Loggers>
</Configuration>
```

### 日志的级别
FATAL(致命)>ERROR(错误)>WARN(警告)>INFO(信息)>DEBUG(调试)从左到右打印的内容越来越详细。

# 三、核心配置文件详解

核心配置文件中的标签必须按照固定的顺序：
properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorFactory?,plugins?,environments?,databaseIdProvider?,mappers?

数据源配置文件 `jdbc.properties` ：
```properties
jdbc.username=root
jdbc.password=123456
jdbc.url=jdbc:mysql://localhost:3306/mybatis
jdbc.driver=com.mysql.cj.jdbc.Driver
```

MyBatis 核心配置文件：
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!--数据库配置文件-->
    <properties resource="jdbc.properties"/>
    <typeAliases>
        <!--以包为单位设置默认别名（不区分大小写）-->
        <package name="com.mybatis.pojo"/>
    </typeAliases>

    <!--设置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url"
                          value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!--引入映射文件-->
    <mappers>
        <!--<mapper resource="mappers/UserMapper.xml"/>-->
        <!--以包为单位目录层级和包名必须一致-->
        <package name="com.mybatis.mapper"/>
    </mappers>
</configuration>
```

# 四、MyBatis的增删改查

### 1 、添加
```xml
<!--int addUser();-->
<insert id="addUser">
    insert into t_user
    values (null, 'nacy', '9090', 19, '女', '34@qq.com')
</insert>
```

### 2 、删除
```xml
<!--int deleteUser();-->
<delete id="deleteUser">
    delete from t_user where id = 13;
</delete>
```

### 3 、修改
```xml
<!--int updateUser();-->
<update id="updateUser">
    update t_user
    set username = '李四' where id = 13;
</update>
```

### 4 、查询一个实体类对象
```xml
<!--User getUserById(int id);-->
<select id="getUserById" resultType="User">
    select * from t_user where id = #{id};
</select>
```

### 5 、查询集合
```xml
<!--List<User> getAllUser();-->
<select id="getAllUser" resultType="User">
    select * from t_user;
</select>
```

> 注意：
> 1、查询的标签select必须设置属性resultType或resultMap，用于设置实体类和数据库表的映射关系：
> resultType：自动映射，用于属性名和表中字段名一致的情况
> resultMap：自定义映射，用于一对多或多对一或字段名和属性名不一致的情况
> 2、当查询的数据为多条时，不能使用实体类作为返回值，只能使用集合，否则会抛出异常TooManyResultsException；但是若查询的数据只有一条，可以使用实体类或集合作为返回值

# 五、MyBatis获取参数值的两种方式（重点）

MyBatis获取参数值的两种方式： \${} 和 #{}
\${}的本质就是字符串拼接，#{}的本质就是占位符赋值
\${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号；但是#{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号。

## 1 、单个字面量类型的参数
若mapper接口中的方法参数为单个的字面量类型
此时可以使用\${}和#{}以任意的名称获取参数的值，注意\${}需要手动加单引号

> 例如： `SELECT * FROM t_user WHERE username = '${username}'`

## 2 、多个字面量类型的参数（了解）
若mapper接口中的方法参数为多个时，此时MyBatis会自动将这些参数放在一个map集合中，以 arg0,arg1... 为键，以参数为值；以 param1,param2... 为键，以参数为值；因此只需要通过\${}和#{}访问map集合的键就可以获取相对应的值，注意\${}需要手动加单引号

> 例如： `SELECT * FROM t_user WHERE username = #{arg0} AND password = #{param2}`

## 3 、map集合类型的参数（了解）
若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中只需要通过\${}和#{}访问map集合的**键**就可以获取相对应的值，注意\${}需要手动加单引号

```xml
<!--User checkLoginByMap(Map<String, Object> Map);-->
<select id="checkLoginByMap" parameterType="Map" resultType="User">
    SELECT * FROM t_user WHERE username = #{username} AND password = #{password}
</select>
```

## 4 、实体类类型的参数
若mapper接口中的方法参数为实体类对象时
此时可以使用\${}和#{}，通过访问实体类对象中的**属性名**获取属性值，注意\${}需要手动加单引号

> 传递实体类中的参数获取通过 getXxx 和 setXxx 方法。

## 5 、使用@Param标识参数
可以通过@Param注解标识mapper接口中的方法参数，此时，会将这些参数放在map集合中，以@Param注解的value属性值为键，以参数为值；以param1,param2...为键，以参数为值；只需要通过\${}和#{}访问map集合的键就可以获取相对应的值，注意\${}需要手动加单引号

# 六、MyBatis的各种查询功能

## 1 、查询一个实体类对象
```java
/**
 * 根据用户ID查询用户信息
 * @param id 用户ID
 * @return 用户信息
 */
User getUserById(@Param("id") int id);
```

```xml
<!--User getUserById(int id);-->
<select id="getUserById" parameterType="int" resultType="User">
    SELECT * FROM t_user WHERE id = #{id}
</select>
```

## 2 、查询一个list集合
```java
/**
 * 查询所有用户信息
 * @return 用户列表
 */
List<User> getAllUser();
```

```xml
<!--List<User> getAllUser();-->
<select id="getAllUser" resultType="User">
    SELECT * FROM t_user
</select>
```

## 3 、查询单个数据
```java
/**
 * 查询记录总数
 * @return 总记录数
 */
Integer getCount();
```

```xml
<!--Integer getCount();-->
<select id="getCount" resultType="Integer">
    SELECT COUNT(*) FROM t_user
</select>
```

## 4 、查询一条数据为map集合
```java
/**
 * 根据用户ID查询用户信息，并将结果转换为Map
 * @param id 用户ID
 * @return 用户信息的Map，键为字段名，值为字段值
 */
Map<Integer, Object> getUsetByIdToMap(@Param("id") int id);
```

```xml
<!--Map<Integer, Object> getUsetByIdToMap(@Param("id") int id);-->
<select id="getUsetByIdToMap" parameterType="int" resultType="map">
    SELECT * FROM t_user WHERE id = #{id}
</select>
```

## 5 、查询多条数据为map集合

### 方式一：
```java
/**
 * 查询所有用户信息，并将结果转换为Map列表
 * @return 用户信息的Map列表，每个Map代表一个用户，键为字段名，值为字段值
 */
List<Map<Integer, Object>> getAllUserToMap();
```

```xml
<!--List<Map<Integer, Object>> getAllUserToMap();-->
<select id="getAllUserToMap" resultType="map">
    SELECT * FROM t_user
</select>
```

### 方式二：
```java
/**
* 查询所有用户信息为map集合
* @return
* 将表中的数据以map集合的方式查询，一条数据对应一个map；若有多条数据，就会产生多个map集合，并
且最终要以一个map的方式返回数据，此时需要通过@MapKey注解设置map集合的键，值是每条数据所对应的
map集合
*/
@MapKey("id")
Map<String, Object> getAllUserToMap();
```

```xml
<!--Map<String, Object> getAllUserToMap();-->
<select id="getAllUserToMap" resultType="map">
select * from t_user
</select>
结果：
<!--
{
1={password=123456, sex=男, id=1, age=23, username=admin},
2={password=123456, sex=男, id=2, age=23, username=张三},
3={password=123456, sex=男, id=3, age=23, username=张三}
}
-->
```

# 七、特殊SQL的执行

## 1 、模糊查询

```java
/** 测试模糊查询
* @param mohu
* @return
*/
List<User> testMohu(@Param("mohu") String mohu);
```
```xml
<!--List<User> testMohu(@Param("mohu") String mohu);-->
<select id="testMohu" resultType="User">
    <!--select * from t_user where username like '%${mohu}%'-->
    <!--select * from t_user where username like concat('%',#{mohu},'%')-->
    select * from t_user where username like "%"#{mohu}"%"
</select>
```

## 2 、批量删除

```java
/**
* 批量删除
* @param ids
* @return
*/
int deleteMore(@Param("ids") String ids);
```

```xml
<!--int deleteMore(@Param("ids") String ids);-->
<delete id="deleteMore">
    delete from t_user where id in (${ids})
</delete>
```
## 3 、动态设置表名
```java
/**
* 动态设置表名，查询所有的用户信息
* @param tableName
* @return
*/
List<User> getAllUser(@Param("tableName") String tableName);
```
```xml
<!--List<User> getAllUser(@Param("tableName") String tableName);-->
<select id="getAllUser" resultType="User">
    select * from ${tableName}
</select>
```

## 4 、添加功能获取自增的主键
t_clazz(clazz_id,clazz_name)
t_student(student_id,student_name,clazz_id)
1 、添加班级信息
2 、获取新添加的班级的id
3 、为班级分配学生，即将某学的班级id修改为新添加的班级的id

```java
/**
* 添加用户信息
* @param user
* @return
* useGeneratedKeys：设置使用自增的主键
* keyProperty：因为增删改有统一的返回值是受影响的行数，因此只能将获取的自增的主键放在传输的参
数user对象的某个属性中
*/
int insertUser(User user);
```
```xml
<!--int insertUser(User user);-->
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
    insert into t_user values(null,#{username},#{password},#{age},#{sex})
</insert>
```

# 八、自定义映射resultMap

## 1 、resultMap处理字段和属性的映射关系

若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射

```xml
<!--
resultMap：设置自定义映射
属性：
id：表示自定义映射的唯一标识
type：查询的数据要映射的实体类的类型
子标签：
id：设置主键的映射关系
result：设置普通字段的映射关系
association：设置多对一的映射关系
collection：设置一对多的映射关系
属性：
property：设置映射关系中实体类中的属性名
column：设置映射关系中表中的字段名
-->
<resultMap id="empResultMap" type="Emp">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <result property="email" column="email"></result>
</resultMap>

<!--List<Emp> getAllEmp();-->
<select id="getAllEmp" resultMap="empResultMap">
    SELECT * FROM t_emp
</select>
```

> 若字段名和实体类中的属性名不一致，但是字段名符合数据库的规则（使用_），实体类中的属性名符合Java的规则（使用驼峰）此时也可通过以下两种方式处理字段名和实体类中的属性的映射关系：
> a>可以通过为字段起别名的方式，保证和实体类中的属性名保持一致
> b>可以在MyBatis的核心配置文件中设置一个全局配置信息mapUnderscoreToCamelCase，可以在查询表中数据时，自动将_类型的字段名转换为驼峰
> 例如：字段名user_name，设置了mapUnderscoreToCamelCase，此时字段名就会转换为 userName 。
> 
> MyBatis 核心配置文件增加如下配置：
> ```xml
> <!--设置mybatis全局配置-->
> <settings>
>     <setting name="mapUnderscoreToCamelCase" value="true"/>
> </settings>
> ```

## 2 、多对一映射处理

查询员工信息以及员工所对应的部门信息

### a>级联方式处理映射关系（了解）
```xml
<resultMap id="empAndDeptResultOne" type="Emp">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <result property="email" column="email"></result>
    <result property="dept.did" column="did"></result>
    <result property="dept.deptName" column="dept_name"></result>
</resultMap>

<!--List<Emp> getAllEmpAndDept();-->
<select id="getAllEmpAndDept" resultMap="empAndDeptResultOne">
    select * from t_emp left join t_dept on t_emp.did = t_dept.did;
</select>
```

### b>使用association处理映射关系
```xml
<resultMap id="empAndDeptResultTwo" type="Emp">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <result property="email" column="email"></result>
    <association property="dept" javaType="Dept">
        <id property="did" column="did"></id>
        <result property="deptName" column="dept_name"></result>
    </association>
</resultMap>

<!--List<Emp> getAllEmpAndDept();-->
<select id="getAllEmpAndDept" resultMap="empAndDeptResultTwo">
    select * from t_emp left join t_dept on t_emp.did = t_dept.did;
</select>
```

### c>分步查询

#### 1 ）查询员工信息
```java
/**
* 分步查询第一步，获取员工信息
* @param eid
* @return 员工信息
*/
Emp getEmpAndDeptByStepOne(@Param("eid") int eid);
```
```xml
<resultMap id="empAndDeptByStepResultMap" type="Emp">
    <id property="eid" column="eid"></id>
    <result property="empName" column="emp_name"></result>
    <result property="age" column="age"></result>
    <result property="gender" column="gender"></result>
    <result property="email" column="email"></result>
    <!--
        select：查询某个属性的值的sql的标识（mapper接口的全类名.方法名）
        column：设置分布查询的主键
        fetchType：当开启了全局的延迟加载之后，可通过此属性手动控制延迟加载
    -->
    <association property="dept"
                 select="com.mybatis.mapper.DeptMapper.getEmpAndDeptByStepTwo" 
                 column="did"
                 fetchType="lazy">
    </association>
</resultMap>

<!--Emp getEmpAndDeptByStepOne(@Param("eid") int eid);-->
<select id="getEmpAndDeptByStepOne" resultMap="empAndDeptByStepResultMap">
    select * from t_emp where eid = #{eid}
</select>
```

#### 2 ）根据员工所对应的部门id查询部门信息
```java
/**
* 分步查询第二步，获取部门信息
* @param did
* @return 部门信息
*/
Dept getEmpAndDeptByStepTwo(@Param("did") int did);
```
```xml
<!--Dept getEmpAndDeptByStepTwo(@Param("did") int did);-->
<select id="getEmpAndDeptByStepTwo" resultType="Dept">
    select * from t_dept where did = #{did}
</select>
```

## 3 、一对多映射处理

### a>collection
```java
/*
部门类：
public class Dept {
    private Integer did;
    private String deptName;
    private List<Emp> empList;
    ...
}
*/

/**
 * 获取部门和部门中的员工信息
 * @param did
 * @return
 */
Dept getDeptAndEmp(@Param("did") int did);
```
```xml
<resultMap id="deptAndEmpResultMap" type="Dept">
    <id property="did" column="did"></id>
    <result property="deptName" column="dept_name"></result>
    <!--
        collection：处理一对多关系
        ofType：表示该属性所对应集合中存储数据的类型
    -->
    <collection property="empList" ofType="Emp">
        <id property="eid" column="eid"></id>
        <result property="empName" column="emp_name"></result>
        <result property="age" column="age"></result>
        <result property="gender" column="gender"></result>
        <result property="email" column="email"></result>
    </collection>
</resultMap>
<!--Dept getDeptAndEmp(@Param("did") int did);-->
<select id="getDeptAndEmp" resultMap="deptAndEmpResultMap">
    select * from t_dept left join t_emp on t_dept.did = t_emp.did where t_dept.did = #{did}
</select>
```

### b>分步查询

#### 1 ）查询部门信息
```java
/**
 * 获取部门信息和员工信息
 * 分布查询第一步
 */
Dept getDeptAnEmpStepOne(@Param("did") int did);
```
```xml
<resultMap id="deptAnEmpStepOneResultMap" type="Dept">
    <id property="did" column="did"></id>
    <result property="deptName" column="dept_name"></result>
    <!--
        select：查询某个属性的值的sql的标识（mapper接口的全类名.方法名）
        column：设置分布查询的主键
    -->
    <collection property="empList"
                select="com.mybatis.mapper.EmpMapper.getDeptAnEmpStepTwo"
                column="did">
    </collection>
</resultMap>
<!--Dept getDeptAnEmpStepOne(@Param("did") int did);-->
<select id="getDeptAnEmpStepOne" resultMap="deptAnEmpStepOneResultMap">
    select * from t_dept where did = #{did}
</select>
```

#### 2 ）根据部门id查询部门中的所有员工
```java
/**
 * 获取部门信息和员工信息
 * 分布查询第二步，查询部门为 did 的员工信息
 */
List<Emp> getDeptAnEmpStepTwo(@Param("did") int did);
```
```xml
<!--List<Emp> getDeptAnEmpStepTwo(@Param("did") int did);-->
<select id="getDeptAnEmpStepTwo" resultType="Emp">
    select * from t_emp where did = #{did}
</select>
```

> 分步查询的优点：可以实现延迟加载，但是必须在核心配置文件中设置全局配置信息：
> lazyLoadingEnabled：延迟加载的全局开关。当开启时，所有关联对象都会延迟加载
> aggressiveLazyLoading：当开启时，任何方法的调用都会加载该对象的所有属性。 否则，每个属性会按需加载此时就可以实现按需加载，获取的数据是什么，就只会执行相应的sql。
> 可通过association和collection中的fetchType属性设置当前的分步查询是否使用延迟加载，fetchType="lazy(延迟加载)|eager(立即加载)"

# 九、动态SQL
Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。

由于编写 SQL 的文件为 `.xml` 格式
| 转义字符 | 名称         |
|----------|--------------|
| `&amp;`  | 和号(ampersand) |
| `&lt;`   | 小于号(less than) |
| `&gt;`   | 大于号(greater than) |
| `&quot;` | 双引号(quotation mark) |
| `&apos;` | 单引号(apostrophe) |

## 1 、if
if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行

```xml
<!--List<Emp> getEmpListByMoreTJ(Emp emp);-->
<select id="getEmpListByMoreTJ" resultType="Emp">
    select * from t_emp where 1=1
    <if test="empName != '' and empName != null">
        and emp_name = #{empName}
    </if>
    <if test="age != '' and age != null">
        and age = #{age}
    </if>
    <if test="gender != '' and gender != null">
        and gender = #{gender}
    </if>
</select>
```

## 2 、where
where和if一般结合使用：
a>若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字
b>若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的
and去掉。

```xml
<!--List<Emp> getEmpByCondition(Emp emp);-->
<select id="getEmpByCondition" resultType="Emp">
    select * from t_emp
    <where>
        <!--注意：test中的值不需要使用 #{} 或 ${}-->
        <if test="empName != null and empName != ''">
            and emp_name = #{empName}
        </if>
        <if test="age != null and age != ''">
            and age = #{age}
        </if>
        <if test="gender != null and gender != ''">
            and gender = #{gender}
        </if>
        <if test="email != null and email != ''">
            and email = #{email}
        </if>
    </where>
</select>
```

> 注意：`where` 标签不能去掉条件最后多余的and。相似标签 `set` 用于 update 语句，`set` 用于去除后缀多余的 ',' 。
> ```xml
> <update id="updateEmp">
>     UPDATE t_emp
>     <set>
>         <if test="empName != null and empName != ''">
>             emp_name = #{empName},
>         </if>
>         <if test="age != null">
>             age = #{age},
>         </if>
>         <if test="empSalary != null">
>             emp_salary = #{empSalary},
>         </if>
>     </set>
>     WHERE id = #{id}
> </update>
> ```

## 3 、trim（了解）
trim用于去掉或添加标签中的内容
常用属性：
prefix：在trim标签中的内容的前面添加某些内容
prefixOverrides：在trim标签中的内容的前面去掉某些内容
suffix：在trim标签中的内容的后面添加某些内容
suffixOverrides：在trim标签中的内容的后面去掉某些内容

```xml
<select id="getEmpListByMoreTJ" resultType="Emp">
    select * from t_emp
    <trim prefix="where" suffixOverrides="and">
        <if test="ename != '' and ename != null">
            ename = #{ename} and
        </if>
        <if test="age != '' and age != null">
            age = #{age} and
        </if>
        <if test="sex != '' and sex != null">
            sex = #{sex}
        </if>
    </trim>
</select>
```

## 4 、choose...when...otherwise
`choose...when...otherwise` 相当于 `if...else if...else`

```xml
<!--List<Emp> getEmpByConditionChoose(Emp emp);-->
<select id="getEmpByConditionChoose" resultType="Emp">
    select * from t_emp
    <where>
        <choose>
            <when test="empName != null and empName != ''">
                and emp_name = #{empName}
            </when>
            <when test="age != null and age != ''">
                and age = #{age}
            </when>
            <when test="gender != null and gender != ''">
                and gender = #{gender}
            </when>
            <when test="email != null and email != ''">
                and email = #{email}
            </when>
            <otherwise>
                eid = 1
            </otherwise>
        </choose>
    </where>
</select>
```

## 5 、foreach
属性：
collection：设置要循环的数组或集合
item：表示集合或数组中的每一个数据
separator：设置循环体之间的分隔符
open：设置foreach标签中的循环所有内容的开始符
close：设置foreach标签中的循环所有内容的结束符

```xml
<!--int deleteEmpByIds(@Param("ids")List<Integer> ids);-->
<delete id="deleteEmpByIds">
    delete from t_emp
    <where>
        <if test="ids != null and ids.size() > 0">
            eid in
            <foreach item="id" collection="ids" open="(" separator="," close=")">
                #{id}
            </foreach>
        </if>
        <if test="ids == null or ids.size() == 0">
            1 = 2
        </if>
    </where>
</delete>
```

## 6 、SQL片段
sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入
```xml
<sql id="empColumns"> eid, emp_name empeid, emp_name, age, gender, email </sql>
<!--List<Emp> getAllEmp();-->
<select id="getAllEmp" resultType="Emp">
    select <include refid="empColumns"></include> from t_emp
</select>
```

# 十、MyBatis的缓存

## 1 、MyBatis的一级缓存
一级缓存是SqlSession级别的，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问。
使一级缓存失效的四种情况：
1) 不同的SqlSession对应不同的一级缓存
2) 同一个SqlSession但是查询条件不同
3) 同一个SqlSession两次查询期间执行了任何一次增删改操作
4) 同一个SqlSession两次查询期间手动清空了缓存

## 2 、MyBatis的二级缓存

二级缓存是SqlSessionFactory级别，通过同一个SqlSessionFactory创建的SqlSession查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取
二级缓存开启的条件：
a>在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置
b>在映射文件中设置标签 <cache />
c>二级缓存必须在SqlSession关闭或提交之后有效
d>查询的数据所转换的实体类类型**必须实现序列化**的接口
使二级缓存失效的情况：
两次查询之间执行了任意的增删改，会使一级和二级缓存**同时失效**。

## 3 、二级缓存的相关配置

在mapper配置文件中添加的cache标签可以设置一些属性：
- eviction属性：缓存回收策略
LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。
FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。
SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。
WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。
    > 默认的是 LRU。

- flushInterval属性：刷新间隔，单位毫秒
默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新
- size属性：引用数目，正整数
代表缓存最多可以存储多少个对象，太大容易导致内存溢出
- readOnly属性：只读，true/false
true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。
false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此默认是false。

## 4 、MyBatis缓存查询的顺序
先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。
如果二级缓存没有命中，再查询一级缓存。
如果一级缓存也没有命中，则查询数据库。
> SqlSession关闭之后，一级缓存中的数据会写入二级缓存。

## 5 、整合第三方缓存EHCache（了解）

### a>添加依赖
```xml
<!-- Mybatis EHCache整合包 -->
<dependency>
    <groupId>org.mybatis.caches</groupId>
    <artifactId>mybatis-ehcache</artifactId>
    <version>1.2.1</version>
</dependency>
<!-- slf4j日志门面的一个具体实现 -->
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

### b>各jar包功能
| jar包名称          | 作用                                   |
|--------------------|----------------------------------------|
| mybatis-ehcache    | Mybatis和EHCache的整合包               |
| ehcache            | EHCache核心包                          |
| slf4j-api          | SLF4J日志门面包                        |
| logback-classic    | 支持SLF4J门面接口的一个具体实现        |

### c>创建EHCache的配置文件ehcache.xml
```xml
<?xml version="1.0" encoding="utf-8" ?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="../config/ehcache.xsd">
    <!-- 磁盘保存路径 -->
    <diskStore path="D:\workspace\forJava\ehcache"/>
    <defaultCache
            maxElementsInMemory="1000"
            maxElementsOnDisk="10000000"
            eternal="false"
            overflowToDisk="true"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            diskExpiryThreadIntervalSeconds="120"
            memoryStoreEvictionPolicy="LRU">
    </defaultCache>
</ehcache>
```

### d>设置二级缓存的类型
```xml
<cache type="org.mybatis.caches.ehcache.EhcacheCache"/>
```

### e>加入logback日志
存在SLF4J时，作为简易日志的log4j将失效，此时我们需要借助SLF4J的具体实现logback来打印日志。
创建logback的配置文件logback.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">
    <!-- 指定日志输出的位置 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- 日志输出的格式 -->
            <!-- 按照顺序分别是：时间、日志级别、线程名称、打印日志的类、日志主体内容、换行 -->
            <pattern>[%d{HH:mm:ss.SSS}][%-5level][%thread][%logger][%msg]%n</pattern>
        </encoder>
    </appender>

    <!-- 设置全局日志级别。日志级别按顺序分别是：DEBUG、INFO、WARN、ERROR -->
    <!-- 指定任何一个日志级别都只打印当前级别和后面级别的日志。-->
    <root level="DEBUG">
        <!-- 指定打印日志的appender，这里通过“STDOUT”引用了前面配置的appender -->
        <appender-ref ref="STDOUT"/>
    </root>

    <!-- 根据特殊需求指定局部日志级别 -->
    <logger name="com.mybatis.mapper" level="DEBUG"/>
</configuration>
```

### f>EHCache配置文件说明
| 属性名                           | 是否必须 | 作用                                                                 |
|----------------------------------|----------|----------------------------------------------------------------------|
| maxElementsInMemory             | 是       | 在内存中缓存的element的最大数目                                      |
| maxElementsOnDisk               | 是       | 在磁盘上缓存的element的最大数目，若是0表示无穷大                     |
| eternal                         | 是       | 设定缓存的elements是否永远不过期。如果为true，则缓存的数据始终有效；如果为false，则根据timeToIdleSeconds、timeToLiveSeconds判断 |
| overflowToDisk                  | 是       | 设定当内存缓存溢出时是否将过期的element缓存到磁盘上                  |
| timeToIdleSeconds               | 否       | 缓存数据前后两次访问的时间超过此值时删除，默认0（闲置时间无穷大）    |
| timeToLiveSeconds               | 否       | 缓存element的有效生命期，默认0（存活时间无穷大）                     |
| diskSpoolBufferSizeMB           | 否       | DiskStore(磁盘缓存)的缓存区大小，默认30MB                            |
| diskPersistent                  | 否       | VM重启时是否启用磁盘保存EhCache中的数据，默认false                   |
| diskExpiryThreadIntervalSeconds | 否       | 磁盘缓存的清理线程运行间隔，默认120秒                                |
| memoryStoreEvictionPolicy       | 否       | 内存缓存达到最大时的移除策略，默认LRU（可选LFU、FIFO）               |

# 十一、MyBatis 代码生成

mybatis-generator-maven-plugin 是一个 Maven 插件，用于生成 MyBatis 的代码（如 Mapper 接口、Mapper XML 文件等），官方文档地址: https://mybatis.org/generator/ 。它可以根据数据库表自动生成相应的 Java 实体类、Mapper 接口和 XML 映射文件，大大减少了手动编写这些重复且机械化的代码的工作量。

## 1、添加依赖和插件
```xml
<!-- Mybatis -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>${spring-boot.version}</version>
</dependency>

 <build>
    <!-- 统一插件管理 -->
    <pluginManagement>
        <plugins>
            // 省略...

            <!-- 代码生成器 -->
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>${mybatis-generator-maven-plugin.version}</version>
                <configuration>
                    <!-- 允许移动生成的文件 -->
                    <verbose>true</verbose>
                    <!-- 允许覆盖生成的文件 -->
                    <overwrite>true</overwrite>
                </configuration>
                <!-- 此插件需要连接数据库，所以需要依赖 MySQL 驱动 -->
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql-connector-java.version}</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </pluginManagement>

</build>
```

子模块引入插件：

```xml
<build>
    <plugins>
        <!-- 代码生成器 -->
        <plugin>
            <groupId>org.mybatis.generator</groupId>
            <artifactId>mybatis-generator-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

## 2、创建代码生成的配置文件

> 文件名必须是：generatorConfig.xml，文件路径在 resources 包下。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <!--
        targetRuntime: 执行生成的逆向工程的版本
        MyBatis3Simple: 生成基本的CRUD（清新简洁版）
        MyBatis3: 生成带条件的CRUD（奢华尊享版）
    -->
    <context id="mysqlTables" targetRuntime="MyBatis3" defaultModelType="flat">

        <!-- 自动检查关键字，为关键字增加反引号，如：`type` -->
        <property name="autoDelimitKeywords" value="true"/>
        <property name="beginningDelimiter" value="`"/>
        <property name="endingDelimiter" value="`"/>
        <!-- 指定生成的 Java 文件编码 -->
        <property name="javaFileEncoding" value="UTF-8"/>

        <!-- 对生成的注释进行控制 -->
        <commentGenerator>
            <!-- 由于此插件生成的注释不太美观，这里设置不生成任何注释 -->
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>

        <!-- 数据库链接 -->
        <jdbcConnection driverClass="com.mysql.cj.jdbc.Driver"
                        connectionURL="jdbc:mysql://127.0.0.1:3306/xiaohashu"
                        userId="root"
                        password="123456">
            <!-- 解决多个重名的表生成表结构不一致问题 -->
            <property name="nullCatalogMeansCurrent" value="true"/>
        </jdbcConnection>

        <!-- 不强制将所有的数值类型映射为 Java 的 BigDecimal 类型 -->
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>

        <!-- DO 实体类存放路径 -->
        <javaModelGenerator targetPackage="com.quanxiaoha.xiaohashu.auth.domain.dataobject"
                            targetProject="src/main/java"/>

        <!-- Mapper xml 文件存放路径-->
        <sqlMapGenerator targetPackage="mapper"
                         targetProject="src/main/resources"/>

        <!-- Mapper 接口存放路径 -->
        <javaClientGenerator type="XMLMAPPER" targetPackage="com.quanxiaoha.xiaohashu.auth.domain.mapper"
                             targetProject="src/main/java"/>

        <!-- 需要生成的表-实体类 -->
        <table tableName="t_user" domainObjectName="UserDO"
               enableCountByExample="false"
               enableUpdateByExample="false"
               enableDeleteByExample="false"
               enableSelectByExample="false"
               selectByExampleQueryId="false"/>
    </context>
</generatorConfiguration>
```

### 3、生成 DO 实体类、Mapper 接口、XML 映射文件
![](./pictures/mybatis/generator.png)

# 十二、分页插件（了解）

> 原始方式，最新方式参考 [SpringBoot 分页插件](./SSM-MyBatis.md#分页插件)

## ~~1 、分页插件使用步骤~~

### ~~a>添加依赖~~
```xml
<!-- 分页插件依赖 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.2</version>
</dependency>
```

### ~~b>配置分页插件~~
在MyBatis的核心配置文件中配置插件
```xml
<plugins>
    <!--设置分页插件-->
    <plugin interceptor="com.github.pagehelper.PageInterceptor"></plugin>
</plugins>
```

## ~~2 、分页插件的使用~~

#### a>在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能
> pageNum：当前页的页码 // 页面从 1 开始
> pageSize：每页显示的条数

```java
// 设置分页参数，当前页为1，每页3条数据
Page<Object> page = PageHelper.startPage(1, 3);
List<Emp> emps = empMapper.selectByExample(null);
emps.forEach(System.out::println);
```

#### b>在查询获取list集合之后，使用PageInfo<T> pageInfo = new PageInfo<>(List<T> list, int navigatePages)获取分页相关数据
> list：分页之后的数据
> navigatePages：导航分页的页码数

```java
PageHelper.startPage(1, 3);// 设置分页参数，当前页为1，每页3条数据
List<Emp> emps = empMapper.selectByExample(null);
// 获取分页相关数据
PageInfo<Emp> pageInfo = new PageInfo<>(emps, 5); // 5表示连续显示的页码数，即，导航前后覆盖的页码数
```

#### c>分页相关数据
> PageInfo{
> pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, >s=8,
> list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, >l=30,
> pages=8, reasonable=false, pageSizeZero=false},
> prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, >reviousPage=true,
> hasNextPage=false, navigatePages=5, navigateFirstPage4, >gateLastPage8,
> navigatepageNums=[4, 5, 6, 7, 8]
> }
> 常用数据：
> pageNum：当前页的页码
> pageSize：每页显示的条数
> size：当前页显示的真实条数
> total：总记录数
> pages：总页数
> prePage：上一页的页码
> nextPage：下一页的页码
> isFirstPage/isLastPage：是否为第一页/最后一页
> hasPreviousPage/hasNextPage：是否存在上一页/下一页
> navigatePages：导航分页的页码数
> navigatepageNums：导航分页的页码，[1,2,3,4,5]


