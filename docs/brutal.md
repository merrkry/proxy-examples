# Brutal

Hysteria 协议发明的拥塞控制算法，用于 Hysteria 系列协议的 QUIC 拥塞控制，后来移植到 TCP（[TCP Brutal](https://github.com/apernet/tcp-brutal/blob/master/README.zh.md)）。通过一定程度上无视丢包率，按照**固定速率**发包和重传来提高速度，尤其适用于质量非常差的线路。具体介绍见 [Hysteria 是多倍发包吗？](https://v2.hysteria.network/zh/docs/misc/Hysteria-Brutal/)。

Brutal 的实际性能**非常依赖于正确的带宽设置**。过高的值没有加速效果，只会徒增延迟。建议你从实际带宽 * 0.8 开始测试，逐渐降低。

Brutal 的缺点非常明显：

- 作用于 QUIC 时，无视拥塞的激进发包将极大增加你被运营商 **QoS** 的概率（这恰恰是追求速度而使用 Brutal 的用户不愿意看到的），
- 作用于 TCP 时，在拥堵条件下仍然大量发包，将导致非常严重的**延迟**问题。（见 [多路复用](/docs/mux.md)）
- 过于激进的发包会劣化其他用户的体验，**加剧拥堵**（这一点有争议，同样见 [Hysteria 是多倍发包吗？](https://v2.hysteria.network/zh/docs/misc/Hysteria-Brutal/) 和 [tobyxdd/response.md](https://gist.github.com/tobyxdd/0993ac063b2eee94f7d36ddd786f52ce)）

实际上，Hysteria 系列协议的优秀速度表现很大程度上来源于 QUIC 协议自身的优点。翻墙圈子里人尽皆知的 BBR 流控已经足够优秀。tobyxdd（Hysteria 2 作者）也承认，BBR 是「比 Brutal 优雅得多的算法」（见 [tobyxdd/response.md](https://gist.github.com/tobyxdd/0993ac063b2eee94f7d36ddd786f52ce)）

如果你仍然希望尝试 Brutal 流控：对于 Hysteria 2 协议，设置 `up` 和 `down` 即可，见 [Clash.Meta 文档](https://wiki.metacubex.one/config/proxies/hysteria2/)；对于 TCP 类协议，请参见 [chika0801/sing-box-examples/TCP Brutal 使用指南](https://github.com/chika0801/sing-box-examples/tree/main/TCP_Brutal#readme)。