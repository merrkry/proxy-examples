# [Hysteria 2](https://v2.hysteria.network/zh/)

- 基于 QUIC，实现连接迁移、多路复用等功能
- 端口跳跃，避免 QoS
- 独有的 Brutal 流控
- HTTP/3 伪装能力

## 服务端配置

```json
// sing-box
{
  "type": "hysteria2",
  "listen": "::",
  "listen_port": 443,
  "users": [
    {
      "password": ""
    }
  ],
  "tls": {
    "enabled": true,
    "alpn": ["h3"],
    "server_name": "",
    "acme": {
      "domain": "",
      "email": ""
    }
  },
  "masquerade": "https://www.bing.com"
}
```

## 客户端配置

```yaml
# Clash.Meta
- name:
  type: hysteria2
  server:
  port:
  password:
  alpn: [h3]
```

这里使用 BBR 流控而非 Brutal，详见 [Brutal 流控](/docs/brutal.md)。

## 端口跳跃

QUIC 支持**连接迁移**，这意味着我们可以随时改变连接端口而不用重新发起握手。这能使我们规避许多 QoS 策略，并轻松地应对端口封禁。

遗憾的是，目前 sing-box 和 Clash.Meta（后者使用了前者的 QUIC 实现）没有支持 Hysteria 2 端口跳跃的计划。（有一个 fork 实现了该功能，见 [SagerNet/sing-box/pull/764](https://github.com/SagerNet/sing-box/pull/764)）

我们可以利用 Clash.Meta 的**负载均衡**策略组，间接实现少量端口的跳跃。笔者的实际体验中，新开网页延迟偶尔明显增高。推测是通过设置不同出站实现的端口跳跃会不断重新协商建立连接，开销很大，没有充分利用 QUIC 的连接迁移等功能。不建议使用，下列示例仅供演示。

服务端需要开启内核转发，并通过 iptables/ufw/firewalld 等工具（取决于你的发行版）配置端口转发，代理服务端则无需特别配置。具体命令请自行搜索。

```yaml
# Clash.Meta
- &hysteria2
  name: "Hysteria 2 p1"
  type: hysteria2
  server: 
  port: 443
  password: 
  alpn: [h3]
- <<: *hysteria2
  name: "Hysteria 2 p2"
  port: 20000
- <<: *hysteria2
  name: "Hysteria 2 p3"
  port: 20001
- <<: *hysteria2
  name: "Hysteria 2 p4"
  port: 20002
- <<: *hysteria2
  name: "Hysteria 2 p5"
  port: 20003
- <<: *hysteria2
  name: "Hysteria 2 p6"
  port: 20004
- <<: *hysteria2
  name: "Hysteria 2 p7"
  port: 20005
- <<: *hysteria2
  name: "Hysteria 2 p8"
  port: 20006

- name: "Hysteria 2"
  type: load-balance
  proxies:
    - Hysteria 2 p1
    - Hysteria 2 p2
    - Hysteria 2 p3
    - Hysteria 2 p4
    - Hysteria 2 p5
    - Hysteria 2 p6
    - Hysteria 2 p7
    - Hysteria 2 p8
  url: "https://www.gstatic.com/generate_204"
  interval: 300
  strategy: sticky-sessions # consistent-hashing, round-robin
```