### 1 异步调用

##### 1.1 编写异步调用

###### 1) 在SpringBoot入口类上配置@EnableAsync注解开启异步处理

```java
@EnableAsync
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

###### 2) 创建AsyncTask类，分别在方法上配置@Async注解

```java
@Component
public class AsyncTask {
    @Async
    public void doTaskOne() throws Exception {
        System.out.print("task one");
    }

    @Async
    public void doTaskTwo() throws Exception {
        System.out.print("task two");
    }

    @Async
    public void doTaskThree() throws Exception {
        System.out.print("task three");
    }
}
```

###### 3) 在单元测试中顺序调用doTaskOne、doTaskTwo、doTaskThree

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class AsyncTaskTest {
    @Autowired
    private AsyncTask task;

    @Test
    public void testAsyncTasks() throws Exception {
        task.doTaskOne();
        task.doTaskTwo();
        task.doTaskThree();
      	System.out.print("in the end");
    }
}
```

###### 4) 执行结果

```
in the end

task two

task three

task one
```

如果反复测试可能会得到不同的结果，task中的顺序无法确保，而in the end几乎总是最先完成。

##### 1.2 编写并行调用(等待并行结果)

###### 1) 在SpringBoot入口类上配置@EnableAsync注解开启异步处理。

```java
@SpringBootApplication
@EnableAsync
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

###### 2) 创建AsyncTask类，分别在方法上配置@Async注解，每个方法返回Future<T>异步调用结果。

```java
@Component
public class AsyncCallBackTask {
    @Async
    public Future<String> doTaskOneCallback() throws Exception {
        System.out.print("task one");
        return new AsyncResult<>("finish one");
    }

    @Async
    public Future<String> doTaskTwoCallback() throws Exception {
        System.out.print("task two");
        return new AsyncResult<>("finish two");
    }

    @Async
    public Future<String> doTaskThreeCallback() throws Exception {
        System.out.print("task three");
        return new AsyncResult<>("finish three");
    }
}
```

###### 3) 在单元测试中顺序调用doTaskOne、doTaskTwo、doTaskThree，使用自旋和Future的isDone来等待所有异步任务完成。

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class AsyncCallBackTaskTest {
    @Autowired
    private AsyncCallBackTask task;

    @Test
    public void testAsyncCallbackTask() throws Exception {
        Future<String> task1 = task.doTaskOneCallback();
        Future<String> task2 = task.doTaskTwoCallback();
        Future<String> task3 = task.doTaskThreeCallback();

        // 三个任务都调用完成，退出循环等待
        while (!task1.isDone() || !task2.isDone() || !task3.isDone()) {
            sleep(1000);
        }

      	System.out.print("in the end");
    }
}
```

###### 4) 执行结果

```
task two

task three

task one

in the end
```

如果反复测试可能会得到不同的结果，task中的顺序无法确保，而in the end每次总是最后完成。

##### 1.3 原理

@Async与@Transactional的原理类似，如果开启了@EnableAsync注解，那么Spring的AOP会拦截所有@Async注解的所有方法和类。如果使用@Async注解修饰类，则表示该类所有方法都是异步。如果使用@Async注解修饰某个方法，那么表示该方法是异步。

##### 1.4 注意事项

-  注解在非public方法会失效。

-  注解在相同类中调用会失效(可以使用AopContext.currentProxy())。

- 异步方法使用注解@Async的返回值只能为void或者Future。

------



### 2 Spring线程池

##### 2.1 Spring线程池类型

###### 1) SimpleAsyncTaskExecutor

不是真的线程池，这个类不重用线程，每次调用都会创建一个新的线程，默认会使用此策略。

###### 2) SyncTaskExecutor

这个类并没有实现异步调用，而是一个同步操作，Spring Event默认使用此策略。

###### 3) ConcurrentTaskExecutor

Executor的适配类，不推荐使用。如果ThreadPoolTaskExecutor不满足要求时，才用考虑使用这个类。

###### 4) SimpleThreadPoolTaskExecutor

是Quartz的SimpleThreadPool的类。线程池同时被quartz和非quartz使用，才需要使用此类。

###### 5) ThreadPoolTaskExecutor

最常使用，推荐。 其实质是对java.util.concurrent.ThreadPoolExecutor的包装。

##### 2.2 默认策略

###### 1) 默认线程池

默认使用SimpleAsyncTaskExecutor线程池，会针对每个任务新建一个线程，运行完线程就停止。

另外，SimpleAsyncTaskExecutor提供了限流机制，通过concurrencyLimit属性来控制开关，当concurrencyLimit>=0时开启限流机制，默认关闭限流机制即concurrencyLimit=-1，当关闭情况下，会不断创建新的线程来处理任务。

###### 2) ThreadPoolTaskExecutor默认配置

```properties
# 核心线程数
spring.task.execution.pool.core-size=8
# 最大线程数
spring.task.execution.pool.max-size=2147483647
# 临时线程空闲时存活时间
spring.task.execution.pool.keep-alive=60s
# 是否允许核心线程超时
spring.task.execution.pool.allow-core-thread-timeout=true
# 线程队列数量
spring.task.execution.pool.queue-capacity=2147483647
# 线程关闭等待
spring.task.execution.shutdown.await-termination=false
spring.task.execution.shutdown.await-termination-period=
# 线程名前缀
spring.task.execution.thread-name-prefix=task-
# 默认拒绝策略是AbortPolicy
```

##### 2.3 使用默认线程池

```java
@Component
public class AsyncCallBackTask {
  
    @Async
    public Future<String> doTaskOneCallback() throws Exception {
        System.out.print("task one");
        return new AsyncResult<>("finish one");
    }

    @Async
    public Future<String> doTaskTwoCallback() throws Exception {
        System.out.print("task two");
        return new AsyncResult<>("finish two");
    }
    
    @Async
    public Future<String> doTaskThreeCallback() throws Exception {
        System.out.print("task three");
        return new AsyncResult<>("finish three");
    }

}
```

##### 2.4 使用自定义线程池

###### 1) @Async默认选择线程池规则

1. 查找实现了TaskExecutor接口的Bean实例。
2. 如果上面没有找到，则查找名称为taskExecutor并且实现了Executor接口的Bean实例。
3. 如果还是没有找到，则使用SimpleAsyncTaskExecutor，该实现每次都会创建一个新的线程执行任务。

###### 2) @Async指定使用的线程池id

```java
@Configuration
public class TaskConfiguration {
  
    @Bean("taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(20);
        executor.setQueueCapacity(200);
        executor.setKeepAliveSeconds(60);
        executor.setThreadNamePrefix("taskExecutor-");
        executor.setRejectedExecutionHandler(new CallerRunsPolicy());
        return executor;
    }
  
		@Bean("selfTaskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(20);
        executor.setQueueCapacity(200);
        executor.setKeepAliveSeconds(60);
        executor.setThreadNamePrefix("selfTaskExecutor-");
        executor.setRejectedExecutionHandler(new CallerRunsPolicy());
        return executor;
    }
  
}
```

```java
@Component
public class AsyncCallBackTask {

		// 使用taskExecutor
    @Async
    public Future<String> doTaskOneCallback() throws Exception {
        System.out.print("task one");
        return new AsyncResult<>("finish one");
    }
    
		// 使用taskExecutor
    @Async("taskExecutor")
    public Future<String> doTaskTwoCallback() throws Exception {
        System.out.print("task two");
        return new AsyncResult<>("finish two");
    }
    
		// 使用selfTaskExecutor
    @Async("selfTaskExecutor")
    public Future<String> doTaskThreeCallback() throws Exception {
        System.out.print("task three");
        return new AsyncResult<>("finish three");
    }

}
```

##### 2.5 优雅关闭线程池

```java
@Bean("taskExecutor")
public Executor taskExecutor() {
    ThreadPoolTaskScheduler executor = new ThreadPoolTaskScheduler();
    executor.setPoolSize(20);
    executor.setThreadNamePrefix("taskExecutor-");
    executor.setWaitForTasksToCompleteOnShutdown(true);
    executor.setAwaitTerminationSeconds(60);
    return executor;
}
```

- setWaitForTasksToCompleteOnShutdown(true)：该方法用来设置线程池关闭的时候等待所有任务都完成后，再继续销毁其他的Bean，这样这些异步任务的销毁就会先于数据库连接池对象的销毁。
- setAwaitTerminationSeconds(60)：该方法用来设置线程池中任务的等待时间，如果超过这个时间还没有销毁就强制销毁，以确保应用最后能够被关闭，而不是阻塞住。
