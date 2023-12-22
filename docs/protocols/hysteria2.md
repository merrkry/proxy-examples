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

我们可以利用 Clash.Meta 的**负载均衡**策略组，间接实现少量端口的跳跃。

> 待施工