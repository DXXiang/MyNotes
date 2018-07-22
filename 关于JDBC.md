# JDBC
1. 导入Connect-tool.jar
2. 加载驱动类
> Class.forName("类名");

#配置参数
* driverClassName:**com.mysql.jdbc.Driver**
* url:**jdbc:mysql://localhost:3306/数据库名**
* username:
password:

# 概述
* 所有的java.sql.Driver实现类都提供了static 块，把自己注册到DriverManager中
* JDBC4.0之后，每个驱动jar包中都在META-INF/services目录下提供了一个名为java.sql.Driver的文件，文件内容就是com.mysql.jdbc.Driver字符串，所以可以不用显式加载驱动类，为了兼容性仍然建议写
# JDBC使用
##连接与关闭
* 导入**mysql-connector-java-8.0.11.jar**，然后输入如下代码

        //加载驱动类
        Class.forName("com.mysql.jdbc.Driver");
        //得到连接对象
        Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb",username,password);
    
* **关闭连接,十分重要!!!**

        rs.close();
        pstmt.close();
        con.close();

## 数据库操作
* **使用PreparedStatement的模板**

* **模板可以重复使用，效率得到显著提高**

* **PreparedStatement可以避免SQL注入**

  

### 增、删、改

```java
        String sql = "INSERT INTO users VALUES(null,?,?,?)";//PrepareStatement模板

        PreparedStatement pstmt = con.prepareStatement(sql);

        pstmt.setString(1,user.getName());

        pstmt.setInt(2,user.getAge());

        pstmt.setString(3,user.getSex());

        int result = pstmt.executeUpdate();

```

 * result=1，插入成功

 * result=N,N表示更改的条目数
### 查询

        String sql = "SELECT * FROM users WHERE ID=?";//模板
        PreparedStatement pstmt = con.prepareStatement(sql);
        pstmt.setInt(1,tid);
    
        ResultSet rs = pstmt.executeQuery();
        User target =null;
        while (rs.next()){
            int id = rs.getInt(1);
            String name = rs.getString(2);
            int age = rs.getInt(3);
            String sex = rs.getString(4);
            target = new User(id,name,age,sex);
        }
        System.out.println(target);

  * 查询成功
## 规范化
* **在try外给出引用**
* **在try内实例化**
* **在finally中关闭连接**
```java
if(rs!=null)rs.close();

if(pstmt!=null)pstmt.close();

if(con!=null)con.close();

```

## 时间转换

1. java.sql.Date->java.util.Date
    后者是前者的父类，故自然转换

2. java.util.Date -> java.sql.Date

   ```java
   java.util.Date date = new  java.util.Date();
   
   long l = date.getTime();
   
   java.sql.Date sqlDate = new java.sql.Date(l);
   
   ##Blob
   
   ```
1. 在my.ini中添加如下配置
    max_allowed_packet=????
     代表单个文件最大容量，单位为B

2. 先把文件转换为byte[]，然后创建Blob

    ```java
    Byte[] b = IOUtils.toByteArray(new FileInuputStream("/home/XXX.jpg"));
    
    Blob blob = new Blob(b);
    
    pstmt.setBlob(3,blob);
    
    ```
## 批处理
在JDBC	url之后添加参数

?rewriteBathedStatements=true;

```java
//创建语句对象

ps =con.prepareStatement(sql);

for(int i=0;i<=10;i++){
    //为字段赋值
    ps.setInt(1, i+1);
    ps.setString(2, "name"+i);
    ps.setInt(3, 10+i);

    //添加进批
    ps.addBatch();
}

//将批中剩余的语句执行完毕
ps.executeBatch();
ps.clearBatch();
```
# 事务

* JDBC处理事务的代码格式

```java
try{
    con.setAutoCommit(false);//开启事务项
    ...
    ...
    con.commit();//在try的最后提交
}catch(Exception e){
    con.rollback();//回滚
    con.close();//关闭连接
}
```

* 同一事务中，所有的操作都使用**同一个 Connection**



# JdbcUtils(获得数据库连接) V1.0

* src下创建dbConfig.properties配置文件

   ```properties
   driverClassName=com.mysql.jdbc.Driver
   url=jdbc:mysql://localhost:3306/mydb
   username=root
   password=dxx19971221
   ```

```java
package com.dxx.projec1.dao;

import java.io.IOException;
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.util.Properties;

/**
 * v1.0
 */
public class JdbcUtils {

    private static Properties props;
    //只在加载JdbcUtils类时执行一次
    static {
        //加载配置文件
        InputStream in = JdbcUtils.class.getClassLoader()
                .getResourceAsStream("dbConfig.properties");
        props = new Properties();
        try {
            props.load(in);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        //加载驱动类
        try {
            Class.forName(props.getProperty("driverClassName"));
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
    }


    public static Connection getConnection() throws SQLException {
        //得到连接对象
         return DriverManager.getConnection(props.getProperty("url"),
                props.getProperty("username"),props.getProperty("password"));
    }

}

```



# DBCP连接池

## 池参数

_所有池参数都有默认值_

* 初始大小
* 最小空闲连接数
* 最大空闲连接数
* 增量
* 最大连接数
* 最大等待时间

## JAR包

commons-dbcp

commons-pool

mysql-connector-java

## 接口

连接池必须实现javax.sql.DataSouce接口(基本都实现了)

**连接池返回的Connection对象的close()方法与众不同，不是关闭，而是把连接归还给池**

## 步骤

1. 创建连接池对象
2. 配置4大参数
3. 配置池参数
4. 得到连接对象



# c3p0连接池
* **建议使用c3p0而不是DBCP**
* **导入c3p0,mchange-commons-java,mysql-connector-java这3个jar包**

## 初步使用

* 简单使用

```java
		ComboPooledDataSource dataSource = new ComboPooledDataSource();

        //参数配置
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUser("root");
        dataSource.setPassword("dxx19971221");

        //池配置
        dataSource.setAcquireIncrement(5);
        dataSource.setInitialPoolSize(20);
        dataSource.setMinPoolSize(2);
        dataSource.setMaxPoolSize(50);
		
		//获取连接对象
        Connection con = dataSource.getConnection();
        System.out.println(con.getClass().getName());
        con.close();
```



## 通过配置文件使用

### 要求

* 文件名称：必须叫**c3p0-config.xml**
* 文件位置：必须在**src下**

### 配置示例

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<c3p0-config>
    <!--默认配置信息-->
    <default-config>
        <!--连接4大参数配置-->
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mydb</property>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password">dxx19971221</property>
        <!--池配置-->
        <property name="acquireIncrement">3</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">2</property>
        <property name="maxPoolSize">20</property>
    </default-config>
    <!--可指定的配置信息-->
    <named-config name="oracle-config">
        <!--连接4大参数配置-->
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mydb</property>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password">dxx19971221</property>
        <!--池配置-->
        <property name="acquireIncrement">3</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">2</property>
        <property name="maxPoolSize">20</property>
    </named-config>
</c3p0-config>
```

### 使用示例

* 默认配置和指定配置

```java
		//创建对象时会加载配置文件，若之后再用代码更改则会覆盖配置
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
        Connection con = dataSource.getConnection();
        System.out.println(con);
        con.close();
```

```java
		//指定配置
		ComboPooledDataSource dataSource = new ComboPooledDataSource("oracle-config");
```

## 通过JNDI使用

### 配置

* 位于tomcat下Catalina/localhost/下，创建与项目名相同的xml文件,注意文件的编码
* 基本配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Context>
    <!--name:指定资源名称
    factory:资源由谁创建
    type:资源的类型
	-->
    <Resource name="jdbc/dataSource"
              factory="org.apache.naming.factory.BeanFactory"
              type="com.mchange.v2.c3p0.ComboPooledDataSource"
              />
</Context>
```

* 示例配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<Context>
    <!--name:指定资源名称
    factory:资源由谁创建
    type:资源的类型
	-->
    <Resource name="jdbc/dataSource"
              factory="org.apache.naming.factory.BeanFactory"
              type="com.mchange.v2.c3p0.ComboPooledDataSource"
              jdbcUrl="jdbc:mysql://localhost:3306/mydb"
              driverClass="com.mysql.jdbc.Driver"
              user="root"
              password="dxx19971221"
              acquireIncrement="5"
              initialPoolSize="10"
              />
</Context>
```

* 获取资源

  ```java
  //这些是tomcat手册中的内容，是固定的
  Context initCtx = new InitialContext();
  Context envCtx = (Context) initCtx.lookup("java:comp/env");
  MyBean bean = (MyBean) envCtx.lookup("bean/MyBeanFactory");
  
  writer.println("foo = "+bean.getFoo() + ",bar = " + bean.getBar)
  ```

### 使用

* 导入c3p0所需jar包
* 示例代码

```java
//这些是tomcat手册中的内容，是固定的
Context initCtx = new InitialContext();
Context envCtx = (Context) initCtx.lookup("java:comp/env");
DataSource dataSource = (DataSource) envCtx.lookup("jdbc/dataSource");//根据xml中的配置
Connection con = dataSource.getConnection();

System.out.println(con);

con.close();

```

# JdbcUtils(获得数据库连接)	V2.0

* **需要提供c3p0的2个jar包和mysql-connect-java**
* **还需要提供c3p0-config.xml**
* c3p0-config.xml如下

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<c3p0-config>
    <default-config>
        <!--连接4大参数配置-->
        <property name="jdbcUrl">jdbc:mysql://localhost:3306/mydb</property>
        <property name="driverClass">com.mysql.jdbc.Driver</property>
        <property name="user">root</property>
        <property name="password">dxx19971221</property>
        <!--池配置-->
        <property name="acquireIncrement">3</property>
        <property name="initialPoolSize">10</property>
        <property name="minPoolSize">2</property>
        <property name="maxPoolSize">20</property>
    </default-config>
</c3p0-config>
```

* JdbcUtils.java

  ```java
  package com.dxx.jdbc;
  
  import com.mchange.v2.c3p0.ComboPooledDataSource;
  
  import javax.sql.DataSource;
  import java.sql.Connection;
  import java.sql.SQLException;
  
  /**
   * 需要提供c3p0的2个jar包和mysql-connect-java
   * 还需要提供c3p0-config.xml
   * V2.0
   */
  public class JdbcUtils {
      //使用默认配置，要求必须给出配置文件
      private static ComboPooledDataSource dataSource = new ComboPooledDataSource();
  
      /**
       * 使用连接池返回连接对象
       * Connection@return
       * getConnection@throws SQLException
       */
      public static Connection getConnection() throws SQLException {
          return dataSource.getConnection();
      }
  
      /**
       * 返回连接池对象
       * DataSource@return
       */
      public static DataSource getDataSouce(){
          return dataSource;
      }
  
  }
  
  ```

# DBUtils(commons)

* 十分好用的数据库数据处理工具包
* 先（通过jdbcUtils)获得连接池

## 示例

* 增示例

  ```java
  QueryRunner qr = new QueryRunner(JdbcUtils.getDataSouce());
  String sql = "insert into users values(null,?,?,?)";
  Object[] params = {"大黑",36,"male"};
  qr.update(sql,params);
  ```

* 查询示例

  ```java
  		QueryRunner qr = new QueryRunner(JdbcUtils.getDataSouce());
          String sql = "select * from users where id=?";
          Object[] params = {4};
  //      ResultSetHandler<User> rsh = new ResultSetHandler<User>() {
  //          @Override
  //          public User handle(ResultSet resultSet) throws SQLException {
  //              if(!resultSet.next())return null;
  //              User user = new User();
  //              user.setId(resultSet.getInt("id"));
  //              user.setUsername(resultSet.getString("name"));
  //              user.setAge(resultSet.getInt("age"));
  //              user.setSex(resultSet.getString("sex"));
  //              return user;
  //            }
  //        };
          /*
           *  执行query方法，需要给出结果集处理器，即ResultSetHandler的实现类对象
           *  我们给的是BeanHandler，它实现了即ResultSetHandler
           *  它需要一个类型，然后它会把rs中的数据封装到指定类型的JavaBean对象中，然后返回JavaBeans
           */
          User user = qr.query(sql,new BeanHandler<User>(User.class),params);
  ```

## 核心类和方法

### QueryRunner

* update方法

  ```java
  int update(String sql,Object... params)
  ```

  可执行增、删、改语句

  ```java
  int update(Connection con,String sql,Object... params)
  ```

  需要调用者提供Connection，支持事务

* query方法

  ```java
  T query(String sql,ResultSetHandler rsh,Object... params)
  ```

  可执行查询。它会先得到ResultSet，然后调用ResultSetHandler的handle()方法把ResultSet转换成需要的类型

  ```java
  T query(Connection con,String sql,ResultSetHandler rsh,Object... params)
  ```

  需要调用者提供Connection，支持事务

### ResultSetHandler接口的实现类

* BeanHandler(单行)-->构造器需要一个Class类型的参数，用来把一行结果转换成指定类型的JavaBean对象

* BeanlListHandler(多行)-->构造器需要一个Class类型的参数，用来把多行结果集转换成List对象

* MapHandler(单行)--> 把一行结果集转换为Map对象

  {sid:1001,sname:XXX,age:99,sex:female}

* MapListHandler-->把多行结果转换为List<Map\>

* ScalarHandler(单行单列)-->通常用于聚集函数查询语句，结果集是单行单列，返回Object