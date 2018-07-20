# JDBC
1. 导入Connect-tool.jar
2. 加载驱动类
> Class.forName("类名");
3. 

#配置参数
* driverClassName:**com.mysql.jdbc.Driver**
* url:**jdbc:mysql://localhost:3306/数据库名**
* username:**root**
password:

# JDBC使用
##连接
1. 导入**mysql-connector-java-8.0.11.jar**，然后输入如下代码

        //加载驱动类
        Class.forName("com.mysql.jdbc.Driver");
        //得到连接对象
        Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/mydb",username,password);

## 操作