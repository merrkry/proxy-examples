# [TUIC](https://github.com/EAimTY/tuic/tree/dev)

基于 QUIC，以减少额外握手延迟为目标的轻量级代理协议。

- 基于 QUIC，实现连接迁移、多路复用等功能
- 多种 UDP 代理模式

## 服务端配置

```json
// sing-box
{
  "type": "tuic",
  "listen": "::",
  "listen_port": 8443,
  "users": [
    {
      "uuid": "",
      "password": ""
    }
  ],
  "congestion_control": "bbr",
  "zero_rtt_handshake": false,
  "tls": {
    "enabled": true,
    "alpn": ["h3"],
    "server_name": "",
    "acme": {
      "domain": "",
      "email": ""
    }
  }
}
```

## 客户端配置

```yaml
# Clash.Meta
- name:
  server:
  port: 8443
  type: tuic
  uuid:
  password:
  alpn: [h3]
  disable-sni: false
  reduce-rtt: false
  congestion-controller: bbr
  cwnd: 8
  fast-open: false
  max-open-streams: 32
  udp-over-stream: true
  udp-over-stream-version: 2
```

```json
// sing-box
{
  "tag": "",
  "type": "tuic",
  "server": "",
  "server_port": 9443,
  "uuid": "",
  "password": "",
  "congestion_control": "bbr",
  "tls": {
    "enabled": true,
    "server_name": "",
    "alpn": ["h3"]
  }
}
```

这里开启了 sing-box 和 Clash.Meta 的[私有扩展](https://sing-box.sagernet.org/zh/configuration/outbound/tuic/#udp_over_stream)：`udp-over-stream`（sing-box 服务端会自适应这个配置）。旨在提供更好的 UDP 处理能力，类似 [Juicity 对 TUIC 的改进](https://github.com/juicity/juicity/blob/main/docs/spec_en.md#protocol-features)。
