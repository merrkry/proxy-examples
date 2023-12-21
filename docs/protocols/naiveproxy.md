# [Näiveproxy](https://github.com/klzgrad/naiveproxy)

[klzgrad/naiveproxy/wiki](https://github.com/klzgrad/naiveproxy/wiki/%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)

- 解决 TLS in TLS
- 自带多路复用
- 极强的安全性
- 客户端支持较少
    - 官方客户端
    - Nekobox

sing-box 提供了 Näive 入站的实现，和官方客户端的不同在于，sing-box 用浏览器访问无法反代到伪装站点，但是配置更简单，不需要前端。

## 服务端配置

```json
// sing-box
{
  "type": "naive",
  "listen": "::",
  "listen_port": 443,
  "users": [
    {
      "username": "",
      "password": ""
    }
  ],
  "tls": {
    "enabled": true,
    "server_name": "",
    "acme": {
      "domain": "",
      "email": ""
    }
  }
}
```

## 客户端配置

通过本地 socks，配合使用 Clash.Meta 和 Näiveproxy 官方客户端。

```yaml
# Clash.Meta
- name:
  type: socks5
  server: 127.0.0.1
  port: 1080
```

```json
// naiveproxy
{
  "listen": "socks://127.0.0.1:1080",
  "proxy": "https://<user>:<password>@<domain>:<port>",
  "insecure-concurrency": 2
}
```

注意设置 `insecure-concurrency` 不是必须的。大于 1 的 `insecure-concurrency` 值有助于改善在高丢包环境下的体验，但是会**降低安全性**，见 [文档](https://github.com/klzgrad/naiveproxy/blob/master/USAGE.txt)。