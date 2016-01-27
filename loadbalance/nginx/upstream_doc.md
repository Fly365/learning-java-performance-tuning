HTTP Upstream模块(翻译)
=========

注：翻译自Nginx官网文档 [HTTP Upstream模块](http://nginx.org/en/docs/http/ngx_http_upstream_module.html)

ngx_http_upstream_module 模块用于定义可以被proxy_pass, fastcgi_pass, uwsgi_pass, scgi_pass和memcached_pass指令引用的服务器集群。

# 配置范例

```bash
upstream backend {
    server backend1.example.com       weight=5;
    server backend2.example.com:8080;
    server unix:/tmp/backend3;

    server backup1.example.com:8080   backup;
    server backup2.example.com:8080   backup;
}

server {
    location / {
        proxy_pass http://backend;
    }
}
```

动态可配置集群(Dynamically configurable group)是商业版本([commercial subscription](https://www.nginx.com/products/))的一部分.

```bash
resolver 10.0.0.1;

upstream dynamic {
    zone upstream_dynamic 64k;

    server backend1.example.com      weight=5;
    server backend2.example.com:8080 fail_timeout=5s slow_start=30s;
    server 192.0.2.1                 max_fails=3;
    server backend3.example.com      resolve;

    server backup1.example.com:8080  backup;
    server backup2.example.com:8080  backup;
}

server {
    location / {
        proxy_pass http://dynamic;
        health_check;
    }
}
```

# 指令

## upstream指令

upstream指令用于定义服务器集群。服务器可以监听在不同端口。另外，监听在TCP和UNIX-domain socket的服务器可以混合使用

    Syntax:	upstream name { ... }
    Default:	—
    Context:	http

范例：

```bash
upstream backend {
    server backend1.example.com weight=5;
    server 127.0.0.1:8080       max_fails=3 fail_timeout=30s;
    server unix:/tmp/backend3;

    server backup1.example.com  backup;
}
```

默认，使用带权重的round-robin平衡算法将请求分派到服务器。在上面的例子中， 每7个请求将被如下分配：

- 5个请求去backend1.example.com
- 1个请求去127.0.0.1:8080
- 1个请求去unix:/tmp/backend3

在和某台服务器通讯的过程中，如果发生错误， 请求将被分派给下一个服务器， 以此类推知道所有可用服务器都被尝试。如果没有任何一个服务器可以可以返回成功的应答，则客户端将会收到和最后一台机器的通讯结果。

## server指令

server指令用于定义一台服务器的地址和其他参数。地址可以是域名或者IP地址，端口可选，或者是以"unix:"前缀指定的UNIX-domain socket路径。如果端口没有指定，将使用80端口。可以解析为多个IP地址的域名将一次性定义多台服务器。

    Syntax:	server address [parameters];
    Default:	—
    Context:	upstream

下面是可用的参数列表。

- weight=number

	设置服务器的权重，默认为1.

- max_fails=number

	设置在参数fail_timeout指定的时间内，发生的和服务器通讯的不成功的数量。默认情况，不成功尝试次数被设置为1.如果设置为0则关闭尝试计数。至于什么是不成功的尝试则通过proxy_next_upstream, fastcgi_next_upstream, uwsgi_next_upstream, scgi_next_upstream, 和 memcached_next_upstream指令定义。

- fail_timeout=time

	用于设置：

	- 时间段，在此期间应该发起指定数量的和服务器通讯的不成功尝试，以判断服务器是否不可到达
	- 和接下来服务器被判定为不可到达的时间期间

	默认，fail_timeout参数被设置为10秒钟.

    （注：如果设置为max_fails=5;fail_timeout=30s，表示如果有5次请求失败，则该服务器被断定为不可到达，之后30s之类将不再尝试这台机器。再之后的每30s，都将进行最多5次尝试，如果继续失败则继续判断为不可到达并不再尝试。）

- backup

	标记当前服务器为备用服务器。当主服务器(注：应该是没有标记为backup和down的服务器)都不能达到时请求将被分派过去。

- down

	将当前服务器标记为永久不可到达。

另外，商业版本将支持下面的参数：

- max_conns=number

	限制同时激活的到被代理的服务器的最大连接数。默认值为0,表示没有限制。

	** 当长连接(keepalive connection)和多work被启用时， 到被代理的服务器的最大连接总数可能超过max_conns参数的值**

- resolve

	监视域名对应的服务器IP地址的变化，并自动修改upstream配置而不需要重启nginx服务器(1.5.12版本)。The server group must reside in the shared memory（注：这句不懂...）.

    为了让这个参数工作，resolver必须指定在http块中。例如：

    ```bash
    http {
        resolver 10.0.0.1;

        upstream u {
            zone ...;
            ...
            server example.com resolve;
        }
    }
    ```

- route=string

	设置服务器路由名字(route name)

- slow_start=time

	设置时间段，在此期间服务器将从0到正常值逐渐恢复它的权重，当服务器从不健康变成健康，或者服务器从被标记为不可到达一段时间后变成可到达时。默认值是0, 表示关闭缓慢启动功能。

** 如果服务器集群中仅有一台服务器， max_fails, fail_timeout 和 slow_start 参数都将被忽略， 而这台机器永远不会被标记为不可到达。**

## zone指令

zone指令在版本1.9.0中出现。

	Syntax:	zone name [size];
    Default:	—
    Context:	upstream

定义共享内存zone的名称和大小， 共享内存zone用于保存集群配置和运行时状态，以便在worker进程之间共享。多个集群可以分享同一个zone。在这种情况下，只需要指定一次大小。

另外，作为商业版本的一部分，这样的集群容许改变集群成员或者修改某台服务器的设置而不需要重启nginx。

## state指令

state指令在版本1.9.7中出现，属于商业版本的一部分.

    Syntax:	state file;
    Default:	—
    Context:	upstream

指定一个文件来保存动态可配置集群的状态。状态目前仅限于服务器列表及其参数.当解析配置时state文件被读取并在每次upstream配置被修改时更新。直接修改这个文件的内容是无效的。这个指令不能server指令一起使用。

configuration reload 或者 binary upgrade造成的修改可能丢失。

## hash指令

hash指令在版本1.7.2中出现。

Syntax:	hash key [consistent];
Default:	—
Context:	upstream

指定服务器集群的负载均衡方法，客户端-服务器映射基于散列key值。key可以包含文本，变量和他们的组合。注意从集群中添加或者删除一个服务器可能导致大部分key重新映射到不同的服务器。这个方法兼容Cache::Memcached Perl类库。

如果consistent参数被指定，则将使用ketama一致性哈希算法。这个算法确保当有一台服务器添加到集群或者从集群中删除时仅有少数key被重新映射到不同的服务器。这将有助于缓存系统实现更高的缓存命中率。ketama_points参数设置为160时，这个方法兼容Cache::Memcached::Fast Perl 类库。


















