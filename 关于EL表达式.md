# 关于EL表达式
* EL是JSP内置的表达式语言
>* jsp2.0开始，不再使用Java脚本，使用EL表达式和动态标签替代Java脚本
 >* EL替代的是<%@=...\>，即只能做输出
 
 * EL表达式读取四大域
>* ${aaa}:全域查找名为aaa的属性，不存在就输出空字符串
> * ${page**Scope**.xxx}
> * ${request**Scope**.xxx}
> * ${session**Scope**.xxx}
> * ${application**Scope**.xxx}

* EL可以输出的东西都在11个内置对象中

## JavaBean导航
* 相当于不断调用get()方法
> ${requestScope.emp.addr.street}相当于requestScope.emp.getAddr().getStreet

## 内置对象
* 11个，其中10个是Map，pageContext是pageContext类型
* 4个Scope
* param：Map<String，String\>类型
> ${param.key}
* paramValues：Map<String，String[]\>类型
>${paramValues.key[i]}
* header:请求头，适用单值头,Map<String，String\>类型
* headerValus:适用多值头,Map<String，String[]\>类型
> 注意User-Agent不能直接使用，会识别为减法
${header["User-Agent"]}
* initParam:获取<context-param\>内的参数
* Cookie：Map<String,Cookie\>类型
> ${cookie.username.value}
 * pageContext:pageContext类型


# EL函数库
 * 由**JSTL**提供的
* 导入标签库
* 自定义标签
> 1. 写一个java类，可定义多个方法，但必须是static的而且有返回值
> 2. 在WEB-INF下创建一个tld文件，编辑<function\>标签，例如WEB-INF/libs/test.tld
>><function\>
        <name\>fun1</name\>
        <function-class\>com.dxx.demo.fn</function-class\>
        <function-signature\>java.lang.String fun1()</function-signature\>
    </function\>
>3. 在jsp中用taglib导入标签
>><%@ taglib prefix="fn" uri="/WEB-INF/tlds/test.tld" %\>
>>${fn:fun1()}

#使用
<%--今后使用这种方法来获取路径--%>
<a href="${pageContext.request.contextPath}/pathTest.jsp"\>点击这里</a\>
<form action="${pageContext.request.contextPath}/ImageServlet" method="post">
    <input type="submit" value="提交">
</form>
${pageContext.session.id}