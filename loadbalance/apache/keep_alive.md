apache长连接支持
==========

apache同样在支持HTTP反向代理和负载均衡时，可以打开keep alive实现长连接。

# 配置支持长连接

apache的配置文件在apache安装目录(例如/etc/httpd)下, 打开conf/httpd.conf,需要设置以下内容：

	// 打开长连接支持(默认就是on)
	KeepAlive on
    // 加大一个长连接上容许的请求的数量，必须设置，默认100太小
    MaxKeepAliveRequests 10000
    // 长连接空闲的超时时间，单位为秒，默认5秒，一般没有必要改大。
	KeepAliveTimeout 5

> 注： 这个配置是设置HTTP client和apache之间的长连接，对于apache和HTTP server(比如tomcat)之间的长连接该如何配置，我没有找到设置的地方。不过实际测试中，发现apache和tomcat之间默认就是开启了长连接的。

# 相关配置项的解释

以下内容摘自[apache官方文档](http://httpd.apache.org/docs/2.4/mod/core.html)。


## KeepAlive 指令

    Description:	打开HTTP长连接支持
    Syntax:	KeepAlive On|Off
    Default:	KeepAlive On
    Context:	server config, virtual host
    Status:	Core
    Module:	core

HTTP/1.0的Keep-Alive扩展和HTTP/1.1的持久连接特性提供长期的HTTP 会话，容许多个请求在同一个TCP连接上发送。在某些情况下，对于带有很多图片的HTML文档这，会在延迟时间上得到接近50%的加速。要开启Keep-Alive连接，设置KeepAlive为on。

对于HTTP/1.0 客户端，Keep-Alive连接仅仅在客户端特别要求的情况下使用。另外，客户端是HTTP/1.0的Keep-Alive连接仅仅在内容长度已知的情况下使用。这使得动态内容比如CGI输出，SSI页面和服务器生成的目录列表将无法使用Keep-Alive连接到HTTP/1.0客户端。对于HTTP/1.1客户端，除非特别声明否则默认是持久连接。如果客户端要求，将使用chunked 编码以便在持久连接上发送未知长度的内容。

当一个客户端使用Keep-Alive连接时， 这个客户端将被MaxConnectionsPerChild指令计数为一个单个的"请求"，无论这个连接发送了多少个请求。

## MaxKeepAliveRequests 指令

    Description:	一条持久连接(长连接)上容许的请求的数量
    Syntax:	MaxKeepAliveRequests number
    Default:	MaxKeepAliveRequests 100
    Context:	server config, virtual host
    Status:	Core
    Module:	core

MaxKeepAliveRequests 指令限制当keepalive开启时每个连接容许的请求数量。如果设置为0, 容许无限制的请求数量。推荐设置为一个比较高的值以最大化服务性能。

范例:

	MaxKeepAliveRequests 500

## KeepAliveTimeout 指令

    Description:	在一个持久连接上服务器等待后续请求的时间数量
    Syntax:	KeepAliveTimeout num[ms]
    Default:	KeepAliveTimeout 5
    Context:	server config, virtual host
    Status:	Core
    Module:	core

apache httpd在关闭一个连接前能够等待后续请求的秒数。如果增加ms后缀这个超时值也可以设置为毫秒。

设置KeepAliveTimeout为一个高的值可能在重负载的服务器上导致性能问题。超时时间越长，服务器进程就会在空闲客户端的连接上保持更长时间。

如果没有为命名虚拟站点设置 KeepAliveTimeout, 第一个定义的最匹配本地IP和端口的虚拟站点的值将被使用。
