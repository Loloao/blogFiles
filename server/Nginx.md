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
