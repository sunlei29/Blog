# 下一代HTTP——SPDY

标签（空格分隔）： HTTP

---

## 什么是SPDY

SPDY是google研发的下一代基于TCP的应用层协议，其目的是降低网页加载延迟

> 应用层协议定义了运行在不同端系统上的应用程序进程如何相互传递报文，一般包括报文类型、报文语法、字段含义以及进程如何发送相应，常见的应用层协议有DNS、FTP、SMTP、HTTP、SNMP和Telnet等

SPDY声称不是取代HTTP，而是在现有HTTP之上重写连接管理和数据传输部分，目前支持该应用协议的浏览器有Chrome、Firefox、Opera和IE 11。

## SPDY VS HTTP

SPDY针对HTTP的问题，主要做了以下4个优化

#### 多路复用

在HTTP协议在传输上的瓶颈之一就是并发请求，由于1个HTTP请求会占用1个TCP连接，后续的请求再重用这个连接时就需要与服务器通信产生延迟，所以浏览器都会做优化，例如给每个域名建立多个TCP连接提高并发请求能力，但是TCP连接不可能建立太多还是会有延迟。
SPDY实现多个请求共用1个TCP连接，理论上请求数可以是任意多，这个策略很大程度上提高了请求并发度。

#### 请求优先级

这个其实应该是上一个多路复用带来的副产物，由于可并发的请求数非常多，当带宽接近饱和时，客户端可以设置请求的优先级以免阻塞，客户端可以给每个请求设置优先级。

### 请求头压缩

HTTP协议对于请求和返回的头部都没有实现压缩，SPDY实现了对于头部的压缩。

#### 服务端推送

HTTP时代服务器只能被动地等待客户端请求后才能相应，但是SPDY实现了服务端可以向客户端推送数据，比如可以推送一个JS脚本，当客户端需要访问这个JS时就会从缓存中获得，而这实际上就是在服务器端管理客户端的缓存，这个策略在使用时需要做好平衡。


## Web服务器支持

nginx和apache都支持了SPDY协议

> chrome浏览器目前只支持SPDY/3，nginx从1.6之后的版本才支持SPDY/3


## DEMO

#### 显示16张图片
HTML代码
```lang-js
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
        <title>Test Page</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    </head>
    <body>
         <img src="/img/1.jpg" />
         <img src="/img/2.jpg" />
         <img src="/img/3.jpg" />
         <img src="/img/4.jpg" />
         <img src="/img/5.jpg" />
         <img src="/img/6.jpg" />
         <img src="/img/7.jpg" />
         <img src="/img/8.jpg" />
         <img src="/img/9.jpg" />
         <img src="/img/10.jpg" />
         <img src="/img/11.jpg" />
         <img src="/img/12.jpg" />
         <img src="/img/13.jpg" />
         <img src="/img/14.jpg" />
         <img src="/img/15.jpg" />
         <img src="/img/16.jpg" />
    </body>
</html>
```

* HTTP瀑布流
![HTTP瀑布流](http://20140905.oss-cn-qingdao.aliyuncs.com/evernote/FF099900-4146-46A8-A68E-D457D1EFCBC9.png)

* SPDY瀑布流
![SPDY瀑布流](http://20140905.oss-cn-qingdao.aliyuncs.com/evernote/F0CECCD8-7653-4EF9-8E51-D0BD560C04AE.png)


从瀑布流看，SPDY的请求并发情况要远远好于HTTP，16张图片同时发起请求，但是由于SPDY依赖SSL，如果服务器的性能不够好反而会拖慢。
如上图所示7.jpg这个文件，HTTP方式只需要300ms而SPDY却需要752ms！


## 争议

#### SSL
google在SPDY在实现上依赖SSL，也就是说SPDY只对使用https有效果，使用SSL对于网站来说是比不小的性能开销，网络上的优化能否抵消SSL的消耗
另外是不是所有的web站点都需要SSL

#### 延迟优化
整体上看SPDY的优化主要体现在减少HTTP延迟上，底层通信协议仍然是TCP，所以传输效率没有发生改变，那么通过减少延迟是否可以提高网站速度

一些争议

http://www.infoq.com/cn/news/2012/09/HTTP-SPDY
http://www.infoq.com/cn/news/2012/07/roy-on-google-spdy


参考

[维基百科](http://zh.wikipedia.org/wiki/SPDY)

[SPDY: An experimental protocol for a faster web](http://www.chromium.org/spdy/spdy-whitepaper)

[spdy-v3中文翻译](http://www.fireflysource.com/spdy/spdy-v3-cn.html)

[SPDY 和你想象的不一样](http://www.oschina.net/translate/not-as-spdy-as-you-thought)