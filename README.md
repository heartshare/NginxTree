# NginxTree
Nginx技术研究

![](https://i.imgur.com/Q1zsmN8.png)

<pre>
Nginx服务器架构
  1）Nginx服务器的WEB请求处理机制
     1）多进程 
       一个请求到来，web服务器主进程生成一个子进程处理请求
       新建一个子进程开销大
       apache早期采用实时生成子进程，后期采用预生成子进程机制
     2）多线程 
       一个请求到来，web服务器主进程派生一个线程处理请求
       缺点：所有线程位于同一个进程内，资源共享，相互影响
     3）异步方式
       1）同步阻塞方式
       2）同步非阻塞方式
       3）异步非阻塞方式
     5) Nginx方式
       结合多进程&&异步机制
       Nginx当前采用的是master-worker模型（single模型性能差）
       
  2）Nginx的事件驱动模型
     1) 事件驱动模型一般由
         1）事件收集器
         2）事件发送器
         3）事件处理器
     2) Nginx中的事件驱动模型
         Nginx支持（可配置）
            1) select模型
            2）poll模型
            3）epoll模型
            5）rtsig模型
            6）kqueue模型
            7）dev/poll模型
            8)eventport模型
     3）select库 poll库 epoll库 rtsig模型
         1) select
             1) 创建所关注事件的描述符集合（read事件描述符集合，write描述符集合, exception事件描述符集合）
             2）底层提供的select()函数轮询所有的事件描述符,检查是否有事件发生，有就处理
         2）poll
             1）所有的事件类型注册到一个描述符集合中，每个描述符对应的结构分别设置读事件，写事件，异常事件
         3）epoll （公认的最优秀的事件驱动模型）
             一旦有某种事件发生，内核把发生事件的描述符列表通知给进程，这样就避免了轮询整个描述符表
</pre>

![](https://i.imgur.com/lkqGpSp.jpg)

![](https://i.imgur.com/KxKYps7.jpg)

![](https://i.imgur.com/69s2aJI.jpg)

<pre>
  3）Nginx的启动初始化
     Nginx服务器程序完成初始化工作之后，就开始启动进程的工作了。Nginx服务器程序的进程模型
     分为Single和Master两种，其中Single模型是以单进程方式进行工作的，一般不会在实际应用
     中使用；Master模型是以Master-Worker多进程方式工作的，它是实际应用环境中使用的主要
     模式。在程序中，通过全局变量ngx_process的值来判断Nginx服务器当前的工作模式，进而启动
     相应模式的进程。

     启动多进程
         启动多进程的过程和执行一般的多进程程序是一样的，主要使用fork()函数，主进程就是我们前文提到的master_process，通过一个for循环来接受和处理外部信号，对Nginx服务的
         启停进行控制：产生出来的子进程就是我们前文提高的work_process，每个工作进程执行
         一个for循环来实现Nginx服务器对事件的接收与处理，以提供Nginx服务器的各项功能。
     
     Nginx的初始化
         main()函数是整个Nginx服务器程序执行起点。
</pre>
    
![](https://i.imgur.com/JCjLfUZ.jpg)

<pre> 
  5）Nginx的内存管理
    Nginx服务器在实际运行过程中占用系统内存很少，这说明程序在对内存管理方面采取的措施对降低
    系统内存开始是非常有效果的，这样的特点既是Nginx服务器博得广大用户青睐的主要原因之一。

    Nginx内存池本质上是一个链表结构，链表的每一个节点称为数据块，由ngx_pool_data结构体
    描述，我们需要对内存进行管理和分配，这依赖于ngx_pool结构体，可以认为该结构体描述了
    Nginx内存池的分配管理模块，遇到分配大数据内存的情况时，还要使用ngx_pool_large_t结构
    体，也形成了一个链表，挂接在ngx_pool_t结构体上便于Nginx程序的管理，分配的内存使用完成
    后，需要对内存进行释放和回收，

  6）Nginx工作进程
  7) Nginx的缓存机制
    Nginx服务器在主要提供了两大功能， 1：负载均衡  2：Web缓存。 

    Nginx与Squid组合
        Squid Cache是目前在大访问量的网站建设中应用非常广泛的WEB缓存服务器，它可以作为
    网页服务器的前置缓存服务器来缓存相关的请求数据，可以缓存公司资源为局域网内用户提供
    共享资源。但是Squid服务本身不支持在单台服务器同一端口下运行多个进程，这样的话就需要
    给每个Squid服务分配一台服务器设备，这样非常浪费资源。

   基于第三方模块ncache的缓存机制
      ncache是新浪公司的开源产品，作为Nginx服务器的一个HTTP模块进行开发，以实现更好的兼
   容性和可扩展性。
      ncache完全是一套定制化的产品，可满足快速部署，网络高并发量，数据海量存储量的需求，
   它使用先进的技术进行组合。

  8）Nginx、Apache Tomcat LightHttpd
    

  9) Nginx的指令
    proxy_pass指令
        扩展  proxy_pass_header
             proxy_hide_header
             proxy_pass_request_body
             proxy_pass_request_body
             proxy_set_header
        该指令用来设置被代理服务器的地址，可以是主机名称，IP地址加端口号等形式
        proxy_pass URL;

    Proxy Buffer的配置
        Proxy Buffer启用后，Nginx服务器异步的将被代理服务器的响应数据传递给客户端。
        Nginx服务器首先尽可能的从被代理服务器那里接受响应数据，放置在Proxy Buffer中,
        Buffer的大小由proxy_buffer_size执行和proxy_buffers指令决定。如果在接受过程
        中，发现Buffer没有足够大小来接受一次响应的数据，Nginx服务器会将部分接收到的数据
        零时存放在磁盘的零时文件中，一次响应数据接受完成或者Buffer已经装满后，Nginx服务器
        开始向客户端传输数据。
        每个Proxy Buffer装满数据后，在从开始想客户端发送一直到Proxy Buffer中的数据全部
        传输给客户端的整个过程中，它都处于BUSY状态，期间对它进行的其他操作都会失败。

        当Proxy Buffer关闭时，Nginx服务器只要接收到响应数据就会同步的传递给客户端，它
        本身不会读取完整的响应数据。
 
        proxy_buffering on | off
        proxy_buffers number size

    Proxy Cache的指令
        Proxy cache机制与缓存数据的产生和使用有很大关系。
        Proxy cache机制与缓存数据的产生和使用有很大关系. Proxy cache实际上是Nginx服务器
        提供的WEB缓存机制的一部分，

        Buffer和Cache虽然都是用于提供I/O吞吐率的，但是他们是一对不同的概念，Buffer主要
        用于传输效率不同步或者优先级不相同的设备之间传递数据，一般的通过对一方数据进行临时
        存放，再统一发送的办法传递给另一方，以降低进程之间的等待时间。

        Cache主要用于将硬盘上已有的数据在内存中国建立缓存数据，提高数据的访问效率，对于
        过期不用的缓存可以随时销毁，但不会销毁硬盘上的数据。

        在Nginx服务器中，Proxy Buffer和Proxy Cache都与代理服务相关，主要用来提供客户端
        与被代理服务器的交互效率。Proxy Buffer实现了被代理服务器响应数据的异步传输，
        Proxy Cache则主要实现Nginx服务器对客户端请求的快速响应。

        特别需要说明的是,Proxy cache机制依赖与Proxy Buffer机制，只有在Proxy Buffer
        开启的情况下Proxy Cache的配置才能发挥作用。

        在Nginx服务器中还提供了另一种被代理服务器数据缓存到本地的方法Proxy Store，与Proxy Cache的区别是，它对来自于被代理服务器的响应数据，尤其是静态数据进行简单的
        缓存，不支持缓存过期更新，内存索引建立等功能，但支持设置用户或者用户组对缓存数据
        的访问权限。
</pre>
