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
