# 关于Properties配置文件

## 格式

```properties
名=值
名=值
名=值
...
```

## 获取方法

* 放在src目录下

  ```java
  private static Properties props;
  
      static {
          InputStream in = DaoFactory.class.getClassLoader().getResourceAsStream("dao.properties");
          props = new Properties();
          try {
              props.load(in);
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
  
  ```

  

* 如dao.properties，下面是一个DAO工厂

  ```java
  package com.dxx.projec1.dao;
  
  import java.io.IOException;
  import java.io.InputStream;
  import java.util.Properties;
  
  /**
   * 通过配置文件获得DAO实现类的名称
   * 通过类名完成创建类对象
   */
  public class DaoFactory {
      private static Properties props;
  
      static {
          InputStream in = DaoFactory.class.getClassLoader().getResourceAsStream("dao.properties");
          props = new Properties();
          try {
              props.load(in);
          } catch (IOException e) {
              throw new RuntimeException(e);
          }
      }
      /**
       * 返回一个具体的UserDao的实例
       * IUserDao@return
       */
      public static IUserDao getUserDao(){
          /*
            给出配置文件，文件中给出UserDao借口的实现类名称
            这个方法获取实现类的类名，通过反射创建对象
           */
          String daoClassName = props.getProperty("com.dxx.project1.dao.UserDao");
          try {
              Class clazz = Class.forName(daoClassName);
              return (IUserDao)clazz.newInstance();
          } catch (Exception e) {
              throw new RuntimeException(e);
          }
  
      }
  }
  
  ```

  