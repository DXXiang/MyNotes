# 关于JavaBean
## JavaBean规范
 * 一般放在名为**domain**的包中
 * **必须要有默认无参构造器**
 * **必须为成员提供getter/setter方法**
 *  一般对于具有getter/setter方法的成员变量称为**属性**
		> 如果只有get方法的属性叫**只读属性**
 * 就算一个属性没有对应的成员变量，只有getter/setter方法也可以
  > 比如为Id属性提供get方法，但却没有设置名为id的变量
* **属性名称的决定：对getter/setter方法去掉get/set后的小写名称**
* 布尔类型的属性，方法可以是is或get

## 内省
通过反射来操作javabean，但比反射方便
 * BeanInfo 是一个javaBean类型的信息类
>BeanInfo info  = Introspector.getBeanInfo(User.class)
>info.getPropertyDiscriptor（）得到PropertyDiscriptor[]
>通过PropertyDiscriptor得到一个属性的读、写方法,通过这些方法操作JavaBean对象



# 相关工具

* **commons-beanutils，依赖内省完成**
> commons-beanutiles.jar
commons-loggin.jar

* 方法

  ```java
  BeanUtils.populate(bean, map);
  ```

  把一个map封装到bean中，map的键名要与bean中的变量名完全相同

* 自制工具

  ```java
  public static <T> T toBean(Map map, Class<T> clazz) {
          try {
              T bean = clazz.newInstance();
              BeanUtils.populate(bean, map);
              return bean;
          } catch (Exception var3) {
              throw new RuntimeException(var3);
          }
      }
  ```

* 用法

  ```java
  类名.toBean(request.getParameterMap(),Bean名.class);
  ```

  