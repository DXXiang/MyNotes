# web.xml 

---

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <!--配置Spring IoC文件路径-->
  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>WEB-INF/applicationContext.xml</param-value>
  </context-param>
  <!--配置ContextLoaderListener用以初始化Spring IoC容器-->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  <servlet>
    <!--注意：Spring MVC 框架会根据 servlet-name 配置，找到/WEB-INF/dispatcher-servlet.xml作为配置文件载入Web工程中-->
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!--使得Dispatcher在服务器启动时就初始化-->
    <load-on-startup>2</load-on-startup>
  </servlet>
  <!--Servlet拦截配置-->
  <servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>*.do</url-pattern>
  </servlet-mapping>
</web-app>
```



# applicationContext.xml

---

* 位于/WEB-INF/下

```xml
<!--使用注解驱动-->
    <context:annotation-config/>

    <!--引入配置文件-->
    <bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
        <property name="locations">
            <list>
                <value>classpath*:jdbc.properties</value>
            </list>
        </property>
    </bean>

    <!--数据库连接池-->
    <bean id="dataSource"
          class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
        <property name="driverClassName" value="${jdbc.driver}"/>
        <property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
        <property name="maxActive" value="255"/>
        <property name="maxWait" value="10000"/>
    </bean>
```



# proterties配置文件

---

## jdbc.properties

```properties
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mydb
jdbc.username=root
jdbc.password=dxx19971221
```



# dispatcher-servlet,xml 

---

```xml
<!--使用注解驱动-->
    <mvc:annotation-driven/>
    <!--定义扫描装载的包-->
    <context:component-scan base-package="com.*"/>

    <!--定义视图解析器-->
    <!--找到Web工程/WEB-INF/JSP文件夹，且结尾为jsp的文件作为映射-->
    <bean id="viewResolver"
          class="org.springframework.web.servlet.view.InternalResourceViewResolver"
          p:prefix="/WEB-INF/jsp/" p:suffix=".jsp"/>

    <!--采用注解事务-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
```

# Mapper的存放和配置

---

## mapper.xml

---

放在Resources/mapper/下

## 配置

---

### applicationContext.xml

注意sqlSessionFactory的第三个配置项

```xml
<!--集成Mybatis-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <property name="configLocation" value="mybatis-config.xml"/>
        <property name="mapperLocations" value="classpath*:mapper/*.xml"/>
    </bean>

    <!--配置数据源事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
<!--采用自动扫描方式创建mapper bean-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.dxx.chapter14"/>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <property name="annotationClass" value="org.springframework.stereotype.Repository"/>
    </bean>
```

### mybatis-config.xml

不再需要写mapper元素，写setting就行

# MVC层次注解

---

## @Repository 持久层

## @Service 业务层

## @Controller 控制层

# 报错

---

## XXX描述得很完美

就是标签声明那个地址没写全

## java.lang.NoClassDefFoundError: com/fasterxml/jackson/databind/Module

还要json支持的jar包

```xml
<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.6</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.6</version>
    </dependency>
    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-annotations -->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.6</version>
    </dependency>
```

