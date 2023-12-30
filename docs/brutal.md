# Brutal

Hysteria 协议发明的拥塞控制算法，用于 Hysteria 系列协议的 QUIC 拥塞控制，后来移植到 TCP（[TCP Brutal](https://github.com/apernet/tcp-brutal/blob/master/README.zh.md)）。通过一定程度上无视丢包率，按照**固定速率**发包和重传来提高速度，理论上「专门为质量非常差的线路设计」（实际情况也许正好相反，见后文）。具体介绍见 [Hysteria 是多倍发包吗？](https://v2.hysteria.network/zh/docs/misc/Hysteria-Brutal/)。

Brutal 的缺点非常明显：

- 作用于 QUIC 时，无视拥塞的激进发包将极大增加你被运营商 **QoS** 的概率（这恰恰是追求速度而使用 Brutal 的用户不愿意看到的），
- 作用于 TCP 时，在拥堵条件下仍然大量发包，将导致非常严重的**延迟**问题。（见 [多路复用](/docs/mux.md)）；Brutal 依赖于 sing-mux 的多路复用，max_connections 必须为 1，此时连接对**丢包**非常敏感，一丢全断。
- 过于激进的发包会劣化其他用户的体验，**加剧线路整体拥堵**（这一点有一些争议，因为拥堵一定程度上是 ISP 超售带宽的结果，而不是利用自己应得的带宽的用户造成的。见 [Hysteria 是多倍发包吗？](https://v2.hysteria.network/zh/docs/misc/Hysteria-Brutal/) 和 [tobyxdd/response.md](https://gist.github.com/tobyxdd/0993ac063b2eee94f7d36ddd786f52ce)）
- 需要设置固定带宽，对于经常变换网络环境的**移动设备**来说非常麻烦。

那么，有没有 QoS 较弱、拥堵程度较低、不丢包、超售程度较低的线路呢？有，那就是各家的**天价**精品网，比如 CN2 GIA。在优质线路下，可以回避上述问题，享受到 Brutal 的好处：

- 单线程跑满带宽的高速度
- 起速更快（尤其是在美西等延迟较高的线路下，BBR 等流控需要较长的时间才能调整到最优速率）

矛盾的一点是，**正是因为线路差才会有人折腾 Brutal**。

实际上，Hysteria 系列协议的优秀速度表现很大程度上来源于 QUIC 协议自身的优点。翻墙圈子里人尽皆知的 BBR 流控已经足够优秀。tobyxdd（Hysteria 2 作者）也承认，BBR 是「比 Brutal 优雅得多的算法」（见 [tobyxdd/response.md](https://gist.github.com/tobyxdd/0993ac063b2eee94f7d36ddd786f52ce)）

如果你希望尝试 Brutal 流控：对于 Hysteria 2 协议，设置 `up` 和 `down` 即可，见 [Clash.Meta 文档](https://wiki.metacubex.one/config/proxies/hysteria2/)；对于 TCP 类协议，请参见 [chika0801/sing-box-examples/TCP Brutal 使用指南](https://github.com/chika0801/sing-box-examples/tree/main/TCP_Brutal#readme)。

Brutal 的实际性能**非常依赖于正确的带宽设置**。过高的值没有加速效果，只会徒增延迟。建议你从实际带宽 * 0.8 开始测试，逐渐降低。