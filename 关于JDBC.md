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
###增、删、改
        String sql = "INSERT INTO users VALUES(null,?,?,?)";//PrepareStatement模板
        PreparedStatement pstmt = con.prepareStatement(sql);
        pstmt.setString(1,user.getName());
        pstmt.setInt(2,user.getAge());
        pstmt.setString(3,user.getSex());
        int result = pstmt.executeUpdate();
        
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
# 规范化
* **在try外给出引用**
* **在try内实例化**
* **在finally中关闭连接**
>         if(rs!=null)rs.close();
        if(pstmt!=null)pstmt.close();
        if(con!=null)con.close();