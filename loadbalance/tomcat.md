tomcat配置
==========

配置文件在tomcat安装目录（例如/usr/share/tomcat6/)下的conf目录中，打开server.xml文件， 找到
Connector设置一节：

```xml
<Connector port="8080"
	protocol="HTTP/1.1"
    connectionTimeout="5000"
    maxHttpHeaderSize="8192"
    maxThreads="500"
    acceptCount="3000"
    maxKeepAliveRequests="200"
    keepAliveTimeout="1000"
    redirectPort="8443" />
```