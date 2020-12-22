# IOS多线程(一)

## 多线程的作用

显示\刷新UI界面

处理UI事件（比如点击事件、滚动事件、拖拽事件等）

## 主线程的使用注意

别将比较耗时的操作放到主线程中

耗时操作会卡住主线程，严重影响UI的流畅度，给用户一种“卡”的坏体验

​        ![img](https://uploader.shimo.im/f/1bbexBKdUSY1mB8y.png!thumbnail)      

注意:主线程在处理比较耗时的一些事件的时候(例:下载),它就不会再去处理一些其他的事件,例如用户点击了按钮或者拖拽了页面.(在同一个时间,它只能处理一个事件) 

​        ![img](https://uploader.shimo.im/f/NkBUdyh9t8QKwCDb.png!thumbnail)      

iOS中多线程的实现方案

​        ![img](https://uploader.shimo.im/f/xnFXbOPSEo8G0KWv.png!thumbnail)      

## GCD

### 任务和队列

GCD中有2个核心概念:任务、队列

GCD的使用就2个步骤：1.定制任务2.将任务添加到队列中

GCD会自动将队列中的任务取出，放到对应的线程中执行，任务的取出遵循队列的FIFO原则：先进先出，后进后出

​        ![img](https://uploader.shimo.im/f/Njs7eioyMhzOUf2U.png!thumbnail)      

# 执行任务

GCD中有2个用来执行任务的函数

用同步的方式执行任务

dispatch_sync(dispatch_queue_t queue, dispatch_block_t block); 

异步执行

dispatch_async(dispatch_queue_t queue, dispatch_block_t block); 

​        ![img](https://uploader.shimo.im/f/Op0wYaT5Cg2UTSPB.png!thumbnail)      

# 队列的类型

GCD的队列可以分为2大类型

并发队列（Concurrent Dispatch Queue）

可以让多个任务并发（同时）执行（自动开启多个线程同时执行任务）

注意:  并发功能只有在异步（dispatch_async）函数下才有效 

串行队列（Serial Dispatch Queue）

让任务一个接着一个地执行（一个任务执行完毕后，再执行下一个任务）

​        ![img](https://uploader.shimo.im/f/X7j7JorvwpjtOFvL.png!thumbnail)      

​        ![img](https://uploader.shimo.im/f/NAFTToMMmUbMQHtv.png!thumbnail)      