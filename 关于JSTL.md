# JSTL
## 概述
* apache的东西，依赖EL

* 使用jstl需要导入jar

* 四大库：
>core:核心库，重点
fmt:格式化：日期数字
sql:过时
xml:过时

##导入标签库
* jar包(jstl.jar和standard.jar)
* 在JSP页面中:<%@ tablib prefix="前缀 uri="路径"%>

## core常用标签(C标签)
* out和set
>* out:输出
>>* <c:out value="${aaa}"/\>与${aaa}相同,可以转义特殊字符和标签防止<script\>攻击
>>* value:可以是字符串常量，也可以是EL 表达式
>>* default:当要输出额内容为null时，会输出default指定的值
>>* escapeXml:默认为true，表示转义
>* set:设置域的属性
>>*  <c:set var="a" value="hello" scope="session:\>,域不指定就是pageContext
>>* var :属性名
>>* value:属性值
>>* 域名
* remove
> * 删除属性
>>* var：变量名
>>* scope:如果不给出scope ，表示删除所有域中的该名变量，否则删除指定域中的
* url
>* 指定一个路径 
>>* value:指定一个路径会在路径前面自动添加项目名
<c:url value="/index.jsp"/\>,会自动输出/项目名/index.jsp
>* 子标签<c:param\>,用来给url后面添加参数
<c:url value="/index.jsp"/\>
 <c:param name="username" value="张三"/\>
 </c:url\>
 结果为：/项目名/index.jsp?username=%ED%2C%3F%4D%3C%3F，可以对参数进行url编码
 > * var:指定变量名，一旦添加这个属性，url标签就不会输出到页面，而是保存到域中<c:url var = "a" scope = "request"\>
>> * 
* if
> * test属性必须是一个boolean类型的值,执行的是标签体的内容，不指定域就全域查找，没有else
>> * <c:if test="${not empty requestScope.a}"\>
<c:out value="${a}"\>
</c:if\>
* choose
>* 对应if..else if...else
>>* <c:choose\>
>> * <c:when test="{score\>100||score<0}"\>错误的分数:${score}</c:when\>
>> * <c:when test="{score\>=90}"\>A级</c:when\>
>>* <c:otherwise\>E级</c:otherwise\>
>> * </c:choose\>
* forEach
> * 用来循环便利数组和集合
>> * 假如域中有个strs数组
>>* <c:forEach items="{$strs}  var="i" \>
${i}
</c:forEach\>
> * 还可以以计数方式来循环,步长默认为1
>>* <c:forEach var="i" begin="1" end="10" step="2"\>
${i}
</c:forEach\>
## 有标签体的标签
* empty:无标签体
* JSP：**JSP2.0已经不支持这个类型了**，表示标签体内容可以是JAVA，可以是标签，可以是EL表达式
* sciptless:只能是EL表达式，也可以是其他标签
* tagdependent:标签体内容不会被执行，earshot直接赋值给标签处理类
*Writer out = this.getJspContext().getOut();获取当前jsp页面的输出流
this.getJspBody().invoke(out);//执行标签体内容，把结果写到指定流中，即页面上