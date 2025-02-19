## ros从0开始脚本
### 初始化及基础配置
* RouterOS恢复原厂设置
``` shell
/system reset-configuration
```
### 配置网卡名称
请先确认lan wan口，自行更改ether 1 2的网口名称，多个网卡以此类推
``` shell
/interface set ether1 name=lan
/interface set ether2 name=wan
/interface pppoe-client add name=pppoe-out1 interface=wan disabled=no
```
### 建立bridge桥接
``` shell
/interface bridge add name=bridge1
/interface bridge port add bridge=bridge1 interface=lan
```
### 配置网络地址
``` shell
/ip address add interface=bridge1 address=10.10.10.1/24 network=10.10.10.10
#wan口地址为光猫地址，方便访问光猫，可不配置
/ip address add interface=wan address=192.168.1.2/24 network=192.168.1 disabled=yes
```
### 配置dhcp
``` shell
/ip pool add name=dhcpv4-pool1 ranges=10.10.10.100-10.10.10.200
/ip dhcp-server add name=dhcpv4-server1 interface=bridge1 address-pool=dhcpv4-pool1 lease-time=1d
/ip dhcp-server network add address=10.10.10.0/24 gateway=10.10.10.1 dns-server=10.10.10.1
```
配置dhcp范围为`100-200`，剩余地址为手动静态地址，可自行修改分配范围。
配置dhcp池为`dhcpv4-pool1`
配置dhcp分配的的dns服务和网关为`10.10.10.1`主路由地址，如果上面网络地址为其他地址需要自行修改
### 配置dns
``` shell
/ip dns set servers=223.5.5.5 allow-remote-requests=yes max-concurrent-queries=4096 max-concurrent-tcp-sessions=512 cache-size=8192 cache-max-ttl=04:00:00
```
配置dns地址为`223.5.5.5`
### 配置源地址伪装
``` shell
/ip firewall nat add action=masquerade chain=srcnat
```
### 配置ipv4防火墙规则
配置filter
``` shell
/ip firewall filter add action=accept chain=forward in-interface=bridge1
/ip firewall filter add action=accept chain=input in-interface=bridge1
/ip firewall filter add action=accept chain=input connection-state=established,related
/ip firewall filter add action=accept chain=input in-interface=!pppoe-out1 protocol=icmp
/ip firewall filter add action=drop chain=input connection-state=invalid in-interface=pppoe-out1
/ip firewall filter add action=drop chain=input in-interface=pppoe-out1 protocol=icmp
/ip firewall filter add action=drop chain=input dst-port=53,8291,80 in-interface=pppoe-out1 protocol=tcp
/ip firewall filter add action=drop chain=input dst-port=53,8291,80 in-interface=pppoe-out1 protocol=udp
/ip firewall filter add action=drop chain=input in-interface=pppoe-out1 src-address-list=BlockIP
/ip firewall filter add action=add-src-to-address-list address-list=BlockIP address-list-timeout=1w chain=input dst-port=!53,443,853 in-interface=pppoe-out1 protocol=tcp psd=21,5s,3,1
/ip firewall filter add action=add-src-to-address-list address-list=BlockIP address-list-timeout=1w chain=input dst-port=!53,443,853 in-interface=pppoe-out1 protocol=udp psd=21,5s,3,1
/ip firewall filter add action=drop chain=input src-address-list=BlockIP
```
配置mangle
``` shell
/ip firewall mangle add action=change-mss chain=forward protocol=tcp tcp-flags=syn new-mss=clamp-to-pmtu passthrough=yes
/ip firewall mangle add action=change-mss chain=output protocol=tcp tcp-flags=syn new-mss=clamp-to-pmtu passthrough=yes
```
``` shell
/ip firewall service-port disable ftp
/ip firewall service-port disable irc
/ip firewall service-port disable pptp
/ip firewall service-port disable rtsp
/ip firewall service-port disable sip
/ip firewall service-port disable tftp
```
### 关闭相关端口及服务
``` shell
/ip smb set enabled=no
/ip smb shares disable numbers=0
/ip ssh set forwarding-enabled=no
/ip socks set enabled=no
/ip upnp set enabled=no
/ip proxy set enabled=no
/ip service disable api
/ip service disable api-ssl
/ip service disable ftp
/ip service disable ssh
/ip service disable telnet
/ip service disable www
/ip service disable www-ssl
/tool bandwidth-server set enabled=no
```

#基础网络设置完成
配置pppoe-out1接口拨号，User填入宽带账号，Password填入宽带密码
现在可以正常上网了

