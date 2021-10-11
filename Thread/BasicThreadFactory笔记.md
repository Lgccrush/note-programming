### BasicThreadFactory笔记

可以设置为daemon线程 和 设置线程名字

Daemon线程是一种支持类型的线程，因为它主要被用作程序中后台调度以及支持性工作，这意味着，当一个java虚拟机中不存在非Daemon线程的时候，JAVA虚拟机将会退出，可以通过调用Thread.setDaemon(true)将线程设置为Daemon线程

注意：Daemon属性需要在启动线程之前设置，不能再线程启动之后设置

使用

```java
    /**
     * worker线程池配置
     */
    private static final int workerThreadNum = 4;
    private static final String workerThreadName = "OMSReceiverWorker-Pool-%d";
 private static ExecutorService workerThreadPool;
BasicThreadFactory build = new 		BasicThreadFactory.Builder().namingPattern(workerThreadName).daemon(true).build();

        workerThreadPool = new ThreadPoolExecutor(workerThreadNum
                , workerThreadNum
                , 0L
                , TimeUnit.SECONDS
                , new LinkedBlockingQueue<Runnable>()
                , build
                , new ThreadPoolExecutor.AbortPolicy());
```

