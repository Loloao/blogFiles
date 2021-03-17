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
