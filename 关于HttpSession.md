# HttpSession
## HttpSession概述
* HttpSession是JavaWeb提供的，用来会话跟踪的的类。session是服务器端对象，保存在服务器端
* ** HttpSession是Servlet三大域对象之一（request,session,ServletContext),所以也有Attribute相关方法**
*  **HttpSession底层依赖Cookie，或是URL重写**
## HttpSession作用
* 会话范围：会话范围是某个用户首次访问服务器开始，到该用户关闭浏览器结束
> 会话：一个用户对服务器的多次连贯性请求，即请求中间没有关闭浏览器
* 服务器会为每个客户端创建一个session对象，session对象好比客户在服务器段的账户，它们被服务器保存到一个**Map**中（这个Map称为session缓存）
> **Servlet中得到session对象：HttpSession session = request.getSession();**
> **Jsp中得到session对象：session是jsp内置对象之一，可直接使用**
## Session的序列化

* 服务器关闭时，Catalina/localhost/项目目录下会生成SESSIONS.ser文件，这是session可以“死而复生”的关键，在用户浏览器不关闭的情况下，关闭服务器时会生成，重启时通过这个文件使session“复活”

* 通过在conf/context.xml文件中添加配置可以使其失效

  ```xml
  <Context>
      <Manager pathname=""/>
  </Context>
  ```

  

## SessionID

* request.getSession()方法
> * 获取Cookie中的sessionId:
>>*如果sessionId不存在，创建sessionId，服务器保存session，浏览器把新创建的sessionId保存到Cookie
>> * 如果sessionId存在，通过sessionId查找session对象，没有找到就创建session并保存
>> * 如果sessionId存在，且通过sessionId找到session对象，则不创建
>> * 返回session
> * 如果创建了新的session，浏览器会得到一个包含sessionId的Cookie，这个Cookie的生命为-1，只在浏览器内存存活
>  * 下次请求时，再次执行request.getSession()方法，会通过sessionId找到session对象，故**与上次使用的是同一个session**
* **服务器不会马上创建session，在第一次调用request.getSession()（第一次获取Session)时才会创建**
> 访问JSP会自动创建
> Servlet就需要调用
* request.getSession(false):如果Cookie不存在session，**返回null,不创建session**,存在就返回session
* request.getSession(true):和request.getSession()相同
## 其他方法
* request.getID():获取sessionID
* int getMsxINactiveInterval():获取session的最大不活动时间（秒），默认为30min
* void invalidate()：让session失效，失效后，若客户端再次请求，服务器会创建一个新的session
* boolean isNew():查看session是否为新的
> if(request.getSession.isNew(){}
## 在web.xml中配置session最大活动时间
 <session-config\>
        <session-timeout\>15</session-timeout\>
</session-config\>
## URL重写
* session依赖Cookie，但当浏览器禁用Cookie时，无法获得sessionId，使用URL

> response.encodeURL("/项目/../当前路径“)

* 这个方法会查看Cookie是否存在，如果不存在就在指定的url后添加jsessionId参数
如果存在就不会在url后添加东西
* 该方法会对URL进行只能的重写，当请求中没有归还sessionid的Cookie，该方法就会重写url，否则不重写。路径必须是本站的