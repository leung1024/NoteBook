# TIME_WAIT太多怎么处理

## TIME_WAIT的作用

- 保证最后一个ACK能够正常的被发出

  如果主动关闭的client端直接进入CLOSED状态，那么被动关闭的server会接受到RST异常，认为对方出现了异常。因此client收到server发送的FIN后，应该进入TIME_WAIT状态并向对方发送ACK，使server能够顺利的进入CLOSED状态。

- 避免网络残留的数据包会污染到下一次TCP传输

  若没有TIME_WAIT的状态，server和client都同时进入CLOSED状态，那么在下一次相同IP和端口的TCP连接中，可能会接收到上一段TCP连接所残留的数据信息。因此，TIME_WAIT需要等待2MSL，使得残余的数据能够被处理（MSL是用来确认一个数据报在网络中单向发送到认定丢失的时间，2倍MSL意义在于计算丢失报文的发送到响应丢失所需要的总时间）

## 大量TIME_WAIT出现并且需要解决的场景

在高并发短连接的TCP服务器上，当服务器处理完请求后立刻按照主动正常关闭连接。。。这个场景下，会出现大量socket处于TIMEWAIT状态。如果客户端的并发量持续很高，此时部分客户端就会显示连接不上。

- 高并发：大量端口被同时占用
- 短连接：传输的总时间小于TIME_WAIT等待时间，造成端口被低效的占用

## 如何处理？

不像Windows 可以修改注册表修改2MSL 的值，linux （默认的MSL值（60s））是没有办法修改MSL的，tcp_fin_timeout 不是2MSL 而是Fin-WAIT-2状态.

在sysctl修改改两个内核参数就行了，如下：

```shell
net.ipv4.tcp_tw_reuse = 1 # Enable fast recycling TIME-WAIT sockets
						  # 同一IP和端口的TCP连接能够在TIME_WAIT状态下被复用

# net.ipv4.tcp_tw_recycle = 1 # !!!(has been removed from Linux 4.1).
							  # 60秒内同一个IP建立2个连接的话，
							  # 后面一个SYN连接的时间戳必须大于之前的SYN里面的TSVal，
							  # 否则服务器会认为这是一个老连接的数据包，忽略它。

# 客户端和服务端timestamps 都需要开启时才管用

```

## 不使用的效果

处于TIME_WAIT状态下的同一个IP和端口不能够立即被重新使用

![img](./imgs/image1.png)

## tcp_tw_reuse的使用效果

即使处于TIME_WAIT状态，仍可以复用连接

![img](./imgs/image2.png)

## Reference

- [TCP/IP详解--TCP连接中TIME_WAIT状态过多](https://blog.csdn.net/yusiguyuan/article/details/21445883)
- [tcp_tw_reuse、tcp_tw_recycle 使用场景及注意事项](https://www.cnblogs.com/lulu/p/4149312.html)
- [TCP TIME STAMP](http://perthcharles.github.io/2015/08/27/timestamp-intro/)
- [The difference with tcp_tw_recycle and tcp_tw_reuse](http://linuxsyseng.blogspot.com/2017/03/the-difference-with-tcptwrecycle-and.html)
- [RTO计算](https://blog.csdn.net/wdscq1234/article/details/52505191)
- [Coping with the TCP TIME-WAIT state on busy Linux servers](https://vincent.bernat.ch/en/blog/2014-tcp-time-wait-state-linux#netipv4tcp_tw_recycle)