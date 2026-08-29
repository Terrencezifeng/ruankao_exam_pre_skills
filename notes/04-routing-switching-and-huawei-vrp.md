# 路由交换与华为 VRP

本文配置示例按华为 VRP 口径。题干指定思科或其他厂商时，不能直接套用本页命令。

## 路由表决策顺序

1. 先选最长前缀匹配。
2. 对同一前缀，比较路由来源优先级；华为 VRP 中数值越小通常越优先。
3. 同一协议内部再按其度量值选路。
4. 满足等价条件时才可能形成等价负载分担。

管理距离/路由优先级衡量“路由来源可信偏好”，度量值衡量“该协议内部路径代价”，两者不能互换。

## 常见路由协议

| 协议 | 类型 | 算法/度量 | 高频点 |
|---|---|---|---|
| RIP | IGP、距离矢量 | 跳数，最大有效 15 跳 | 周期更新、收敛慢、环路抑制 |
| OSPF | IGP、链路状态 | Cost | 区域、LSA、SPF、DR/BDR、骨干区 0 |
| IS-IS | IGP、链路状态 | Cost | 分层、运营商网络常见 |
| BGP | EGP、路径矢量 | 路径属性 | AS 间路由、策略控制、TCP 179 |

RIP 防环关键词：最大跳数、水平分割、毒性逆转、触发更新、抑制计时。OSPF 邻接问题优先检查区域、网段、Hello/Dead、认证、网络类型和 MTU 等条件。

## 配置题通用流程

1. 从拓扑表格抄出设备角色、接口、IP/掩码和 VLAN。
2. 先完成二层与接口状态，再配置三层地址。
3. 再做路由、DHCP、NAT、ACL 等服务。
4. 最后用显示命令和端到端连通性验证。

## 华为 VRP 最小命令卡

### 基础与接口

```text
<Huawei> system-view
[Huawei] sysname R1
[R1] interface GigabitEthernet 0/0/0
[R1-GigabitEthernet0/0/0] ip address 192.0.2.1 255.255.255.0
[R1-GigabitEthernet0/0/0] undo shutdown
[R1-GigabitEthernet0/0/0] quit
```

接口编号以题干为准。不要因示例使用 `GigabitEthernet0/0/0` 就改写题目给出的端口。

### VLAN、Access 与 Trunk

```text
[SW1] vlan batch 10 20
[SW1] interface GigabitEthernet 0/0/1
[SW1-GigabitEthernet0/0/1] port link-type access
[SW1-GigabitEthernet0/0/1] port default vlan 10
[SW1-GigabitEthernet0/0/1] quit
[SW1] interface GigabitEthernet 0/0/24
[SW1-GigabitEthernet0/0/24] port link-type trunk
[SW1-GigabitEthernet0/0/24] port trunk allow-pass vlan 10 20
```

### VLANIF 三层网关

```text
[SW1] interface Vlanif 10
[SW1-Vlanif10] ip address 192.168.10.1 255.255.255.0
```

### 静态路由

```text
[R1] ip route-static 203.0.113.0 255.255.255.0 192.0.2.2
[R1] ip route-static 0.0.0.0 0.0.0.0 192.0.2.2
```

### OSPF

```text
[R1] ospf 1 router-id 1.1.1.1
[R1-ospf-1] area 0
[R1-ospf-1-area-0.0.0.0] network 192.0.2.0 0.0.0.255
```

OSPF `network` 后使用反掩码；它匹配本机接口地址并把接口纳入区域，不是直接“发布任意远端网段”。

### DHCP 全局地址池

```text
[R1] dhcp enable
[R1] ip pool LAN
[R1-ip-pool-LAN] network 192.168.10.0 mask 255.255.255.0
[R1-ip-pool-LAN] gateway-list 192.168.10.1
[R1-ip-pool-LAN] dns-list 192.0.2.53
[R1-ip-pool-LAN] quit
[R1] interface GigabitEthernet 0/0/1
[R1-GigabitEthernet0/0/1] dhcp select global
```

### 基本 ACL 与接口应用

```text
[R1] acl number 2000
[R1-acl-basic-2000] rule 5 permit source 192.168.10.0 0.0.0.255
[R1-acl-basic-2000] quit
[R1] interface GigabitEthernet 0/0/0
[R1-GigabitEthernet0/0/0] traffic-filter inbound acl 2000
```

基本 ACL 主要匹配源地址；高级 ACL 才能进一步匹配目的、协议和端口。方向必须站在接口视角判断。

### Easy IP/NAT Outbound 常见形式

```text
[R1] acl number 2000
[R1-acl-basic-2000] rule 5 permit source 192.168.10.0 0.0.0.255
[R1] interface GigabitEthernet 0/0/0
[R1-GigabitEthernet0/0/0] nat outbound 2000
```

此处假设 `GigabitEthernet0/0/0` 是公网出口且已配置公网地址。题目若要求地址池、静态 NAT 或服务器映射，命令结构不同。

## 验证命令

```text
display current-configuration
display interface brief
display ip interface brief
display vlan
display mac-address
display ip routing-table
display ospf peer
display acl 2000
ping 203.0.113.1
```

做配置填空时先给精确空格内容，再在解释中补上下文；不要把提示符、无关命令或另一厂商语法填进答案。

## MPLS VPN 一页理解

- CE：客户边缘设备，与运营商 PE 相连。
- PE：运营商边缘设备，维护客户 VRF，并在客户路由与 MPLS 骨干间衔接。
- P：运营商骨干设备，主要依据外层标签转发，不需要保存每个客户的 VPN 路由。
- 常见双层标签：外层用于把报文送到出口 PE，内层用于在出口 PE 识别 VPN/转发表上下文。

