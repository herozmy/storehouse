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
wget https://raw.githubusercontent.com/herozmy/StoreHouse/latest/script/install.sh && bash install.sh
```
