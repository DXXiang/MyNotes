# 关于JSP

## 指令描述

* 格式

  > <%@ 指令名 attr1="" attr2=""%>
  >
  > 一般会放在JSP文件的最上方，但这不是必须的

* 三大指令

  > page、include、taglib
  >
  > 最常用、最复杂的是page
  >
  > 每个指令都可以出现多次
  
  * 九大内置对象
  > * out
  > * config:ServletConfig
  > * page:即"this"
  > * pageContext:
  > * exception:只有错误页面可以使用
  > * request
  > * response
  > * application:ServletContext
  > * session:HttpSession
  

## page指令

* pageEncoding

  > pageEncoding = "UTF-8"
  >
  > 当前jsp页面的编码
  >
  > 在服务器把jsp编译成.java时需要使用pageEncoding

* contentType

  > contentType="text/html;charset = UTF-8"
  >
  > 表示添加一个响应头：Content-Type
  >
  > 等同于response.setContentType("text/html;charset = UTF-8")

* 当以上两者只使用一个时，另一个默认为使用的那个的值
* import
> 导包，多个用逗号隔开（可以出现多次）
* errorPage和isErrorPage
>  * errorPage:当前页面如果抛出异常，就转发到由errorPage指定的页面
>  * isErrorPage：指定当前页面是否是一个错误页面，**仅有错误页面才可使用内置的exception对象**
 >* 也可以在web.xml中配置错误页面，与page指令无关
<error-page\>
	<exception-code\>错误代号</exception-code\>
</error-page\>
或者更细致：
<error-page\>
	<exception-type\>Exception类</exception-type\>
</error-page\>
* autoFlush和buffer
>* autoFlush:指定jsp的输出流缓冲区满时，是否自动刷新。默认为true,如果为false，满了就抛异常f
> * buffer:指定缓冲区大小，默认8kb，不需要修改
* isELIgnored:是否忽略el表达式，默认值为false
* 基本没用的属性
> * language:指定当前jsp编译后的语言类型，默认为java
> * isThreadSafe:当前jsp是否支持并发访问（会实现已废除的借口）
> * session:是否支持session
> * extends:让生成的sevlet继承指定的类

  ## 九大内置对象
* out
> 输出流

* config:ServletConfig对象
* page
>当前jsp对象，引用类型为Objcet,Object page = this;
* pageContext:
>  * 一个顶九个
 >  * ServletContext:整个应用程序
>  * session:整个会话
>  * request:一个请求链
>  * pageContext:一个jsp页面，这个域在当前jsp页面和当前jsp页面中使用的标签之间共享数据'
>> * page**域对象**
>> * **代理其他域，可以存取request、session、ServletContext域**
pageContext.setAttribute("xxx","XXX",PageContext.SESSION_SCOPE);
>> * **获取其他8个内置对象**
>> * **全域查找**
pageConteaxt.findAttribute("xxx"),从小到大查找，小域优先
getRequest();
* exception:只有错误页面可以使用
* request
* response
* application:ServletContext
* session:HttpSession

##include 静态包含
* 与RequestDispatcher的include()方法功能相似
* <@include%>在编译java文件时，共同生成一个java文件，生成**1个class**，故路径不能是变量
* RequestDispatcher的include()方法,2个.class,运行时才合并使用
 * 作用：把页面分解，使用包含的方式组合在一起，把变化的部分和不变的部分分开，只需要处理变化的部分，比如在一个页面中引入需要的所以import，然后包含进来，**可重用**
 
##taglib导入标签库
* 两个属性
> * prefix:指定标签库在本页面中的前缀，我们自己起名
> * uri:指定标签库的位置
> * <%@taglib prefix="pre" uri="/struts-tags"%\> <pre:text\>

# JSP动作标签
这些jsp的动作标签，与html提供的标签有本质区别
 * 标签由（tomcat）服务器来解释执行，与Java代码一样都在服务器端执行
 
 * HTML由浏览器执行
 
 * <jsp:forward\>:转发，与RequestDispatcher的forward()相同，一个在Servlet中使用，一个在jsp中使用
 
  * <jsp:include\>:动态包含，与RequestDispatcher的include()相同，一个在Servlet中使用，一个在jsp中使用
  > <@include%>和 <jsp:include\>:前者是合并，后者是相当于调用
  
  * <jsp:param\>:用来作为forward和include的子标签（在其里面)，用于传递参数
  
###与Bean相关 （已经不用了）
  * <jsp:useBean\>:创建或查询bean
  > <jsp:useBean id="user1 class="com.dxx.domain.User" scope="域" /\>:查找
  > <jsp:useBean id="user1 class="com.dxx.domain.User" scope="域" /\>:然后创建
 * <jsp:setProperty\>:
  > <jsp:setProperty property="usernname" name="user1" value="张三 \>
 * <jsp:getProperty\>:
   > <jsp:getProperty property="usernname" name="user1" \>
   
# 在web.xml中配置jsp
<Context reloadable="true"\>,修改时重新加载，是个定时操作，只适合在开发阶段
  
  
