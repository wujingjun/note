# TCP的三次握手和四次挥手



## 背景描述

真正进行通信的实体是在主机的进程，是一个主机中的一个进程与另外一个主机中的一个进程在交换数据。IP协议虽然能把数据报文送到目的主机，但是并没有交付给主机的具体应用进程。而端到端的通信才应该是应用进程之间的通信。

UDP，在传送数据前不需要先建立连接，远地的主机在收到UDP报文后也不需要给出任何确认。虽然UDP不提供可靠交付，但是正是因为这样，省去很多的开销，使得它的速度比较快，比如一些对实时性要求较高的服务，就常常使用的是UDP。对应的应用层的协议主要有DNS、TFTP、DHCP、SNMP、NFS等。

TCP，提供面向连接的服务，在传送数据之前必须先建立连接，数据传送完成后要释放连接。因此TCP是一种可靠的运输服务，但是正因为这样，不可避免地增加了许多的开销，比如确认，流量控制等。对应的应用层的协议主要有SMTP，TELNET,HTTP,FTP等。



## 常用的熟知端口号

| 应用程序   | FTP   | TFTP | TELNET | SMTP | DNS  | HTTP | SSH  | MYSQL |
| ---------- | ----- | ---- | ------ | ---- | ---- | ---- | ---- | ----- |
| 熟知端口   | 21,20 | 69   | 23     | 25   | 53   | 80   | 22   | 3306  |
| 传输层协议 | TCP   | UDP  | TCP    | TCP  | UDP  | TCP  | TCP  | TCP   |



## TCP的概述

TCP把连接作为最基本的对象，每一条TCP连接都有两个端点，这种端点我们叫做套接字（socket），它的定义未端口号拼接到IP地址即构成了套接字，例如，若IP地址为192.3.4.16而端口号为80，那么得到的套接字为192.3.4.16:80



### TCP报文首部

![](D:\note\Internet.assets\20180628005629363.png)

1. 源端口和目的端口，各占2个字节，分别写入源端口和目的端口；
2. 序号，占4个字节，TCP连接中传送的字节流中的每个字节都按顺序编号。例如，一段报文的序号值是301，而携带的数据共有100字段，显然下一个报文段（如果还有的话）的数据序号应该从401开始
3. 确认号，占4个字节，是期望收到对方下一个报文的第一个数据字节的序号。例如，B收到了A发送过来的报文，其序列号字段是501，而数据长度是200字节，这表明B正确的收到了A发送的到序号700为止的数据。因此，B期望收到A的下一个数据序号是701，于是B在发送给A的确认报文段中把确认号位置为701.
4. 数据偏移，占4位，它指出TCP报文的数据距离TCP报文段的起始处有多远。
5. 保留，占6位，保留今后使用，但目前应都为0。
6. 紧急URG，当URG=1，表明紧急指针字段有效。告诉系统此报文段中有紧急数据；
7. 确认ACK，仅当ACK=1时，确认号字段才有效。TCP规定，在连接建立后所有报文的传输都必须把ACK置为1；
8. 推送PSH，当两个应用进程进行交互式通信时，有时在一端的应用进程希望在键入一个命令后立即就能收到对方的响应，这时候就将PSH=1；
9. 复位RST，当RST=1，表明TCP连接中出现严重差错，必须释放连接，然后再重新建立连接；
10. 同步SYN，在连接建立时用来同步序号。当SYN=1，ACK=0，表明是连接请求报文，若同意连接，则响应报文中应该是SYN=1,ACK=1;
11. 终止FIN，用来释放连接。当FIN=1，表明此报文的发送方的数据已经发送完毕，并且要求释放；
12. 窗口，占2字节，指的是通知接收方，发送本报文你需要有多大的空间来接受；
13. 检验和，占2字节，检验首部和数据这两部分；
14. 紧急指针，占2字节，指出本报文段中的紧急数据的字节数；
15. 选项，长度可变，定义一些其他的可选的参数。





## TCP连接的建立（三次握手）

![](D:\note\Internet.assets\aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwNjA1MTEwNDA1NjY2.png)

 ```
最开始的时候客户端和服务器都是处于CLOSED状态。主动打开连接的为客户端，被动打开连接的是服务器。
 ```

1. TCP服务器进程先创建传输控制块TCB，时刻准备接收客户进程的连接请求，此时服务器就进入了LISTEN（监听）状态；
2. TCP客户进程也是先创建传输控制块TCB，然后向服务器发出链接请求报文，这是报文首部中的同步位SYN=1，同时选择一个出事序列号seq=x，此时，TCP客户端进程进入了SYUN-SENT（同步已发送状态）状态。TCP规定，SYN报文段（SYN=1的报文段）不能携带数据，但需要消耗一个序号。
3. TCP服务器收到请求报文后，如果同意连接，则发出确认报文。确认报文应该ACK=1,SYN=1,确认号是ack=x+1，同时也要为自己初始化一个序列号seq=y，此时，TCP服务器进程进入了SYN-RCVD（同步收到）状态。这个报文也不能携带数据，但是同样要消耗一个序号。
4. TCP客户端进程收到确认后，还要向服务器给出确认。确认报文的ACK=1，ack=y+1，自己的序列号seq=x+1，此时，TCP连接建立后，客户端进入ESTABLISHED（已建立连接）状态。TCP规定，ACK报文段可以携带数据，但是如果不携带数据则不消耗序号。
5. 当服务器收到客户端的确认后也进入ESTABLISHED状态，此后双方就可以开始通信了。



## 为什么TCP客户端最后还要发送一次确认呢？

一句话，主要防止已经失效的连接请求报文突然又传送到了服务器，从而产生错误。

如果使用的是两次握手建立连接，假设有这样一种场景，客户端发送了第一个请求来连接并且没有丢失，只是因为在网络结点中滞留的时间太长了，由于TCP的客户端迟迟没有收到确认报文，以为服务器没有收到，此时重新向服务器发送这条报文，此后客户端和服务器经过两次握手完成连接，传输数据，然后关闭连接/。此时此前滞留的那一次请求连接，网络通畅了到达了服务器，这个报文本该是失效的，但是，两次握手的机制将会让客户端和服务器再次建立连接，这将导致不必要的错误和资源的浪费。

如果采用的是三次握手，就算是那一次失效的报文传送过来了，服务器端接收到了那条失效报文并且回复了确认报文，但是客户端不会再次发出确认。由于服务器收不到确认，就知道客户端并没有请求连接。



## TCP连接的释放（四次挥手）

![](D:\note\Internet.assets\aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTcwNjA2MDg0ODUxMjcy.png)

```
数据传输完毕后，双方都可释放连接，最开始的时候，客户端和服务器都是处于ESTABLSHED状态，然后客户端主动关闭，服务器被动关闭
```

1. 客户端进程发出来连接释放报文，并且停止发送数据。释放数据报文首部，FIN=1，其序列号为seq=u（等于前面已经传送过来的数据的最后一个字节的序号加1），此时，客户端进入FIN-WAIT-1（终止等待1）状态。TCP规定，FIN报文端即使不携带数据，也要消耗一个序号。
2. 服务器收到连接释放报文，发出确认报文，ACK=1,ack=u+1，并且带上自己的序列号seq=v，此时，服务端就进入了CLOSE-WAIT（关闭等待）状态。TCP服务器通知高层的应用进程，客户端向服务器的方向就释放了，这时候处于半关闭状态，即客户端已经没有数据要发送了，但是服务器若发送数据，客户端依然要接受，这个还要持续一段时间，也就是整个CLOSE-WAIT状态持续的时间。
3. 客户端收到服务器的确认请求后，此时，客户端进入FIN-WAIT-2（终止等待2）状态，等待服务器发送来连接释放报文（在这之前还需要接受服务器发送的最后的数据）。
4. 服务器将最后的数据发送完毕后，就向客户端发送来连接释放报文，FIN=1，ack=u+1，由于在半关闭状态，服务器很可能又发送了一些数据，假定此时的序列号为seq=w，此时，服务器就进入了LAST-ACK（最后确认）状态，等待客户端的确认。
5. 客户端收到服务器的连接释放报文后，必须发出确认，ACK=1，ack=w+1,而自己的序列号是seq=u+1，此时，客户端就进入了TIME-WAIT（时间等待）状态。注意此时TCP连接还没有释放，必须经过2*MSL（最长报文段寿命）的时间后，当客户端撤销相应的TCB后，才进入CLOSED状态。
6. 服务器只要收到了客户端发出的确认，立即进入CLOSED状态。同样，撤销TCB后，就结束了这次的TCP连接。可以看到，服务器结束TCP连接的时间要比客户端早一些。



## 为什么客户端最后还要等待2MSL？

MSL（Maximum Segment Lifetime），TCP允许不同的实现可以设置不同的MSL值。

第一，保证客户端发送的最后一个ACK报文能够到达服务器，因为这个ACK报文可能丢失，站在服务器的角度看来，我已经发送了FIN+ACK报文请求断开了，客户端还没有给我回应，应该是我发送的请求断开报文它没有收到，于是服务器又会重新发送一次，而客户端就能在这个2MSL时间段内收到这个重传的报文，接着给出回应报文，并且会重启2MSL计时器。

第二，防止类似与“三次握手”中提到了的“已经失效的连接请求报文段”出现在本连接中。客户端发送完最后一个确认报文后，在这个2MSL时间中，就可以使本连接持续的时间内所产生的所有报文段都从网络中消失。这样新的连接中不会出现旧连接的请求报文。



## 为什么建立连接是三次握手，关闭连接却是四次挥手？

建立连接的时候， 服务器在LISTEN状态下，收到建立连接请求的SYN报文后，把ACK和SYN放在一个报文里发送给客户端。
而关闭连接时，服务器收到对方的FIN报文时，仅仅表示对方不再发送数据了但是还能接收数据，而自己也未必全部数据都发送给对方了，所以己方可以立即关闭，也可以发送一些数据给对方后，再发送FIN报文给对方来表示同意现在关闭连接，因此，己方ACK和FIN一般都会分开发送，从而导致多了一次。



## 如果已经建立连接，但是客户端突然出现了故障了怎么办？

TCP还设有一个保活计时器，显然，客户端如果出现故障，服务器不能一直等下去，拜拜浪费资源。服务器每收到一次客户端的请求后都会重新复位这个计时器，时间通常是设置为2小时，若两小时还没有收到客户端的任何数据，服务器就会发送一个探测报文段，以后每隔75秒发送一次。若一连发送10个探测报文仍然没有反应，服务器就认为客户端出了故障，接着就关闭连接。






























