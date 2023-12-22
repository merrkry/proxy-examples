# [ShadowTLS](https://github.com/ihciah/shadow-tls)

- 可以使用第三方受信证书

ShadowTLS 协议只是提供伪装隧道，内层还需要再搭配其他代理协议，比如 shadowsocks 2022。

伪装域名的选取参考 [REALITY](/docs/protocols/reality.md)。

## 服务端配置

```json
// sing-box
{
    "type": "shadowtls",
    "listen": "::",
    "listen_port": 443,
    "detour": "shadowsocks-in",
    "version": 3,
    "users": [
    {
        "password": ""
    }
    ],
    "handshake": {
        "server": "",
        "server_port": 443
    },
    "strict_mode": true,
},
{
    "type": "shadowsocks",
    "tag": "shadowsocks-in",
    "listen": "::",
    "method": "2022-blake3-aes-128-gcm",
    "password": ""
},
```

注意 Shadowsocks 部分对密码格式有要求，见 [sing-box 文档](https://sing-box.sagernet.org/zh/configuration/inbound/shadowsocks/)

如果要使用 [多路复用](/docs/mux.md)，相关字段需要填写在内层 shadowsocks 部分。

## 客户端配置

```yaml
- name:
  type: ss
  server:
  port: 443
  cipher: 2022-blake3-aes-128-gcm
  password:
  plugin: shadow-tls
  plugin-opts:
    host:
    password:
    version: 3
```
