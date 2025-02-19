# mosdns分流sing-box安装指南
## 特解鸣谢:
* @Panicpanic
* @ovpavac
## 前言:
脚本根据手动流程写成
mosdns相关分流规则有O佬和ph佬两套配置
sing-box有三版内核分别:
* 官方内核
* puer sing-box内核 {支持机场}
* 曦灵X内核 {支持机场}

脚本内的sing-box和mosdns请分为两个系统sing-box最好使用VM安装，当然lxc也可以。mosdns lxc vm都可以
* 仅测试Ubuntu22.04安装，理论支持debian系统

支持:amd64 arm64
多合一脚本:
``` shell
wget https://raw.githubusercontent.com/herozmy/StoreHouse/refs/heads/latest/script/proxy.sh && bash proxy.sh
```
### 脚本安装流程
#########sing-box安装
安装sing-box/mihomo
![image](https://github.com/user-attachments/assets/abaa16a8-a0b9-432d-90f2-105cedec5bde)
选择内核：
![image](https://github.com/user-attachments/assets/500e9f93-b332-405c-ab35-c6234d6f17a5)
* sing-box官方内核有两种安装方式

1：源码编译安装
2: 二进制安装
按需选择
这里以：曦灵X核为例，其他内核同理
选择3，脚本自动安装内核，
![image](https://github.com/user-attachments/assets/da09fca4-77f8-40d9-85d6-2c73cb60e8a9)

这里需要输入你的机场订阅地址<脚本不会储存你的订阅地址，请放心使用>
输入y安装，写入订阅
![image](https://github.com/user-attachments/assets/2eddf4be-8cc6-4dc9-a0fa-1c60d39ec3d9)
安装到这里会提示你是否安装sing-box<mihomo暂时没写>回家配置,需要有公网ip地址，按需选择
* 输入你的`ddns`域名地址
* 协议端口号
* 密码
* 你的内网网段 例如：10.10.10.0/24

![image](https://github.com/user-attachments/assets/a0c90cd5-2458-4bb0-afdf-93cfcfe163c1)
选择分流规则：
* o佬规则：用于手机sing-box配置访问内网服务，如果想代理，需要自行写入你的代理协议
* ph佬规则：通过家里mosdns设置的规则分流，可以直接使用singbox节点，不用自行添加

![image](https://github.com/user-attachments/assets/e2caa5f3-2f6d-4f15-92b7-583133b9c41b)
这里以ph规则为例需要输入你的mosdns服务器地址
家里wifi bssid这里可以直接默认，需要生成以后在手机端开关singbox日志里显示你的wifi bssid，写入到配置里，安装完毕以后会在`/root/go_home.json
``` shell
请使用 systemctl start sing-box 启动服务
请使用 systemctl enable sing-box 设置开机自启
请使用 systemctl status sing-box 查看服务状态
请使用 systemctl restart sing-box 重启服务
请使用 systemctl stop sing-box 停止服务
请使用 systemctl disable sing-box 禁用开机自启
请使用 systemctl restart nftables 重启nftables
请使用 systemctl status nftables 查看nftables状态
请使用 systemctl restart sing-box-router 重启路由
请使用 systemctl status sing-box-router 查看路由状态
```

`生成配置自行下载放到手机sing-box运行
#######################################singbox安装完毕##########################################
### mosdns安装
脚本选择mosdns安装脚本
![image](https://github.com/user-attachments/assets/ee0da52b-6cab-419d-882c-330deecaf6ee)
输入sing-box/mihomo入站地址端口：输入上面singbox的地址后面加上端口号`6666`
![image](https://github.com/user-attachments/assets/66fb2692-82ca-4b8f-92ae-0a8abbcc7df7)
输入分流规则：
* O佬规则 
* PH佬规则 <优势：安装完成以后输入 http://ip:9099/graphic 可以显示简单ui页面>
![image](https://github.com/user-attachments/assets/e32568ae-c46c-42b7-bc48-a4d0fa4da2dd)
安装完成。
主路由设置需要配合同目录fakeip相关设置
https://github.com/herozmy/StoreHouse/blob/latest/docs/router/ros-fakeip%E5%88%86%E6%B5%81%E6%A8%A1%E5%BC%8F.md

