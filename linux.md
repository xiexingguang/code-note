别急然后自己又梳理了下,因为我们知道你中断work空闲线程的场景要么是在`shutdown`方法里要么在`tryTerminate`方法中 。线程池其他方法里面没有场景说是要中断空闲线程。那么我们看看 `shutdown`源码、


```java
 /**
     * Initiates an orderly shutdown in which previously submitted
     * tasks are executed, but no new tasks will be accepted.
     * Invocation has no additional effect if already shut down.
     *
     * <p>This method does not wait for previously submitted tasks to
     * complete execution.  Use {@link #awaitTermination awaitTermination}
     * to do that.
     *
     * @throws SecurityException {@inheritDoc}
     */
    public void shutdown() {
        final ReentrantLock mainLock = this.mainLock;
        mainLock.lock();
        try {
            checkShutdownAccess();
            advanceRunState(SHUTDOWN); //设置线程池状态
            interruptIdleWorkers(); //中断空闲线程
            onShutdown(); // hook for ScheduledThreadPoolExecutor
        } finally {
            mainLock.unlock();
        }
        tryTerminate();

```

 可以看到方法做了中断空闲线程,最后有个`tryTerminate();`方法。该方法为尝试去终止线程池。那么我
 
 们可以知道一旦线程收到中断信号那么我们可以肯定的是线程池状态至少为`SHUTDOWN` 或者是`STOP` 
 
 我们可以看到shutdown方法调用`interruptIdleWorkers`方法并不见得空闲线程就会被中断掉。因为我
 
 们知道空闲线程要返回必须`getTask`方法返回null 才行。而且`getTask`方法捕获了中断异常。那么我
 
 们看看getTask方法中返回null得情况。


