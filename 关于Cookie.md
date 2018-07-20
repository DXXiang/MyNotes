# Cookie
*  HTTP协议和Cookie（了解）
> * Cookie是HTTP协议制定的。先由服务器保存Cookie到浏览器，再下次浏览器请求服务器时，把上一次请求得到的Cookie再归还给服务器
>* 由服务器创建保存到客户端服务器的一个键值对。服务器保存Cookie的**响应头**:
S**et-Cookie**:**aaa=AAA **
**Set-Cookie**:**bbb=BBB**
>* 当浏览器请求服务器时，会把该服务器保存在浏览器的Cookie随请求发送给服务器。浏览器归还Cookie的**请求头**：
**Cookie**:**aaa=AAA;bbb=BBB**
>  * Http协议规定（保证不给浏览器太大压力）：
>> **一个服务器Cookie最大4KB**
一个**服务器**最多可以向一个浏览器保存**20个Cookie**
1个**浏览器**最多可以保存**300个Cookie**
>* 浏览器大战：因为浏览器竞争激烈，所以很多浏览器在一定范围内违反HTTP规定
* Cookie的用途
>* 服务器使用Cookie来跟踪客户端状态
>* 保存购物车（购物车中的商品不能用request保存，因为它是一个用户向服务器发送的多个请求信息）
>* 显示上交登录名（一个用户多个请求）
*  **Cookie是不能跨浏览器的**
* JavaWeb中使用Cookie
>* 原始方式（了解）
>> 使用response发送Set-Cookie响应头
>>使用request获取Cookie 请求头
>* **便捷方式(精通）**
>>**使用response.addCookie()方法向浏览器保存Cookie**
>>**使用request.getCookies()获取浏览器归还的Cookie**
* Cookie详解
>* Cookie不止有name和value两个属性
>* Cookie的maxAge(掌握):Cookie的最大生命，即Cookie可保存的最大时长。以秒为单位，例如cookie.setMaxAge(60)表示这个Cookie会被浏览器保存到硬盘
>>* maxAge>0:浏览器会把Cookie保存到客户端硬盘上，有效时长由maxAge决定
>>* maxAge<0(默认）:Cookie只会在浏览器内存中存在，当浏览器关闭时，浏览器进程结束，Cookie死亡
>>* maxAge=0:浏览器会马上删除这个Cookie
>* Cookie的path(理解):
>> *  **Cookie的path并不是设置这个Cookie在客户端的保存路径**
>>*  Cookie的path由**服务器**创建Cookie时设置
>>* 当浏览器访问服务器某个路径时，需要归还哪些Cookie给服务器？这由Cookie的path决定
>>* **浏览器访问服务器的路径如果包含某个Cookie的路径，那么就会归还这个Cookie（不包含的就不归还)**
如aCookie.path=/dxx/jsps/
访问/dxx/index.jsp就会归还
>> * ** Cookie的path默认值：当前访问路径的父路径（当前目录）**