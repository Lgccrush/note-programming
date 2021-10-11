### spring @Async

Simply put, annotating a method of a bean with @Async will make it execute in a separate thread. In other words, the caller will not wait for the completion of the called method

简而言之，使用@Async 注释bean 的方法将使其在单独的线程中执行。换句话说，调用者不会等待被调用方法的完成

#### 使用

在配置类上加@EnableAsync

```java
@SpringBootApplication
@EnableAsync
public class SpringBootDockerTestApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootDockerTestApplication.class, args);
    }

}

```

在调用的方法上加上@Async

```java
  	@Async
    public void asyncTest() {
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
          //
        }
        System.out.println(StrUtil.format("execute async,currentThread-->{}", Thread.currentThread().getName()));
    }
```

#### 使用限制

It must be applied to public methods only.
Self-invocation — calling the async method from within the same class — won't work.
The reasons are simple: The method needs to be public so that it can be proxied. And self-invocation doesn't work because it bypasses the proxy and calls the underlying method directly.

方法必须是public 才能被代理

默认情况下（即@EnableAsync注解的mode=AdviceMode.PROXY），同一个类内部没有使用@Async注解修饰的方法调用@Async注解修饰的方法，是不会异步执行的，如果想实现类内部自调用也可以异步，则需要切换@EnableAsync注解的mode=AdviceMode.ASPECTJ

####  错误的使用情况

```java
//同一个类中   
@GetMapping("/async")
    public String async(){
        System.out.println("Invoking an asynchronous method. "
                + Thread.currentThread().getName());
        asyncTest();
        System.out.println("结束");
        return "结束";
    }

    @Async
    public void asyncTest() {
        try {
            Thread.sleep(2000);
        } catch (InterruptedException e) {
          //
        }
        System.out.println(StrUtil.format("execute async,currentThread-->{}", Thread.currentThread().getName()));
    }
```

控制台打印 是同一个线程

```
Invoking an asynchronous method. http-nio-8080-exec-2
execute async,currentThread-->http-nio-8080-exec-2
结束
```

#### 正确使用方式

- 无返回值

```java
  @GetMapping("/async1")
    public void testAsyncAnnotationForMethodsWithReturnType()
            throws InterruptedException, ExecutionException {
        System.out.println("Invoking an asynchronous method. "
                + Thread.currentThread().getName());
        asyncTest.asyncMethodWithReturnType();
        System.out.println("Continue doing something else. ");
    }
```

```java
@Component
public class AsyncTest {
    @Async
    public void asyncMethodWithReturnType() {
        System.out.println("Execute method asynchronously - "
                + Thread.currentThread().getName());
        String s = "hello world !!!!";
        try {
            Thread.sleep(2000);
            System.out.println(s);
         
        } catch (InterruptedException e) {
            //
        }
     }
}
```

控制台输出

```java
Invoking an asynchronous method. http-nio-8080-exec-4
Continue doing something else. 
Execute method asynchronously - default asyncExecutor-1
hello world !!!!
```

可以设置指定线程池

```java
public class SpringAsyncConfig implements AsyncConfigurer {
    //返回全局的async线程池
    @Override
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10); // 核心线程数10：线程池创建时候初始化的线程数
        executor.setMaxPoolSize(20); // 最大线程数20：线程池最大的线程数，只有在缓冲队列满了之后才会申请超过核心线程数的线程
        executor.setQueueCapacity(200); // 缓冲队列200：用来缓冲执行任务的队列
        executor.setKeepAliveSeconds(60); // 允许线程的空闲时间60秒：当超过了核心线程出之外的线程在空闲时间到达之后会被销毁
        executor.setThreadNamePrefix("default asyncExecutor-"); // 线程池名的前缀：设置好了之后可以方便我们定位处理任务所在的线程池
        /**
         * 线程池对拒绝任务的处理策略：这里采用了CallerRunsPolicy策略，当线程池没有处理能力的时候，该策略会直接在 execute 方法的调用线程中运行被拒绝的任务；如果执行程序已关闭，则会丢弃该任务
         */
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        executor.initialize();
        return executor;
    }
    //Async无返回值的异常捕获
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new CustomAsyncExceptionHandler();
    }
}
```

自定义Async无返回值的异常捕获

```java
public class CustomAsyncExceptionHandler implements AsyncUncaughtExceptionHandler {

    @Override
    public void handleUncaughtException(
            Throwable throwable, Method method, Object... obj) {
        System.out.println("current thread -"+Thread.currentThread().getName());
        System.out.println("Exception message - " + throwable.getMessage());
        System.out.println("Method name - " + method.getName());
        for (Object param : obj) {
            System.out.println("Parameter value - " + param);
        }
    }
}
```

- 有返回值

  ```java
  		@GetMapping("/asyncFuture")
      public void testAsyncAnnotationForMethodsWithReturnType()
              throws InterruptedException, ExecutionException {
          System.out.println("Invoking an asynchronous method. "
                  + Thread.currentThread().getName());
          Future future = asyncTest.asyncMethodWithReturnType();
          while (true) {
              if (future.isDone()) {
                  System.out.println("Result from asynchronous process - " + future.get());
                  break;
              }
              System.out.println("Continue doing something else. ");
              Thread.sleep(1000);
          }
      }
  ```

  ```java
  @Component
  public class AsyncTest {
      @Async
      public Future<String> asyncMethodWithReturnType() {
          System.out.println("Execute method asynchronously - "
                  + Thread.currentThread().getName());
        
       	 try {
              Thread.sleep(2000);
              System.out.println(s);
              return new AsyncResult<>("hello world !!!!");
  
          } catch (InterruptedException e) {
              //
          }
            return null;
      }
  ```

  控制台打印

  ```
  Invoking an asynchronous method. http-nio-8080-exec-1
  Continue doing something else. 
  Execute method asynchronously - default asyncExecutor-1
  Continue doing something else. 
  Continue doing something else. 
  hello world !!!!
  Result from asynchronous process - hello world !!!!
  ```

  

  

