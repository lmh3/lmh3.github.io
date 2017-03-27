---
layout: post
title: ThreadPool
categories: Java
tags:
- ThreadPool
description: ThreadPool
---

#### 一、Excutor

```
public interface Executor {

    /**
     * Executes the given command at some time in the future.  The command
     * may execute in a new thread, in a pooled thread, or in the calling
     * thread, at the discretion of the {@code Executor} implementation.
     *
     * @param command the runnable task
     * @throws RejectedExecutionException if this task cannot be
     * accepted for execution
     * @throws NullPointerException if command is null
     */
    void execute(Runnable command);
}
```

Executor是线程池的顶级接口，只是定义了执行Runnable任务对象的方法。Executor接口并没有严格地要求执行是异步的。在最简单的情况下，执行程序可以在调用方的线程中立即运行已提交的任务。

#### 二、ExecutorService

真正定义了管理线程池的方法。

* `void shutdown()` 关闭线程池，拒绝接受新任务，允许执行以前提交的任务。
* `List<Runnable> shutdownNow()` 关闭线程池，试图停止所有正在执行的活动任务，暂停处理正在等待的任务，并返回等待执行的任务列表。
* `<T> Future<T> submit(Callable<T> task)`  提交一个返回值的任务用于执行，返回一个表示任务的未决结果的 Future。该 Future 的 get 方法在成功完成时将会返回该任务的结果。
* `<T> List<Future<T>> invokeAll(Collection<? extends Callable<T>> tasks,long timeout, TimeUnit unit)`  执行给定的任务列表，当所有任务完成或超时期满时（无论哪个首先发生），返回保持任务状态和结果的 Future 列表
* `<T> T invokeAny(Collection<? extends Callable<T>> tasks,long timeout, TimeUnit unit)`  执行给定的任务，如果在给定的超时期满前某个任务已成功完成（也就是未抛出异常），则返回其结果。一旦正常或异常返回后，则取消尚未完成的任务。如果此操作正在进行时修改了给定的 collection，则此方法的结果是不确定的。

#### 三、ThreadPoolExecutor
ThreadPoolExecutor是ExecutorService的一个实现类。

ThreadPoolExecutor构造函数：

```
    /**
     * Creates a new {@code ThreadPoolExecutor} with the given initial
     * parameters.
     *
     * @param corePoolSize the number of threads to keep in the pool, even
     *        if they are idle, unless {@code allowCoreThreadTimeOut} is set
     * @param maximumPoolSize the maximum number of threads to allow in the
     *        pool
     * @param keepAliveTime when the number of threads is greater than
     *        the core, this is the maximum time that excess idle threads
     *        will wait for new tasks before terminating.
     * @param unit the time unit for the {@code keepAliveTime} argument
     * @param workQueue the queue to use for holding tasks before they are
     *        executed.  This queue will hold only the {@code Runnable}
     *        tasks submitted by the {@code execute} method.
     * @param threadFactory the factory to use when the executor
     *        creates a new thread
     * @param handler the handler to use when execution is blocked
     *        because the thread bounds and queue capacities are reached
     * @throws IllegalArgumentException if one of the following holds:<br>
     *         {@code corePoolSize < 0}<br>
     *         {@code keepAliveTime < 0}<br>
     *         {@code maximumPoolSize <= 0}<br>
     *         {@code maximumPoolSize < corePoolSize}
     * @throws NullPointerException if {@code workQueue}
     *         or {@code threadFactory} or {@code handler} is null
     */
    public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory,
                              RejectedExecutionHandler handler) {
        if (corePoolSize < 0 ||
            maximumPoolSize <= 0 ||
            maximumPoolSize < corePoolSize ||
            keepAliveTime < 0)
            throw new IllegalArgumentException();
        if (workQueue == null || threadFactory == null || handler == null)
            throw new NullPointerException();
        this.corePoolSize = corePoolSize;
        this.maximumPoolSize = maximumPoolSize;
        this.workQueue = workQueue;
        this.keepAliveTime = unit.toNanos(keepAliveTime);
        this.threadFactory = threadFactory;
        this.handler = handler;
    }
```

* `corePoolSize` 核心线程数。提交任务时，如果运行的线程少于corePoolSize，则创建新线程来处理请求，即使有线程是空闲的。并不是一开始就创建corePoolSize个core线程。
* `maximumPoolSize` 最大线程数。如果运行的线程多于corePoolSize 而少于 maximumPoolSize，则仅当队列满时才创建新线程。
* `keepAliveTime` 保持活动时间。如果线程空闲时间超过keepAliveTime，就停掉该线程，对该线程进行回收。该策略默认只对当前线程数大于corePoolSize的情况使用，可以使用allowsCoreThreadTimeOut扩大到核心线程。
* `unit` keepAliveTime的单位。
* `workQueue` 用于保存任务的队列。此队列仅保持由execute方法提交的Runnable任务。 可以使用LinkedBlockingQueue无界队列，也可以使用ArrayBlockingQueue等有界队列。
* `ThreadFactory` 使用ThreadFactory创建新线程。如果没有另外说明，则使用 Executors.defaultThreadFactory() 创建线程，他们在同一个ThreadGroup中并且这些线程具有相同的 NORM_PRIORITY 优先级和非守护进程状态。通过提供不同的 ThreadFactory，可以改变线程的名称、线程组、优先级、守护进程状态，等等。
* `handler` 拒绝策略。当Executor已经关闭，或Executor将有限边界用于最大线程和工作队列容量，且已经饱和时，将调用RejectedExecutionHandler处理。默认有四种实现：AbortPolicy，CallerRunsPolicy，DiscardPolicy和DiscardOldestPolicy。

任务执行：

```
public void execute(Runnable command) {
        if (command == null)
            throw new NullPointerException();
        /*
         * Proceed in 3 steps:
         *
         * 1. If fewer than corePoolSize threads are running, try to
         * start a new thread with the given command as its first
         * task.  The call to addWorker atomically checks runState and
         * workerCount, and so prevents false alarms that would add
         * threads when it shouldn't, by returning false.
         *
         * 2. If a task can be successfully queued, then we still need
         * to double-check whether we should have added a thread
         * (because existing ones died since last checking) or that
         * the pool shut down since entry into this method. So we
         * recheck state and if necessary roll back the enqueuing if
         * stopped, or start a new thread if there are none.
         *
         * 3. If we cannot queue task, then we try to add a new
         * thread.  If it fails, we know we are shut down or saturated
         * and so reject the task.
         */
        int c = ctl.get();
        if (workerCountOf(c) < corePoolSize) {
            if (addWorker(command, true))
                return;
            c = ctl.get();
        }
        if (isRunning(c) && workQueue.offer(command)) {
            int recheck = ctl.get();
            if (! isRunning(recheck) && remove(command))
                reject(command);
            else if (workerCountOf(recheck) == 0)
                addWorker(null, false);
        }
        else if (!addWorker(command, false))
            reject(command);
    }
```

#### 四、Excutors

Excutors是线程池的工厂类，常用的有：

* `newSingleThreadExecutor`

```
public static ExecutorService newSingleThreadExecutor() {
        return new FinalizableDelegatedExecutorService
            (new ThreadPoolExecutor(1, 1,
                                    0L, TimeUnit.MILLISECONDS,
                                    new LinkedBlockingQueue<Runnable>()));
    }
```
创建一个单线程的线程池，corePoolSize和maximumPoolSize都是1,永不过期，无界队列。

* `newCachedThreadPool`

```
public static ExecutorService newCachedThreadPool() {
        return new ThreadPoolExecutor(0, Integer.MAX_VALUE,
                                      60L, TimeUnit.SECONDS,
                                      new SynchronousQueue<Runnable>());
    }
```
corePoolSize是0，maximumPoolSize是MAX_VALUE，无界线程池，可以进行自动线程回收。

* `newFixedThreadPool`

```
public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
    }
```
固定大小的线程池。corePoolSize和maximumPoolSize相同，都为固定值。永不过期，无界队列。


