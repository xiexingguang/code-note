# 基础常识

>最近在面试,看了一些基础题目感觉挺有意思的,记录下自己的理解

## 1 equals,==
equals比较的是内容，==比较的是内存地址。Object类的equals方法比较的是地址.所以子类如果要实现自己的相等逻辑就必须重写equlas。因为不重写默认是父类的equals方法比较的是地址。

为什么重写equals时要重写hashCode?

+ 1. 因为在Object类中，equals比较的是地址,hashCode返回得也是对象的地址这样逻辑和物理一致。后续的子类也最好保证逻辑地址和物理地址一致。
+ 2. hashCode相等那么对象肯定是同一个对象。
+ 3. 重写equals方法只是保证后续保证我们的业务类的相同,比如我们认为学生年纪和名称相同就是同一个学生。所以我们要学生类重写equals去表达这个概念。但是重写完equals我们最好保证逻辑上相等的对象最好物理地址也相等。所以我们最好重写hashCode。

	
代码理解

```java
 HashSet hashSet = new HashSet();
        Student student = new Student(12, "jasshine");
        Student student1 = new Student(12, "jasshine");

        hashSet.add(student);
        hashSet.add(student1);
       
       
 class Student {

    private int age;
    private String name;

    public Student(int age, String name) {
        this.age = age;
        this.name = name;
    }
}
        
```
以hashset为例,我们知道它是里面的对象不重复的.但我们打印结果发现set里面存储了2个student对象。但是student对象明显是2个相同的对象啊,年龄姓名都一样。

解释：因为hashset 首先是通过对象的hashcode去查找地址,找到后在通过对象的equals去比较。所以由于student对象的hashcode是继承父类的所以比较的是地址。那么这2个对象的hashcode肯定是不一样的。所以会put进去。我们新建的对象需要让他们相等,这个是我们业务上认为的相同,所以我们需要重写equals方法
如下。

```java
 @Override
    public boolean equals(Object o) {
        if (o instanceof Student) {
            Student s1 = (Student)o;
            if (s1.age == age && s1.name.equals(name)) {
                return true;
            }
        }
        return false;
    }
     @Override
    public int hashCode() {
        return age*12;
    }																		
```
如果不重写hashcode 发现hashset里面存储的还是2个对象。为什么？这很好理解啊，因为虽然equals上你认为相等了,但是hashset用的hashcode做为key去查找。对象的hashCode不等用的还是父类的hashCode呀
所以必须重写hashCode方法

## 2.sleep,wait方法

这个2个方法在面试的时候80%能回答上sleep不会释放锁,wait会释放锁。60%的能回答上wait需要同步快里面,40%的人能回答上wait需要在`synchronized(o)`跟对象的`o.wait`要是同一个对象。几乎很少有人去
思考为什么wait方法要在同步代码块里面

```java 
  public static  Object o = new Object();

    public static void main(String[] args) {

        synchronized (o) {   //获取o对象的锁
            try {
                o.wait(1000); //当前线程在o对象上等待
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        synchronized (o) {
            o.notify();
        }
```
 我谈下自己的理解
 1.wait方法必须在`synchronized`原因是我们知道在java中线程间通信是通过共享对象通信的。
 试想下如果`wait`没有在`synchronized`下,那么我们如何去将该线程唤醒呢？由于`synchronized`的语义实际上是获取一个对象的监视器,我们可以理解为这个对象就是线程通信的共享对象。线程告诉共享对象说我要wait即`o.wait()`这个时候实际上可以理解如下伪代码
 
```java
Monitor o = new Monitor() //对象监视器
o.put("threadA","wait")
```
可以形象的去理解释放锁的感觉就是wait调用完把 o对象扔给B线程。

实际上是告诉该对象我这个线程现在是wait状态。这个时候wait后就释放了锁,即监视器o对象。这个时候其他线程拿到o对象 调用`o.notify()`方法   实际实现的伪代码如下

```java
Monitor o = new Monitor() //对象监视器
o.put("threadA","runnable") //修改线程
```
即notify方法实际上是修改监视器里面的状态。将线程置换为running. 这个时候B线程在将该对象扔给A线程。这个A线程拿到对象发现自己的状态是running 于是就变成运行状态了 等待OS调度去运行了。

下面有代码测试

```java

public class TestClassWait {

    private static Object o = new Object();
    public static void main(String[] args) throws InterruptedException {
        new Thread(new B(o)).start();
        Thread.sleep(3000); //让B线程充分运行
        synchronized (o) { //主线程获取锁
            System.out.println("wake up thread");
            o.notify();
            System.out.println("waiting release lock o");
            Thread.sleep(2000);
        }
        System.out.println("releas lock o");
    }

}


class B implements Runnable {
    public Object o ;
    public B(Object O) {
        this.o = O;
    }
    public void run() {
        synchronized (o) { //B线程获取对象锁o
            System.out.println(Thread.currentThread().getName() + " get lock , begin wait");
            try {
                o.wait();// Thread.sleep(2000);

            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println(Thread.currentThread().getName() + " was waked up");
        }
    }
}

```
执行结果

```json
Thread-0 get lock , begin wait
wake up thread
waiting release lock o
releas lock o  
Thread-0 was waked up

```
可以看到releas lock o  一定在前面,即使B线程被唤醒了,但是由于锁还是主线程持有,B线程依然是不能处于runnable状态的。直到B线程获取了锁才可以。

