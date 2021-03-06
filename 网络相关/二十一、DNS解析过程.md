###### 二十一、DNS解析过程

**1、迭代查询和递归查询**

很清晰地显示出了一条DNS查询链：`本地DNS服务器`-->`根DNS服务器`-->`顶级域DNS服务器`-->`权威DNS服务器` ，所有查询都是递归的。
这是**递归查询**。

这种利用了**迭代查询**和**递归查询**，从Client与本地DNS之间是**递归查询**，其余则是**迭代查询**。
所谓 **递归查询**过程 就是 “查询的递交者” 更替, 而 **迭代查询**过程 则是 “查询的递交者”不变。
从理论上讲，任何DNS查询既可以是迭代的也能是递归的。
而在实际过程中，更常用的是图上 **从请求主机到本地DNS服务器的查询是递归，其余查询是迭代的**这种方式。

**2、DNS缓存**

**DNS缓存（DNS Caching）**：为了改善时延性能并减少在因特网上到处传输的DNS报文数量，DNS广泛使用了缓存技术。
在一个请求链中，当某DNS服务器接收一个DNS回答时，它能将该回答中的信息缓存在本地存储器中。那么另一个对相同主机名的查询到达该DNS服务器时，该DNS服务器就可以提供所要求的IP地址，即使它不是该主机名的权威服务器。
由于IP和主机名的映射并不是永久的，DNS服务器在一段时间后就会丢弃缓存的信息。
本地DNS服务器也能够缓存TLD服务器的IP地址，从而允许本地DNS绕过查询链中的根DNS服务器。
**而事实上，有DNS的地方，就有缓存。浏览器、操作系统、本地 DNS服务器、根DNS服务器，它们都会对DNS结果做一定程度的缓存。**

**3、DNS解析过程**

大致分为8步：

> * 1、发起基于域名的请求后，首先检查`本地缓存`（浏览器缓存-->操作系统的hosts文件） 
> * 2、如果`本地缓存`中有，直接返回目标IP地址，否则将域名解析请求发送给`本地DNS服务器`
> * 3、如果`本地DNS服务器`中有，直接返回目标IP地址，到这一步基本能解析80%的域名。如果没有，`本地DNS服务器`将解析请求发送给`根DNS服务器`
> * 4、`根DNS服务器`会返回给`本地DNS服务器`一个所查询的`TLD服务器`地址列表
> * 5、`本地DNS服务器`再向上一步返回的`TLD服务器`发送请求，`TLD服务器`查询并返回域名对应的`权威域名服务器`的地址
> * 6、`本地DNS服务器`再向上一步返回的`权威域名服务器`发送请求，`权威域名服务器`会查询存储的域名和IP的映射关系表，将IP连同一个`TTL`（过期时间）值返回给`本地DNS服务器`
> * 7、`本地DNS服务器`会将IP和主机名的映射保存起来，保存时间由`TTL`来控制
> * 8、`本地DNS服务器`把解析的结果返回给用户，用户根据`TTL`值缓存在`本地系统缓存`中，域名解析过程结束