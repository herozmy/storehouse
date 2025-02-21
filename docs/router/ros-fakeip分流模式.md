## mikrotik fakeip配置

# fakeip v4配置
### 添加`proxy_v4`标签
``` shell
/routing table add name=proxy-v4 fib
```
### 地址列表添加fakeip v4段及添加tg v4网段及奈菲v4网段
``` shell
/ip firewall address-list add list=proxy_ipv4 address=28.0.0.0/8
/ip firewall address-list add list=proxy_ipv4 address=207.45.72.0/22
/ip firewall address-list add list=proxy_ipv4 address=208.75.76.0/22
/ip firewall address-list add list=proxy_ipv4 address=210.0.153.0/24
/ip firewall address-list add list=proxy_ipv4 address=91.108.56.0/22
/ip firewall address-list add list=proxy_ipv4 address=91.108.4.0/22
/ip firewall address-list add list=proxy_ipv4 address=91.108.8.0/22
/ip firewall address-list add list=proxy_ipv4 address=91.108.16.0/22
/ip firewall address-list add list=proxy_ipv4 address=91.108.12.0/22
/ip firewall address-list add list=proxy_ipv4 address=149.154.160.0/20
/ip firewall address-list add list=proxy_ipv4 address=91.105.192.0/23
/ip firewall address-list add list=proxy_ipv4 address=91.108.20.0/22
/ip firewall address-list add list=proxy_ipv4 address=185.76.151.0/24
/ip firewall address-list add list=proxy_ipv4 address=95.161.64.0/20
/ip firewall address-list add list=proxy_ipv4 address=8.8.8.8/32
/ip firewall address-list add list=proxy_ipv4 address=8.8.4.4/32
/ip firewall address-list add list=proxy_ipv4 address=1.1.1.1/32
/ip firewall address-list add list=proxy_ipv4 address=1.0.0.1/32
```
为走向`proxy_ipv4`地址标记
``` shell
/ip firewall mangle add action=mark-routing chain=prerouting dst-address-list=proxy_ipv4 new-routing-mark=proxy-v4 passthrough=yes
```
为带有`proxy-v4`标记的流量转发至proxy代理服务（sing-box或者mihomo）
``` shell
/ip route add disabled=no distance=1 dst-address=0.0.0.0/0 gateway=10.10.10.254 routing-table=proxy-v4 scope=30 suppress-hw-offload=no target-scope=10
```
`10.10.10.254`为`sing-box或者mihomo地址`
`proxy-v4`为上面设置的标记需要代理的流量

### 伪装中排除标记流量，用于在sing-box或者mihomo ui中显示源地址ip
![image](https://github.com/user-attachments/assets/cfb992b0-a2ba-45d1-b2a8-8588996baf94)

# fakeip v6配置
### 添加`proxy-v6`标签
``` shell
/routing table add name=proxy-v6 fib
```
### 地址列表添加fakeip及时tg v6段
``` shell
/ipv6 firewall address-list add address=f2b0::/18 list=proxy_ipv6
/ipv6 firewall address-list add address=2001:b28:f23d::/48 list=proxy_ipv6
/ipv6 firewall address-list add address=2001:b28:f23f::/48 list=proxy_ipv6
/ipv6 firewall address-list add address=2001:67c:4e8::/48 list=proxy_ipv6
/ipv6 firewall address-list add address=2001:b28:f23c::/48 list=proxy_ipv6
/ipv6 firewall address-list add address=2a0a:f280::/32 list=proxy_ipv6
```
为走向`proxy_ipv6`地址标记
``` shell
/ipv6 firewall mangle add action=mark-routing chain=prerouting dst-address-list=proxy_ipv6 new-routing-mark=proxy-v6
```
为带有`pmihomo6`标记的流量转发至proxy代理服务（sing-box或者mihomo）
``` shell
/ipv6 route add dst-address=::/0 gateway=dc00::2222 routing-table=proxy-v6
```
`dc00::2222`为`sing-box或者mihomo地址 v6地址`
`proxy-v6`为上面设置的标记需要代理的流量
### 添加v6路由规则
``` shell
/routing rule add action=lookup-only-in-table comment= "for ipv6 mangle effective in route" disabled=no routing-mark=sing-box-v6 table=proxy-v6
```
### 伪装中排除标记流量，用于在sing-box或者mihomo ui中显示源地址ip
![image](https://github.com/user-attachments/assets/ba010fb0-e269-4009-b1df-ee245351f4de)

### enjoy 大工告成那
