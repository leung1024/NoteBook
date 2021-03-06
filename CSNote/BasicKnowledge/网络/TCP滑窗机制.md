# TCP滑窗机制

## TCP滑窗

### 作用

- 提供TCP可靠性；
- 提供TCP的流控特性。

### 相关参数

- 16bits表示窗口大小；
- option-kind表示窗口的扩大因子。

### 发送端状态

- 已经发送并得到对方ACK；
- 已经发送还未收到ACK；
- 未发送但对方运行发送；
- 未发送且对方不允许发送。

### 接收端状态

- 已接收；
- 未接收准备收；
- 未接收并未准备接收。

### 工作流程

- 移动的条件：只有收到对方发送的ACK确认，才会移动窗口的左边界。
- 窗口调整方式：
  - 慢开始和拥堵避免；
  - 快速重传和快速回复。



## TCP拥塞控制

### 拥塞判断

- TCP发生了RTO重传，认为网络发生拥塞。

### 慢开始和拥塞避免

- 慢开始
  - 避免立即使用较大的窗口进行数据的传输，导致堵塞。该方法会从小到大逐渐增大发送端的拥塞窗口数值
  - 先将拥塞窗口cwnd设置为一个MSS的大小（即窗口大小设置为1）
  - 每收到一个确认后cwnd就扩大一倍(1, 1 + 1, 2 + 2, 4 + 4)
- 拥塞避免
  - 当窗口大小到达门限值ssthresh，启用拥塞避免
  - 拥塞避免的主要思想是加法增大，也就是cwnd的值不再指数级往上升，开始加法增加。
  - 此时当窗口中所有的报文段都被确认时，cwnd的大小加1，cwnd的值就随着RTT开始线性增加，这样就可以避免增长过快导致网络拥塞，慢慢的增加调整到网络的最佳值。
- 为了防止cwnd增长过大引起网络拥塞，还需设置一个慢开始门限ssthresh状态变量。
  - **当cwnd<ssthresh时，使用慢开始算法。**
  - **当cwnd>ssthresh时，改用拥塞避免算法。**
  - **当cwnd=ssthresh时，慢开始与拥塞避免算法任意。**
- 当网络发生拥塞时
  - 把ssthresh降低为cwnd值的一半
  - 把cwnd重新设置为1
  - 重新进入慢启动过程
- 过程图

![20130801220438375](./imgs/20130801220438375.jpeg)

- 快重传和快恢复

  - 快重传

    - 如果发送方一连收到 3 个重复的确认，就应当立即传送对方未收到的报文 M3，而不必等待 M3 的重传计时器到期。

  - 快恢复

    - 当发送方连续收到三个重复确认时，就执行“乘法减小”算法，把ssthresh门限减半。但是接下去并不执行慢开始算法。
    - 此时不执行慢开始算法，而是将cwnd设置为ssthresh的大小，然后执行拥塞避免算法

  - 过程图

    ![image-20181003181346195](./imgs/image-20181003181346195.png)

    ![image-20181003181313675](./imgs/image-20181003181313675.png)



## 一些名词解释 

#### MSL

即“报文最大生存时间”，他是任何报文在网络上存在的最长时间，超过这个时间报文将被丢弃。The TCP standard defines MSL as being a value of 120 seconds 

#### RTT

客户到服务器往返所花时间（round-trip time，简称RTT），RTT包含：

- 链路的传播时间（propagation delay）
- 末端系统的处理时间
- 路由器缓存中的排队和处理时间（queuing delay）

#### RTO（Retransmission TimeOut）

重传超时时间，计算的常用方法有：

- 经典的算法 RFC793
- Jacobaon/Karels 算法

#### cwnd（congestion window size）
拥塞窗口大小（能装多少个MSS）

#### HTTP METHOD
  - GET: 获取接口信息，form只支持get和post
  - POST： 提交数据
  - PUT： 支持幂等性的post。也就是说同一个请求因服务器或者传输发生异常发生重传时，最终保证只有唯一一条数据被写入——幂等性。
  - DELETE：删除服务器上的资源
  - HEAD：查看HTTP的头部，看看支持什么方法，数据类型是什么，大小是什么
  - OPTIONS：查看支持的HTTP方法
#### Http code

- 301和302
  - 301为永久性的跳转，而302为临时性的跳转。永久性体现在之前的连接不再存在。

![image-20180920114555774](./imgs/image-20180920114555774.png)

> 301 Moved Permanently 被请求的资源已永久移动到新位置，并且将来任何对此资源的引用都应该使用本响应返回的若干个URI之一。如果可能，拥有链接编辑功能的客户端应当自动把请求的地址修改为从服务器反馈回来的地址。除非额外指定，否则这个响应也是可缓存的。

> 302 Found 请求的资源现在临时从不同的URI响应请求。由于这样的重定向是临时的，客户端应当继续向原有地址发送以后的请求。只有在Cache-Control或Expires中进行了指定的情况下，这个响应才是可缓存的。

