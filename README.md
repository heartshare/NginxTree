# NginxTree
Nginx技术研究

<pre>
Nginx服务器架构
  1）Nginx服务器的WEB请求处理机制
     多进程 
       一个请求到来，web服务器主进程生成一个子进程处理请求
       新建一个子进程开销大
       apache早期采用实时生成子进程，后期采用预生成子进程机制
     多线程 
       一个请求到来，web服务器主进程派生一个线程处理请求
       缺点：所有线程位于同一个进程内，资源共享，相互影响
     异步方式
       1）同步阻塞方式
       2）同步非阻塞方式
       3）异步非阻塞方式
     Nginx方式
       结合多进程&&异步机制
       Nginx当前采用的是master-worker模型（single模型性能差）
       
  2）Nginx的事件驱动模型
     select库 poll库 epoll库 rtsig模型
  3）Nginx的启动初始化
  5）Nginx的内存管理
  6）Nginx工作进程
  7) Nginx的缓存机制
  8）Nginx、Apache Tomcat LightHttpd
  9) Nginx的指令
</pre>
