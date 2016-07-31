#线程
##1 线程wait,notify理解
###1.1 why约定wait,notify需要在同步块里面调用？
首先看如下代码：
![](media/14699329834131.jpg)
	看在调用wait的时候,需要先进入synchronized区域里面，即获取对象goods的锁，表示当前线程在goods对象上等待。goods.wait 可以这样理解，表示当前线程向goods发了一个wait信号，goods我们可以看成共享对象，由此可以想到，wait,notify其实就是2个线程通信，通过共享对象goods进行通信,所以在jdk里面需要一个线程在该对象wait,必须通过同一个对象发送notify信号才能唤醒。有木有深刻get到？这个其实是jvm不同线程通信的模型，通过共享内存通信。在想一下,这个模式在我们身边随处可见呐。比如2个系统要进行通信,常见做法是通过消息队列,数据库也可以。那么这个消息队列和数据库就相当于共享对象了。有木有？
	


###1.2 为什么wait,notify方法在Object类里面？
这个很好理解啊，你想一下,看上面代码，线程等待被唤醒,其实就是个通信过程。通过共享对象进行通信,那么上面的理解就为A线程拿到对象goods的锁了,然后给goods.wait，即给goods发送了一个wait的消息。这个就表示当前线程告诉了goods了，说我要等待了的消息。然后线程A放弃了goods的锁了,等待了。然后另外一个线程B拿到goods的锁,goods.notify即表示,向goods发送一个消息表示要唤醒的消息。那么A线程感知到goods有notify消息的时候,表示有线程要唤醒我,于是就是等待队列切换到可执行队列里面,即具备执行了权限。可见,2个线程等待唤醒，都是通过goods的wait,notify传递信息。即共享对象。那么wait,notify方法肯定是定义在object里面。


##2 interrupt中断理解
###2.1 interrupt方法
代码片段
`thread.interrupt()` 该方法表示给thread发一个中断信号，注意理解中断，中断的意思就是阻止当前线程的执行状态，状态有可能是在执行中,有可能是处于wait中,例如线程如果调用了wait,sleep,join，这个时候中断表示使线程从中断状态中醒来。当这个方法被调用,会将flag置为ture,jdk会扫描该flag,如果发现变量为ture,则响应中断,如果线程之前是wait状态的时候，那么响应中断就是抛出interruptException异常,同时将falg设置为false.如下：
try{
   object.wait() //被中断后会抛出InterruptException异常
}catch(InterruptException){
}
  
###2.2 thread.isInterrupted()
查看线程中断状态。如果调用了thread.interrupt()后，会将状态设置为ture.但有时候我们发现明显我们已经调用thread.interrupt方法,可是为什么thread.isInterrupted方法返回的还是false呢？这里其实可以理解，因为你调用完后,线程响应了中断状态。已经将中断标记复位了，即为false了。
##3 join理解
`thread.join()` 表示的意思线程加入到当前线程中，一定要理解当前线程这个概念，当前线程就是运行thrad.join代码的这个线程，join的意思就是将当前线程阻塞，调度给thread线程运行。但是有个前提条件就是thread必须是alive的。也就是thread必须start状态的。






