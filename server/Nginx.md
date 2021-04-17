# Nginx
nginx 作为近年来最火的中间件之一，很有必要学习
nginx 优势
1. 采用 IO 多路复用`epoll`模型，`epoll`驱动的就是在操作系统下的系统内核来进行工作，实现 IO 复用的处理方式
  - 多进程多线程处理，但一个线程只能处理一个 IO 流
  - 多路 IO 复用，采用的是一个线程并实现 IO 流非阻塞模式
  ```
  while true {
    for i in stream[]; {
      // 查看有无数据，无数据就进行循环
      if i has data
      read until unavailable
    }
  }
  ```
  所以当没有数据时，就会白白浪费 cpu，此时我们可以使用内核模型来进行循环，一般使用的是`epoll`模型，`epoll`模型相当于是一个代理，获取有数据的循环再进行遍历
  `epoll`优势
    - 解决`SELECT`模型对于文件句柄 FD 打开限制
    - 采用`callback`函数回调机制优化模型效率
2. 利用了cpu亲和`affinity`来提高 cpu 的并发处理能力减少不必要的性能损耗。cpu 亲和就是把 cpu 核心和 Nginx 工作进程绑定方式，把每个 worker 进程固定在一个 cpu 上执行，减少切换 cpu 的 cache miss，获得更好地性能
3. `sendfile`，在没有 nginx 时，用户发送一个请求获取文件的时候，会经过内核空间和用户空间，最终到达`socket`并传递给用户，而`sendfile`只会进过内核空间传递给用户

## 安装
[这里](https://zhuanlan.zhihu.com/p/138007915)查看
### 安装目录
- `/etc/logrotate.d/nginx`Nginx 日志轮转，用于`logrotate`服务的日志切割，也就是可以按天进行输出
- `/etc/nginx | /etc/nginx/nginx.conf | /etc/nginx/conf.d | /etc/nginx/conf.d/default.conf`Nginx 主要配置文件
- `/etc/nginx/fastcgi_params | /etc/nginx/uwsgi_params | /etc/nginx/scgi_params`为`cgi`配置和`fastcgi`配置
- `/etc/nginx/koi-utf | /etc/nginx/koi-win | /etc/nginx/win-utf`编码转换映射转换文件
- `/etc/nginx/mime.types`设置 http 协议的`Content-Type`与扩展名对应关系
- `/usr/lib/systemd/system/nginx-debug.service | /usr/lib/systemd/system/nginx.service | /etc/sysconfig/nginx | /etc/sysconfig/nginx-debug`用于配制出系统守护进程管理器管理方式
- `/usr/lib64/nginx/modules | /etc/nginx/modules`Nginx 模块目录
- `/usr/sbin/nginx | /usr/sbin/nginx-debug`Nginx 命令
- `/usr/share/doc/nginx-1.12.0 | /usr/share/doc/nginx-1.12.0/COPYRIGHT | /usr/share/man/man8/nginx.8.gz`Nginx 的手册和帮助文件
- `/var/cache/nginx`Nginx 的缓存目录
- `/var/log/nginx`Nginx 的日志目录
## 命令
- `nginx -t -c`：查看配置是否正确
- `nginx -s reload -c`：重新加载配置

## 配置语法

### 默认配置语法
- `user`：设置 nginx 服务的系统使用用户
- `worker_processes`：工作进程数，一般和 cpu 核心一致
- `error_log`：nginx 的错误日志
- `pid`：`nginx`服务启动时候`pid`
- `events`：事件模块
  - `worker_connections`：每个进程允许最大连接数
  - `use`：设置 nginx 使用的内核模型
- `http`: 包含所有 http 的服务
  - `server`：配置虚拟或是独立的站点
    - `listen`：监听的端口
    - `server_name`：监听的 host
    - `location`：控制每一层路径的访问
      - 可以接一个路径，然后有一个模块
    - `error_page`: 可以接状态码，之后再接页面
    ```nginx
    server {
      listen 80;
      server_name localhost;

      location / {
        root /usr/share/nginx/html;
        // 如果在 root 路径下没有找到 index.html 回去找 index.htm
        index index.html index.htm;
      }

      error_page 500 502 /50x.html;
      location = /50x.html {
        // 在这个路径下寻找对应的 html 文件
        root /usr/share/nginx/html;
      }
    }
    ```
  - `include`：包含所有的`Content-Type`
  - `default_type`：默认的`Content-Type`
  - `log_format`：日志类型
  - `keepalive_timeout <时间长度>`：客户端和服务端超时的时间
  - `gzip`：打开`gzip`

### 虚拟主机
当我们希望一个 Nginx 来运行多套单独服务时，可以配置多个虚拟主机。有三种方式
1. 基于主机的多IP的方式
  - 多网卡多IP的方式，网卡1 -> IP1 网卡2 -> IP2，对主机配置要求高
  - 单网卡多IP的方式，网卡1 -> IP1 -> IP2，对网卡设置别名。通过`ip a add <Ip>/<掩码> dev <设备名>`的方式来增加IP地址，在增加之前使用`ping <IP>`的方式来判断这个域名是否存在服务，如果有服务就不能增加
2. 基于端口的配置方式
  - 端口其实和上面类似，只不过不用设置多 IP，`listen`监听的是不同端口号即可。需要处理的是端口冲突，需要先用命令查看，然后避开对应的端口
3. 基于多个host名称方式，常见的方式，也就是多域名方式。它只需要一个 IP 和一个端
虚拟主机的配置放在nginx的配置文件`/etc/nginx/nginx.conf`中`includes`属性中包括的路径中，之后通过`listen`属性配置到具体的IP 地址
首先需要修改本地的 host 文件，增加 IP 对应的 host 地址，但需要配置的 host 地址对应同一个 IP 地址，同时需要配置`server_name`
```
server {
  listen 80;
  server_name 2.imooc.com;
}
```

### 日志
日志类型包括`error.log`和`access_log`
- `error.log`：主要用于处理http请求的状态，以及 nginx 服务的状态，会按照不同的级别记录到`error.log`中
  - `error_log <log 位置> <error 级别(比如 warn)>`：在服务配置中增加字段
- `access_log`：依赖于`log_format`的配置，每一个信息都是对应一个变量，`log_format`就是将所有变量组装到一起
  - `access_log <log 位置> <log_format 的 name>`：在服务配置中增加字段
  - `Nginx 变量`：可在`log_format`中进行组合`format`输出日志信息
    - HTTP 请求变量
      - `arg_<变量名>`：输出信息
      - `http_<HEADER>`：`request`中的`head`
      - `sent_http_<HEADER>`：`response`中的`head`
    - 内置变量：比较多，进入官网了解
    - 自定义变量

### 模块
使用`nginx -V`，以`--with`开头的就是开启的模块
- Nginx官方模块，官方支持的模块
  - `--with-http_stub-status_module`：Nginx 的客户端状态，此时可以使用浏览器访问`<地址>/mystatus`查看nginx当前活跃的连接数
    - `Syntax`: `stub_status`
    - `Default`: --
    - `Context`: `server`，`location`
    ```nginx
    location /mystatus {
      stub_status;
    }
    ```
  - `--with-http_random_index_module`：目录中选择一个随机主页，也就是随机选择一个主页，给用户不同的感觉。但不会选择路径下的隐藏文件
    - `Syntax`：`random_index on | off`
    - `Default`：`random_index off`
    - `Context`：`location`
    ```nginx
    location / {
      random_index on;
    }
    ```
  - `--with-http_sub_module`：HTTP 内容替换
    - `Syntax`：`sub_filter <要替换的内容> <替换的内容>`
    - `Default`：--
    - `Context`：`http`，`server`，`location`
    或是，每次请求校验服务器内容是是否更新，如果更新返回的是新的
    - `Syntax`：`sub_filter_last_modified on | off`
    - `Default`：`sub_filter_last_modified off`
    - `Context`：`http`，`server`，`location`
    或是，匹配所有html代码里的第一个，还是匹配所有指定的字符串
    - `Syntax`：`sub_filter_once on | off`
    - `Default`：`sub_filter_once on`
    - `Context`：`http`，`server`，`location`
- 第三方模块

### 请求限制
HTTP协议的连接与请求
- HTTP1.0：TCP 不能复用
- HTTP1.1：顺序性 TCP 复用
- HTTP2.0：多路复用 TCP 复用
- `-limit_conn_module`：连接限制
  调用空间进行限制，`key`就是对什么进行限制，比如`IP`，`name`就是这个`zone`的名字，`size`就是大小
  - `Syntax`：`limit_conn_zone key zone=name:size
  - `Default`：--
  - `Context`：`http`
  以及，`zone`表示申请的`zone`名，`number`就是同一时间只能允许多少个
  - `Syntax`：`limit_conn zone number`
  - `Default`：--
  - `Context`：`http，server, location`
- `-limit_req_module`：请求限制
  也是调用空间进行限制，`name`就是`zone`名，`size`就是空间大小，`rate`就是对速率进行限制，通常以秒为单位
  - `Syntax`：`limit_req_zone key zone=name:size rate=rate;`
  - `Default`：--
  - `Context`：`http`
  ```nginx
  # $binary_remote_addr 表示的是客户端的地址
  limit_req_zone $binary_remote_addr zone=req_zone:1m rate=1r/s
  ```
  以及，`burst`表示是超过指定速率以后，遗留的三个请求放到下一秒请求，其他的会直接返回
  - `Syntax`：`limit_req zone=name burst=number] [nodelay]`
  - `Default`：--
  - `Context`：`http，server, location`

### 访问控制
- `http_access_module`：基于 IP 的访问控制，但是它只是对代理层进行限制，而对真正的客户端没有进行限制。
  - `http_x_forwarded_for`：常用的http变量，会显示代理以及发起连接的客户端 IP，可以一定程度的解决限制真正客户端问题
    - `http_x_forwarded_for = Client IP, Proxy(1) IP, Proxy(2) IP`
    - 局限性
      - 它是通过头`HTTP_X_FORWARD_FOR`来进行控制的，可能有厂商不使用这个头，或是传递一个别的值
      - 结合`geo`模块
      - 通过 HTTP 自定义变量传递，可以在 HTTP 头定义一个我们规定的变量，在联系上一级时把自己的`remote_addr`信息携带到上一级上，从而得到客户端信息
  `address`IP地址，`CIDR`网段，`unix`就是linux或是unix使用`socket`的方式的访问，`all`就是允许所有的
  - `Syntax`：`allow address | CIDR | unix: | all`
  - `Default`：--
  - `Context`：`http，server, location, limit_except`
  或是，也就是不允许访问的
  - `Syntax`：`deny address | CIDR | unix: | all`
  - `Default`：--
  - `Context`：`http，server, location, limit_except`
  
- `http_auth_basic_module`：基于用户的信任登录
  - `Syntax`：`auth_basic string | off`
  - `Default：`auth_basic off`
  - `Context`：`http，server, limit_except`
  以及，`file`就是用来存储用户名和密码信息的文件，需要按照 nginx 规定的格式。可以使用`htpasswd`来生成密码文件，`htpasswd -c <生成文件名> <用户名>`
  - `Syntax`：`auth_basic_user_file file`
  - `Default：--
  - `Context`：`http, location，server, limit_except`
  局限性
  1. 用户信息依赖文件方式
  2. 操作管理机械，效率低下
  解决方案
  1. nginx 结合`LUA`实现高效验证
  2. nginx 和 `LDAP`打通，利用`nginx-auth-ldap`模块
