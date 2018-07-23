# 关于BaseServlet

## 我们希望一个Servlet中可以有多个请求处理方法

 ## 示例

* BaseServlet(不在web.xml中注册)

  ```java
  package com.dxx.web.servlet;
  
  import javax.servlet.ServletException;
  import javax.servlet.http.HttpServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  import java.lang.reflect.InvocationTargetException;
  import java.lang.reflect.Method;
  
  public abstract class BaseServlet extends HttpServlet {
      @Override
      protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          String methodName = req.getParameter("method");
          if(methodName == null||methodName.trim().isEmpty()){
              throw new RuntimeException("没有传递method参数!无法确定方法");
          }
          /*
           * 1.得到方法名，通过反射得到方法对象
           * 需要得到当前类Class
           */
          Class clazz = this.getClass();
          Method method = null;
          try {
              method = clazz.getMethod(methodName,HttpServletRequest.class,HttpServletResponse.class);
          } catch (NoSuchMethodException e) {
              throw new RuntimeException("要调用的方法："+methodName+"不存在!");
          }
          /*
           * 调用
           */
          try {
              String fun = (String) method.invoke(this,req,resp);//反射调用
  
              if(fun == null||fun.trim().isEmpty()){
                  return;
              }
              if(fun.contains(":")){
                  String _fun = fun.split(":")[0];
                  String path = fun.split(":")[1];
                  if(_fun.equalsIgnoreCase("r")){
                      resp.sendRedirect(req.getContextPath()+path);
                  }else if(_fun.equalsIgnoreCase("f")){
                      req.getRequestDispatcher(path).forward(req,resp);
                  }else {
                      throw new RuntimeException("当前版本不支持");
                  }
  
              }else {
                  req.getRequestDispatcher(fun).forward(req,resp);
              }
          } catch (IllegalAccessException e) {
              throw new RuntimeException(e);
          } catch (InvocationTargetException e) {
              throw new RuntimeException(e);
          }
      }
  }
  
  ```

* 其实现类AServlet(在web.xml注册，可以访问)

  ```java
  package com.dxx.web.servlet;
  
  import javax.servlet.ServletException;
  import javax.servlet.annotation.WebServlet;
  import javax.servlet.http.HttpServletRequest;
  import javax.servlet.http.HttpServletResponse;
  import java.io.IOException;
  
  @WebServlet(name = "AServlet")
  public class AServlet extends BaseServlet{
  
      public String fun1(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          System.out.println("/index.jsp");
          return "/index.jsp";
      }
  
      public String fun2(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          System.out.println("f:/index.jsp");
          return "f:/index.jsp";
      }
  
      public String fun3(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          System.out.println("r:/index.jsp");
          return "r:/index.jsp";
      }
  
      public String fun4(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          System.out.println("null");
          return null;
      }
  
  
  }
  
  ```

  