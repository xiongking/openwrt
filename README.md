# openwrt
the latest available release of openwrt-x86-64 for docker; 最新OpenWRT-X86-64官方纯净版镜像 

<https://hub.docker.com/r/daxiong828/openwrt>

Openwrt-x86-64 for docker，Compiled directly from the official pure version of the image.

OpenWRT-X86-64，编译自官方纯净版镜像docker版

the latest image version is : 22.03.5

最新镜像版本号：22.03.5

Check the latest version number: <https://downloads.openwrt.org/>

这里查看官方镜像最新版本： <https://downloads.openwrt.org/>


## create your macvlan at first

## 首先为docker创建一个macvlan

```
docker network create -d macvlan --subnet=10.0.0.0/24 --gateway=10.0.0.1 -o parent=enp1s0 macvlan_proxy 
```
## configure a network interface into promiscuous mode

## 设置网卡为混淆模式

```
ip link set eth0 promisc on
```
## docker run

## 运行docker

```
docker run -d --restart unless-stopped --network macvlan_proxy --privileged --name openwrt daxiong828/openwrt /sbin/init
```
## or docker-compose.yml

## 或者使用docker-compose

```
openwrt:
 container_name: openwrt
 image: daxiong828/openwrt
 restart: always
 command: /sbin/init
 privileged: true
 environment:
      - PUID=$PUID
      - PGID=$PGID
      - TZ=$TZ
    networks:
      macvlan_proxy:
        ipv4_address: 10.0.0.100<your route IP 这里是网关ip>
```
## enter the docker

## 进入docker

```
docker exec -it openwrt ash
```
## then, check your network :

## 然后检查网络：

```
ifconfig
```
## edit the network configiure:

##  编辑网络配置：

```
vi /etc/config/network
```
## at last, restart network:

##  最后重启网络配置使其生效：

```
/etc/init.d/network restart
```
## run as:

## 运行过程参考如下：

```
BusyBox v1.30.1 () built-in shell (ash)
/ # ifconfig
br-lan Link encap:Ethernet HWaddr 02:42:0A:00:00:65
inet addr:192.168.1.1 Bcast:192.168.1.255 Mask:255.255.255.0
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:332 errors:0 dropped:0 overruns:0 frame:0
TX packets:2 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000 
RX bytes:71999 (70.3 KiB) TX bytes:684 (684.0 B)
eth0 Link encap:Ethernet HWaddr 02:42:0A:00:00:65
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:338 errors:0 dropped:3 overruns:0 frame:0
TX packets:3 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:0 
RX bytes:78583 (76.7 KiB) TX bytes:1727 (1.6 KiB)
lo Link encap:Local Loopback
inet addr:127.0.0.1 Mask:255.0.0.0
inet6 addr: ::1/128 Scope:Host
UP LOOPBACK RUNNING MTU:65536 Metric:1
RX packets:64 errors:0 dropped:0 overruns:0 frame:0
TX packets:64 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000 
RX bytes:4352 (4.2 KiB) TX bytes:4352 (4.2 KiB)

/ # vi /etc/config/network

config interface 'loopback'
option ifname 'lo'
option proto 'static'
option ipaddr '127.0.0.1'
option netmask '255.0.0.0'
config interface 'lan'
option ifname 'eth0'         
option proto 'static'
option ipaddr '10.0.0.100' 
option netmask '255.255.255.0'

option delegate '0'      
option gateway '10.0.0.1'   
option dns '223.5.5.5 114.114.114.114'

/ # /etc/init.d/network restart

/ # ping www.baidu.com
PING www.baidu.com (61.135.185.32): 56 data bytes
64 bytes from 61.135.185.32: seq=0 ttl=57 time=3.792 ms
64 bytes from 61.135.185.32: seq=1 ttl=57 time=2.765 ms
--- www.baidu.com ping statistics ---
2 packets transmitted, 2 packets received, 0% packet loss
round-trip min/avg/max = 2.765/3.278/3.792 ms
```
