# TCP的握手过程

## 如果在 LAST_ACK 状态没收到对方的 ack 信号怎么办？

> B 发送 FIN，进入 LAST_ACK 状态，A 收到这个 FIN 包后发送 ACK 包，由于某种原因，这个 ACK 包丢失了，B 没有收到 ACK 怎么办？

 这种情况下，B 等待 ACK 包超时，则又会向 A 发送了一个 FIN 包。 

- 假如这个时候，A 还处于 TIME_WAIT 状态(也就是在 TIME_WAIT 持续的时间 2MSL 内，收到了) A 收到这个 FIN 包，则A向 B 发送了一个 ACK 包，B 收到这个 ACK 包进入 CLOSED 状态 。

- 假如这个时候，A已经从TIME_WAIT状态变成了CLOSED状态A收到这个FIN包后，认为这是一个错误的连接，向B发送一个RST包，当B收到这个RST包，进入CLOSED状态 。

- 假如这个时候，A挂了（假如这台机器炸掉了），B没有收到A的回应，那么会继续发送FIN包，也就是触发了TCP的重传机制，如果A还是没有回应，B还会继续发送FIN包，直到重传超时(至于这个时间是多长需要仔细研究)，B重置 。

- 
