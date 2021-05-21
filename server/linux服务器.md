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
