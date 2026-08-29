# Linux 与网络服务

外部笔记大量使用 `ifconfig`、`route`、`netstat` 和 SysV `service`。考试若给出旧环境，应识别这些命令；实际新系统优先掌握 iproute2 与 systemd。编辑配置文件默认使用 `vim`。

## 命令新旧对照

| 目的 | 现代常用 | 传统题目可能出现 |
|---|---|---|
| 查看地址/接口 | `ip address`、`ip link` | `ifconfig` |
| 查看/配置路由 | `ip route` | `route` |
| 邻居表 | `ip neigh` | `arp` |
| Socket/监听端口 | `ss -lntup` | `netstat -lntup` |
| 服务管理 | `systemctl` | `service`、`chkconfig` |
| 日志 | `journalctl` | `/var/log/*` |

常用诊断：

```bash
ip -br address
ip route
ip neigh
ss -lntup
ping -c 4 192.0.2.1
traceroute 203.0.113.10
dig example.com
curl -I https://example.com
```

修改配置时先备份并检查发行版口径，例如：

```bash
sudo vim /etc/hosts
sudo systemctl restart named
sudo systemctl status named
```

服务名和路径可能是 `named`、`bind9` 或其他名称，题干指定什么就采用什么。

## 目录速记

- `/etc`：系统级配置。
- `/var`：经常变化的数据，如日志、缓存、队列。
- `/home`：普通用户主目录。
- `/root`：root 用户主目录。
- `/usr`：用户空间程序、库和共享数据。
- `/dev`：设备文件。
- `/proc`、`/sys`：内核和设备信息的虚拟文件系统。
- `/tmp`：临时文件，生命周期由系统策略决定。

## DNS

- 递归查询：被查询服务器负责给请求者最终答案或错误。
- 迭代查询：服务器返回自己掌握的最佳答案或下一步权威服务器线索。
- 常见记录：`A` 对应 IPv4，`AAAA` 对应 IPv6，`CNAME` 是别名，`MX` 指示邮件交换器，`NS` 指示权威服务器，`PTR` 用于反向解析。
- `/etc/hosts` 是本地主机名静态映射；系统解析顺序通常由 NSS 配置等机制决定。旧资料把 `/etc/host.conf` 作为唯一解析顺序依据，不适合所有现代发行版。

排查顺序：客户端 DNS 地址 → 本地静态映射/缓存 → 递归服务器可达性 → 委派和权威记录 → 防火墙/UDP-TCP 53。

## DHCP

- DHCPv4 服务器 UDP 67，客户端 UDP 68。
- 首次获取常见 DORA：Discover → Offer → Request → ACK。
- 地址池至少关注网络、掩码、网关、DNS、租期和排除地址。
- 中继用于跨广播域转发 DHCP 请求；没有中继时，普通路由器不会直接转发客户端广播。

故障关键词：地址池耗尽、网关/掩码错误、中继地址错误、VLAN/Trunk 不通、UDP 67/68 被拦截、存在非法 DHCP 服务器。

## Web 与邮件

- Web 服务排查：域名解析、TCP 端口监听、防火墙、虚拟主机/站点绑定、文件权限、应用日志、上游代理。
- HTTP 默认 80，HTTPS 默认 443；HTTPS 还需检查证书名称、信任链和有效期。
- SMTP 用于邮件提交/传递；POP3、IMAP 用于客户端读取。邮件收发题还常结合 DNS MX 记录。

## 文件与权限

`r=4`、`w=2`、`x=1`，三组分别对应所有者、所属组、其他用户。例如 `chmod 640 file` 表示所有者读写、组只读、其他无权限。

服务无法读取文件时同时检查：Unix 权限、所属用户/组、父目录执行权限、安全模块策略及服务沙箱，而不是一律使用 `chmod 777`。

