# linux 服务器

## 连接 Internet
### 网络配置相关文件
- `/etc/sysconfig/network-scripts/ifcfg-eth0`：用于查看`IP`、`Netmask`、`DHCP`、`Gateway`等
  - `DEVICE`：网卡的名称，这个值需要与`ifcfg`后的值相同
  - `BOOTRPROTO`：是否使用`dhcp`，如果是手动分配IP，需要输入`static`或`none`
  - `HWADDR`：是否加入网卡`MAC`地址，可以区分不同的网卡
  - `IPADDR`：IP地址
  - `NETMASK`：子网掩码
  - `ONBOOT`：要不要默认启动此端口
  - `GATEWAY`：网关地址，代表整个主机系统的`Default Gateway`
  - `NM_CONTROLLED`：额外的网管软件，建议取消
- `/etc/sysconfig/network`：主机名
  - `NETWORKING`：要不要使用网络
  - `NETWORKING_IPV6`：是否支持 IPV6
  - `HOSTNAME`：主机名
- `/etc/resolv.conf`：`Name Server DNS`的 IP地址可以直接输入中国电信的DNS`202.96.199.133`
- `/etc/hosts`：私有 IP 主机名别名
- `/etc/services`：记录构建在`TP/IP`上的各种协议，包括`HTTP`、`FTP`、`SSH`、`Telnet`等服务所定义的`port number`
- `/etc/protocols`：这个文件是定义`IP`数据包协议的相关数据
### 网络方面的启动命令
- `/etc/init.d/network restart`：可以一口气重新启动整个网络的参数
- `ifup eth0 (ifdown eth0)`：启动或是关闭某个网络接口，这两个`script`会主动到`/etc/sysconfig/network-scripts/`目录下读取适当的配置文件来处理
- `ifconfig eth0`：用于查看配置是否正确
- `route -n`：查看路由定义是否正确
- `dig <域名>`：用于查看解析域名的 DNS 服务器
- `hostname`：获取主机名

### 无线网络
在无线网络中，也需要一个接收信号的设备，就是无线接入点`AP(Wireless Access Point)`比如`TP-Link`路由器，另一个设备就是装在计算机主机上的无线网卡了
- `iwconfig`：用于无线网络设置的一个命令，与`ifconfig`类似，可以查看模块与对应的网卡代号，如果发现加粗字体，就代表该网络接口使用的是无线网卡
- `ifconfig ra0 up`：其中`ra0`就是网卡别名，用于启动无线网卡
- `iwlist ra0 scan`：用于搜索整个区域内的无线接入点
- `/etc/sysconfig/network-scripts/ifup-wireless`：用于查看无线网卡设置
网卡设置
- `DEVICE`：设备名，比如上面的`ra0`
- `BOOTPROTO`：类似有线
- `ONBOOT`：是否开机启动
- `RATE`：严格指定传输的速率，与`iwconfig`相同
启动无线网卡`ifup ra0`

### 常见问题说明
- 如果两部主机之间无法查询到正确的主机名与IP的对应信息，将可能发生持续查询主机名对应的动作，这个动作一般持续30~60秒，此时可以在`/etc/hosts`中给予内部的每台主机一个名称与 IP 的对应即可
- 域名无法解析，需要进入`/etc/resolv.conf`文件中配置
- 如果使用拨号，不要在`ifcfg-eth0`中指定`GATEWAY`或是`GATEWAYDEV`等变量

## 常用的网络命令
### 设置网络参数的命令
- `ifconfig`：没有参数可以查看所有网络接口
  - `ifconfig <interface>`：可以查看对应网卡的数据
  - `ifconfig <interface> <up|down>`：可以手动启动、查看与修改网络接口的相关参数，包括`IP`参数以及`MTU`等，其中`interface`为网卡接口名称
  - `ifconfig <interface> <optioins>`：`options`为可以使用的参数，包括
    - `up, down`：启动或关闭该网络接口
    - `mtu`：可以设置不同的`MTU`数值
    - `netmask`：就是子网掩码
    - `broadcast`：就是广播地址
- `ifup` | `ifdown`：这两个其实是`script`，它会直接到`/etc/sysconfig/network-scripts`目录下查找到对应的配置文件，比如`ifup ehto`，它会读取`ifcfg-eth0`这个文件的内容，然后加以设置。如果以`ifconfig eth0`的方式来设置或是修改了网络接口后，就无法再以`ifdown eth0`的方式来关闭

- `route`：用于查看路由
两条主机一定要有路由才能够互通`TCP/IP`协议，否则无法连接，一般来说，只要有网络接口，该接口就会产生一个路由
  - `-n`：不要使用通信协议或主机名，直接使用`IP`或`port number`
  - `-ee`：显示更详细的信息
  - `-net`：表示后面接的路由为一个网络
  - `-host`：表示后面接的为连接到单部主机的路由
  - `netmask`：与网络有关，可以设置`netmask`决定网卡的大小
  - `gw`：`gateway`的简写，后续接的是 IP 的数值，与`dev`不同
  - `dev`：如果只是要指定由那一块网卡连接出去，使用这个设置，后面接`eth0`等
它能够有以下几个参数显示
- `Destination` | `Genmask`：这两个参数分别就是`network`与`netmask`
- `Gateway`：改完落实通过哪个`Gateway`连接出去，如果显示`0.0.0.0`则表示该路由是直接由本机传送；如果显示IP的话，表示该路由需要经过路由器(网关)的帮忙才能够发送出去
- `Flags`：总共有多个标志，代表的意义如下
  - `U(route is up)`：该路由是启动的
  - `H(target is a host)`：目标是一台主机而非网络
  - `G(use gateway)`：需要通过外部的主机来传递数据包
  - `R(reinstate route for dynamic routing)`：使用动态路由时，恢复路由信息的标志
  - `D(dynamiclly installed by daemon or redirect)`：动态路由
  - `M(modified from routing daemon or redirect)`：路由已经被修改了
  - `!(reject route)`：这个路由不会被接受，用来阻止不安全的网络
- `iface`：这个路由传递数据包的接口

- `ip <option> <动作> <命令>`：这个命令综合了`ifconfig`和`route`两个命令
  - `-s`：显示出设备的统计数据，例如接受数据包的总数等
  关于接口设备的相关设置`ip link`，包括`MTU`以及改完络接口的`MAC`等，当然也可以启动`up`和`down`某个网络接口
    - `ip -s link show`：单纯查看该设备相关信息
    - `ip link set <device> <动作与参数>`：其中动作与参数包括下面的动作，但是使用前需要关闭网卡
      - `up` | `down`：启动或关闭某个接口
      - `address`：如果这个设别可以更改`MAC`的话，用这个参数修改
      - `name`：给予这个设备的一个特殊名字
      - `mtu`：就是最大传输单元
    - `ip address show`：查看IP参数
    - `ip address <add | del> <IP参数> <dev 设备名> <相关参数>`：进行相关参数的增加或删除设置，相关参数有下面这些
      - `broadcast`：设置广播地址，如果设置值是`+`表示让系统自动计算
      - `label`：就是这个设备的别名，比如`eth0:0`
      - `scope`：这个选项的参数

- `iwlist`和`iwconfig`可以设置无线网络
- `dhclient`：如果使用`DHCP`协议，则这个命令才是真正发送`DHCP`请求的
- `ping`主要通过`ICMP`数据包来进行整个网络的状态报告，其中最重要的就是`ICMP type0、8`这两个类型，它还是使用`IP`数据包来传送`ICMP`数据包，而`IP`数据包里有个相当重要的`TTL`属性
  - `-c`：后面接数字，表示执行`ping`的次数
  - `-n`：在输出数据时不进行`IP`与主机名的反差，直接使用`IP`输出
  - `-s <num>`：发送出去的`ICMP`数据包的大小，默认为`56 bytes`，不过可以放大此数值
  - `-t <num>`：`TTL`的数值，默认为`255`，每经过一个节点就会少一
  - `-W <num>`：等待响应对方主机的秒数
  - `-M [do|dont]`：主要在检测网络的`MTU`数值大小，两个常见的项目时：
    `do`：代表传送一个`DF(Don't Fragment)`标志，让数据包不能重新拆包与打包
    `dont`：代表不要传送`DF`标志，表示数据包可以在其他主机上拆包与打包
  显示的数据有以下信息
  - `64 bytes`：表示这次发送的`ICMP`数据包大小为`64 bytes`
  - `icmp_seq=1`：`ICMP`的检测次数
  - `ttl=243`：`TTL`和`IP`数据包内的`TTL`是相同的，没经过一个带有`MAC`的节点，比如`router`、`bridge`时，`TTL`就会减少1，默认为`255`，但如果是在同一个网络内，默认为`64`
  - `time=15.4ms`：响应时间，越小越好
- `traceroute <IP>`：此命令可以跟踪两台主机之间通过的各个节点的好坏
  - `-n`：可以不必进行主机的名称解析，单纯用`IP`，速度较快
  - `-U`：使用`UDP`的端口`33434`来进行检测，这是默认的检测协议
  - `-I`：使用`ICMP`的方式来进行检测
  - `-T`：使用`TCP`的方式进行检测，一般使用端口`80`进行检测
  - `-w`：如果对方主机在几秒钟内没有回应就声明不通，默认`5`秒
  - `-p <端口号>`：如果不想使用`UDP`或是`TCP`的默认端口测试，则可在此改变端口号
  - `-i <设备>`：用子啊比较复杂的环境，如果网络很多很复杂时，参会用到这个参数
  - `-g <路由>`：和`-i`的参数相似，只是`-g`后面接的是`gateway`的`IP`
- `netstat`：查看本机的网络连接与后门
  与路由有关的参数
  - `-r`：列出路由表`route table`，功能如同`route`这个命令
  - `-n`：不使用主机名与服务名称，使用`IP`与`port number`，如同`route -n`与网络接口有关的参数
  与网络有关的参数
  - `-a`：列出所有连接状态，包括`tcp/udp/unix socket`等
  - `-t`：仅列出`TCP`数据包的连接
  - `-u`：仅列出`UDP`数据包的连接
  - `-l`：仅列出已在`listen`监听的服务的网络状态
  - `-p`：列出`PID`与`Program`的文件名
  - `-c <num>`：可以设置几秒钟后更新一次
- `host`：这个命令可以查出是某个主机名的`IP`，这个`IP`是通过`/etc/resolv.conf`那个文件内的DNS服务器IP
  - `-a`：列出各主机详细的各项主机名设置数据
- `nslookup`：这条命令和`host`基本是一样，也是使用`/etc/resolv.conf`文件来作为DNS服务器的来源选择
  - `-query=type`：查询单类型，除了传统的`IP`与主机名对应外，`DNS`还有很多信息：所以我们可以查询很多不同的信息，比如`mx`、`cname`

### 远程连接命令与即时通信软件
远程连接就是在不同计算机之间进行登录
- `telnet <host | IP port>`：他不但可以直接连接到服务器上，还可以连接到`BBS`，但是数据在`telnet`上是通过明文传输
- `FTP`连接软件
  - `ftp <host | IP> <port>`：用于处理`FTP`服务器的下载数据
  - `lftp`：默认使用你们登录`FTP`服务器，可以使用类似网址的方式取得数据
    - `-p`：后面可以接上远程`FTP`主机提供`port`
    - `-u`：后面接上账号和密码，就能够连接上远程主机了，如果没有账号密码，会适应`anonymous`登录
    - `-f`：可以将命令写入监本中，这样可以帮助进行`shell script`的自动处理
    - `-c`：后面直接加上所需命令

### 文字接口网页浏览
- `links <url>`：文字接口浏览器，这个命令可以来浏览网页但最大的功能是可以使用它来茶语`Linux`本机上以`HTML`语法写成的文件数据
  - `-anonymous [0|1]`：是否使用匿名登录
  - `-dump [0|1]`：是否将网页数据直接输出到标准输出而非`links`软件功能
  - `-dump_charset`：谋面接想要通过`dump`输出到屏幕的语系编码，简体中文使用`cp936`
- `wget <网址>`；能够进行网页数据的取得
  - `--http-user=username`：当连接的网站需要登录时输入用户名
  - `--http-passwork=password`：当连接的网站需要登录时输入密码
  - `--quiet`：不要显示`wget`在捕获数据时的显示信息

### 数据包捕获
- `tcpdump`：它不但可以分析数据包的流向，连数据包的内容也可以进行监听，如果使用明文传输的话，在`Router`或`Hub`上就会被别人监听
  - `-A`：数据包的内容以`ASCII`显示，通常用来抓取`WWW`的网页数据包数据
  - `-e`：使用数据链路层`OSI`第二层的`MAC`数据包来显示
  - `-nn`：直接以`IP`和`port number`显示，而非主机名与服务名称
  - `-q`：仅列出较短的数据包信息，第一行比较精简
  - `-X`：可以列出十六进制以及`ASCII`的数据包内容
  - `-i`：后面接要坚挺的网络接口，比如`eth0`，`lo`，`ppp0`等界面
  - `-w`：如果想要将监听所得的数据包数据存储下来，用这个参数就行，后面接文件名
  - `-r`：从后面接的文件将数据包数据读出来，这个文件是已经存在的文件，并且这个文件是由`-w`制作出来的
  - `-c`：监听的数据包数，如果没有这个参数，`tcpdump`会不断地监听
- `wireshark`：这个软件是图形接口的数据包捕获器
- `nc <IP | host> <port>`：可以作为某些服务的检测，因为它可以连接到某个`port`来进行通信，还可自行启动一个`port`来监听其他用户的连接
  - `-l`：作为监听之用，也就是打开一个`port`来监听用户连接
  - `-u`：不使用`TCP`而是使用`UDP`作为连接的数据包状态
