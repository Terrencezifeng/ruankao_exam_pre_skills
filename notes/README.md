# 网络工程师备考笔记

本目录把外部资料重新整理为适合检索、做题和错题回炉的原创速记。知识口径优先级为：当期官方通知与考试大纲 > 第 6 版官方教程 > RFC/厂商文档 > 有解析的题目 > 网络笔记。

外部 PDF 只作为选题线索。发现冲突时，不以文件名中的“必考”“必过”作为正确性依据。

## 按问题查笔记

| 你正在解决的问题 | 先看 | 对应冲刺周 |
|---|---|---|
| 带宽、时延、编码、CRC、海明码 | [网络基础与数据通信](01-network-basics-and-data-communication.md)、[计算速查](08-calculation-quick-reference.md) | 第 1 周 |
| Ethernet、VLAN、STP、链路聚合、WLAN | [局域网与无线网络](02-lan-vlan-stp-and-wlan.md) | 第 2 周 |
| IPv4/IPv6、ARP、ICMP、TCP/UDP、常见端口 | [IP 与传输层](03-ip-and-transport.md) | 第 3 周 |
| 路由协议、交换和华为配置 | [路由交换与华为 VRP](04-routing-switching-and-huawei-vrp.md) | 第 3—4 周 |
| 密码学、PKI、ACL、防火墙、VPN | [网络安全](05-network-security.md) | 第 5 周 |
| Linux、DNS、DHCP、Web、邮件 | [Linux 与网络服务](06-linux-and-network-services.md) | 第 4—5 周 |
| SNMP、故障排查、规划设计、可靠性 | [网络管理与规划](07-management-troubleshooting-and-design.md) | 第 6 周 |
| 专业英语 | [专业英语](09-professional-english.md) | 每周穿插 |

## 使用方式

1. 学新知识时先读对应章节的“考场结论”，再做 3—5 道同类题。
2. 做错题时把错误原因写入 `study/mistakes.md`，并链接到最小相关小节。
3. 配置题先确认厂商。本文命令示例只采用华为 VRP；思科题另按题干解析，不混用语法。
4. 计算题必须写单位并做反向校验；只背公式、不检查数量级不算掌握。
5. 涉及报名、考试时间、产品版本或命令差异时重新核对官方来源。

## 来源与校正说明

- 外部资料清单、页数和用途见 [来源目录](source-catalog.md)。
- 部分外部笔记年代较早，包含过时设备、旧版服务器系统、思科旧命令及少量事实错误。本目录已按当前教材主线重排，并把需要谨慎使用的内容标出。
- 协议字段以 RFC 为校验依据：[IPv4](https://www.rfc-editor.org/rfc/rfc791.html)、[IPv6](https://www.rfc-editor.org/rfc/rfc8200.html)、[TCP](https://www.rfc-editor.org/rfc/rfc9293.html)、[UDP](https://www.rfc-editor.org/rfc/rfc768.html)、[ICMP](https://www.rfc-editor.org/rfc/rfc792.html)、[ARP](https://www.rfc-editor.org/rfc/rfc826.html)。

