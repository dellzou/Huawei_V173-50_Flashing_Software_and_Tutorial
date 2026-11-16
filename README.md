# 华为 V173-50 光猫使能工具与教程

用于华为 V173-50（FTTR 主光猫，GPON / XG-PON 自适应，R24 固件）的维修使能、开启 Telnet、恢复华为公版界面。

## 配套教程

完整操作步骤、115 KB 装备包拆解、OBSCTool 日志逐字段解读和排障表在这篇文章：

**https://xiaozou123.cn/huawei-v173-50-ont-enable-r24-china/**

## 文件清单

| 文件 | 大小 | 用途 |
|---|---|---|
| `ONT_V100R002C00SPC253.exe` | 9,101,312 | OBSCTool，2025-04-30 编译，刷装备包用 |
| `V173-50_enable_package.bin` | 115,403 | R24 装备包，包内签名 `V500R024C00SPC110B002` |
| `HW_Dollar2.exe` | 161,280 | 编辑 `hw_boardinfo`，2.2.0.2104 |
| `tftpd64.exe` | 343,040 | TFTP 服务端，取回/回传 boardinfo |

### SHA256

```
c882032e2dcd1a3ea2fc5e359e7c22a94d024fb0ae7e7be2f1cefd341796f8ec  ONT_V100R002C00SPC253.exe
d25e86d67757753b2100fc917a3e06d520670da1bc4405899f0170ca5f90db32  V173-50_enable_package.bin
d3110469d8cccc7ca479105393e047faef74c74cfb34d174deece84153ac1cc8  HW_Dollar2.exe
2f5de838cf3079723f1697e5fac2f75fad4b98cd81b165f87cdfa99ded22d801  tftpd64.exe
```

## 使用顺序

1. 复位光猫（不接光纤），或使用超密登录后台开启 LAN 口 Telnet。
2. 电脑网卡设 `192.168.1.2`，关防火墙，打开 `ONT_V100R002C00SPC253.exe`。
3. 升级页签选 `V173-50_enable_package.bin`，启动，等成功总数变成 1，断电重启。
4. 网卡改 `192.168.100.2`，`telnet 192.168.100.1`，账号 `root`，密码 `admin`。
5. `su` → `Shell` → `cd /mnt/jffs2`，用 `tftpd64.exe` 取回两份 boardinfo。
6. `HW_Dollar2.exe` 把 `0x0000001b` 改成 `CHINA`，V5 模式保存，两个文件都改。
7. 回传覆盖，`EquipMode.sh off`，重启，登录 `telecomadmin / admintelecom`。

## 注意

- 这个装备包**不是完整固件**，只有 115 KB，作用是把设备切到公版装备模式，不替换系统。本仓库没有 V173-50 的完整固件和地区包。
- 包内硬件校验全部关闭，刷错机型也可能显示成功。刷之前务必确认型号和固件代际。
- 工具会发原始组播包、监听端口，被杀软报毒是常态，建议在虚拟机或冰点还原环境里运行。
- 仅用于自己拥有或获得授权的设备。刷机有风险，操作前请做好 boardinfo 和分区备份。
