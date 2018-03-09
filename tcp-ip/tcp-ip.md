# TCP/IP

## Focus

4th — Transport TCP Segment<br>
3rd - Network   IP  Package<br>
2nd - Data Link ARP Frame

## TCP Head

![head](http://guyuchen.com/deadpool/images/TCP-Header-01.jpg)

* 五元组: protocol, src_ip, src_port, dst_ip, dst_port
* TCP: protocol(TCP), src_port, dst_port
* **Sequence Number**: 是包的序号，用来解决网络包乱序（reordering）问题。
* **Acknowledgement Number**: 就是ACK——用于确认收到，用来解决不丢包的问题。
* **Window**: 又Advertised-Window，也就是著名的滑动窗口（Sliding Window），用于解决流控的。
* **TCP Flag**: 也就是包的类型，主要是用于操控TCP的状态机的。

Others:

![others](http://guyuchen.com/deadpool/images/TCP-Header-02.jpg)

## TCP/IP State Machine

![State](http://guyuchen.com/deadpool/images/tcpfsm.png)

## TCP/IP Connection

![Connection](http://guyuchen.com/deadpool/images/tcpIpConnection.png)

* **为什么建链接要3次握手**

    主要是要初始化 Sequence Number 的初始值。<br>
    通信的双方要互相通知对方自己的Inital Sequence Number(ISN)用于同步。<br>
    所以叫SYN(Synchronize Sequence Numbers).<br>
    这个号要作为以后的数据通信的序号，以保证应用层接收到的数据不会因为网络上的传输的问题而乱序（TCP会用这个序号来拼接数据）。

* **为什么断链接需要4次挥手**

    因为TCP是全双工的，所以，发送方和接收方都需要Fin和Ack，只不过，有一方是被动的。<br>

    ![](http://guyuchen.com/deadpool/images/tcpclosesimul.png)

* **关于建连接时SYN超时**
    
    第二次握手后client掉线，server端没有收到client回来的ACK，那么，这个连接处于一个中间状态。<br>
    于是，server端如果在一定时间内没有收到的TCP会重发SYN-ACK。<br>
    在Linux下，默认重试次数为5次，重试时间间隔为1s, 2s, 4s, 8s, 16s，总共31s，第5次发出后还要等32s都知道第5次也超时了，所以，总共需要 1s + 2s + 4s+ 8s+ 16s + 32s = 2^6 -1 = 63s，TCP才会把断开这个连接。

* **关于SYN Flood攻击**

    YN Flood攻击可以把服务器的syn连接的队列耗尽。<br>
    解决办法：<br>
    第一个是：tcp_synack_retries 可以用他来减少重试次数；<br>
    第二个是：tcp_max_syn_backlog，可以增大SYN连接数；<br>
    第三个是：tcp_abort_on_overflow 处理不过来干脆就直接拒绝连接了。

* **关于ISN的初始化**

    ISN会和一个假的时钟绑在一起，这个时钟会在每4微秒对ISN做加一操作，直到超过2^32，再归零，大约是4.55个小时。
    所以，只要MSL(Maximum Segment Lifetime)的值小于4.55小时，ISN就不会重复。

* **为什么要这有TIME_WAIT？为什么不直接给转成CLOSED状态呢？**

   1. TIME_WAIT确保有足够的时间让对端收到了ACK，如果被动关闭的那方没有收到Ack，就会触发被动端重发Fin，一来一去正好2个MSL
   2. 有足够的时间让这个连接不会跟后面的连接混在一起

## TCP重传机制

接收端给发送端的Ack确认只会确认最后一个连续的包。<br>
比如，发送端发了1,2,3,4,5一共五份数据，接收端收到了1，2，于是回ack 3，然后收到了4，此时3没收到。

* **超时重传机制**

    发送端发了1,2,3,4,5一共五份数据，接收端收到了1，2.<br>
    此时回复ack2, 不回ack3，死等3.<br>
    当发送方发现收不到3的ack超时后，会重传3。一旦接收方收到3后，会ack4意味着3和4都收到了。

    发送方对后续数据对此有两种选择：<br>
    一是仅重传timeout的包，也就是第3份数据。<br>
    二是重传timeout后所有的数据，也就是第3，4，5这三份数据。

* **快速重传机制**

    Fast Retransmit的好处是不用等timeout了再重传。但它依然面临一个艰难的选择，是重传之前的一个还是重传所有的问题。

    发送方发出了1，2，3，4，5份数据，第一份先到送了，于是就ack回2，结果2因为某些原因没收到，3到达了，于是还是ack回2，后面的4和5都到了，但是还是ack回2，因为2还是没有收到，于是发送端收到了三个ack=2的确认，知道了2还没有到，于是就马上重转2。然后，接收端收到了2，此时因为3，4，5都收到了，于是ack回6。示意图如下：

    ![](http://guyuchen.com/deadpool/images/FASTIncast021.png)

* **SACK (Selective Acknowledgment)**

    这种方式需要在TCP头里加一个SACK的东西，ACK还是Fast Retransmit的ACK，SACK则是汇报收到的数据碎版。<br>
    这个协议需要两边都支持。在 Linux下，可以通过tcp_sack参数打开这个功能。<br>

    ![](http://guyuchen.com/deadpool/images/tcp_sack_example-900x507.jpg)

## 超时时长

超时时间在不同的网络的情况下，根本没有办法设置一个死的值。只能动态地设置。 <br>
TCP引入了RTT(Round Trip Time)，也就是一个数据包从发出去到回来的时间。<br>
从而可以方便地设置Timeout——RTO(Retransmission TimeOut).

* 经典算法
* Karn / Partridge 算法
* Jacobson / Karels 算法

## TCP滑动窗口 - 网络流控

TCP头里有一个字段叫Window，又叫Advertised-Window，这个字段是接收端告诉发送端自己还有多少缓冲区可以接收数据。于是发送端就可以根据这个接收端的处理能力来发送数据，而不会导致接收端处理不过来。 

* **发送端**

    ![](http://guyuchen.com/deadpool/images/tcpswwindows.png)

    #1已收到ack确认的数据。<br>
    #2发还没收到ack的。<br>
    #3在窗口中还没有发出的（接收方还有空间）。<br>
    #4窗口以外的数据（接收方没空间）

    ![](http://guyuchen.com/deadpool/images/tcpswslide.png)

* **接受端**

    ![](http://guyuchen.com/deadpool/images/tcpswflow.png)

* **DDoS攻击**

    一些攻击者会在和HTTP建好链发完GET请求后，就把Window设置为0，然后服务端就只能等待进行ZWP(Zero Window Probe)，于是攻击者会并发大量的这样的请求，把服务器端的资源耗尽。

* **MSS(Max Segment Size)**

    MTU(Maximum Transmission Unit)是1500字节，除去TCP+IP头的40个字节，真正的数据传输可以有1460，这就是所谓的MSS
    
## TCP的拥塞处理

TCP通过一个timer采样了RTT并计算RTO，但是，如果网络上的延时突然增加，那么，TCP对这个事做出的应对只有重传数据，但是，重传会导致网络的负担更重，于是会导致更大的延迟以及更多的丢包，于是，这个情况就会进入恶性循环被不断地放大。试想一下，如果一个网络内有成千上万的TCP连接都这么行事，那么马上就会形成“网络风暴”，TCP这个协议就会拖垮整个网络。

* **慢启动**

    1. 连接建好的开始先初始化cwnd = 1，表明可以传一个MSS大小的数据。
    1. 每当收到一个ACK，cwnd++; 呈线性上升
    1. 每当过了一个RTT，cwnd = cwnd*2; 呈指数让升
    1. 还有一个ssthresh（slow start threshold），是一个上限，当cwnd >= ssthresh时，就会进入“拥塞避免算法”

    ![](http://guyuchen.com/deadpool/images/tcp.slow_.start_.jpg)

* **拥塞避免**

    当cwnd >= ssthresh时，就会进入“拥塞避免算法”。一般来说ssthresh(slow start threshold)的值是65535字节，当cwnd达到这个值时后，算法如下：

    1. 收到一个ACK时，cwnd = cwnd + 1/cwnd
    1. 当每过一个RTT时，cwnd = cwnd + 1

* **拥塞发生**

    当丢包的时候，会有两种情况：

    1. 等到RTO超时，重传数据包。TCP认为这种情况太糟糕，反应也很强烈。

        sshthresh =  cwnd /2<br>
        cwnd 重置为 1<br>
        进入慢启动过程<br>

    2）Fast Retransmit算法，也就是在收到3个duplicate ACK时就开启重传，而不用等到RTO超时。

        cwnd = cwnd /2<br>
        sshthresh = cwnd<br>
        进入快速恢复算法(Fast Recovery)

* **快速恢复(Fast Recovery)**

    快速重传和快速恢复算法一般同时使用.<br>
    进入Fast Recovery之前，cwnd 和 sshthresh已被更新：

    * cwnd = cwnd /2
    * sshthresh = cwnd

    进入Fast Recovery算法：

    * cwnd = sshthresh  + 3 * MSS （3的意思是确认有3个数据包被收到了）
    * 重传Duplicated ACKs指定的数据包
    * 如果再收到 duplicated Acks，那么cwnd = cwnd +1
    * 如果收到了新的Ack，那么，cwnd = sshthresh ，然后就进入了拥塞避免的算法了。


### Reference

[TCP 的那些事儿（上）](https://coolshell.cn/articles/11564.html)
[TCP 的那些事儿（下）](https://coolshell.cn/articles/11609.html)

