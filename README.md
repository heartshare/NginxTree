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
  3）Nginx的启动初始化
  5）Nginx的内存管理
  6）Nginx工作进程
  7) Nginx的缓存机制
  8）Nginx、Apache Tomcat LightHttpd
  9) Nginx的指令
</pre>
