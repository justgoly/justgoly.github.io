### 并行与并发的区别

并发和并行是两个相关但不同的概念。并发是指在一段时间内，多个程序可以交替执行。并行是指在某一时刻，多个程序可以同时执行。并发是操作系统通过上下文切换实现的一种现象，而并行是硬件通过多核处理器或多台机器实现的一种能力。

简单来说，并发是在一段时间内宏观上多个程序同时运行，而并行是在某一时刻，真正有多个程序在运行。

## 线程

### 1.1进程

**进程**就是正在运行中的程序（进程是驻留在内存中的）

*   是系统执行资源分配和调度的独立单位
*   每一进程都有属于自己的存储空间和系统资源
*   注意：进程A和进程B的内存独立不共享。

### 1.2线程

**线程**就是进程中的单个顺序控制流，也可以理解成是一条执行路径

*   单线程：一个进程中包含一个顺序控制流（一条执行路径）
*   多线程：一个进程中包含多个顺序控制流（多条执行路径）
*   在java语言中：\
     线程A和线程B，堆内存和方法区内存共享。\
     但是栈内存独立，一个线程一个栈。
*   对于单核的CPU来说，不能够做到真正的多线程并发，但是可以做到给人一种“多线程并发”的感觉。对于单核的CPU来说，在某一个时间点上实际上只能处理一件事情，但是由于CPU的处理速度极快，多个线程之间频繁切换执行，跟人来的感觉是多个事情同时在做

    线程分为用户线程和守护线程
*   虚拟机必须确保用户线程执行完毕
*   虚拟机不用等待守护线程执行完毕
*   正常线程都是用户线程，gc是守护线程

### 1.3多线程中的生命周期

*   **就绪状态**：就绪状态的线程又叫做可运行状态，表示当前线程具有抢夺CPU时间片的权力（CPU时间片就是执行权）。当一个线程抢夺到CPU时间片之后，就开始执行run方法，run方法的开始执行标志着线程进入运行状态。

*   **运行状态**：run方法的开始执行标志着这个线程进入运行状态，当之前占有的CPU时间片用完之后，会重新回到就绪状态继续抢夺CPU时间片，当再次抢到CPU时间之后，会重新进入run方法接着上一次的代码继续往下执行。

*   **阻塞状态**：当一个线程遇到阻塞事件，例如接收用户键盘输入，或者sleep方法等，此时线程会进入阻塞状态，阻塞状态的线程会放弃之前占有的CPU时间片。之前的时间片没了需要再次回到就绪状态抢夺CPU时间片。

*   **锁池**：在这里找共享对象的对象锁线程进入锁池找共享对象的对象锁的时候，会释放之前占有CPU时间片，有可能找到了，有可能没找到，没找到则在锁池中等待，如果找到了会进入就绪状态继续抢夺CPU时间片。（这个进入锁池，可以理解为一种阻塞状态）

*   &#x20;**死亡：线程结束**

### 1.4多线程的实现方式

1.***继承Thread类***

1、自定义一个类MyThread类，用来继承与Thread类

2、在MyThread类中重写run（）方法

3、在测试类中创建MyThread类的对象

4、启动线程

```java
public class thread extend Thread{
    @Override
    public void run() {
        for(int i=0;i<100;i++){
            System.out.println(Thread.currentThread().getName()+"now:"+i);
        }
    }

    public static void main(String[] args) {
        thread th=new thread();
        th.start;
    }
}
```

2.***实现Runnable接口(推荐常用)***

1、自定义一个MyRunnable类来实现Runnable接口

2、在MyRunnable类中重写run（）方法

3、创建Thread对象，并把MyRunnable对象作为Tread类构造方法的参数传递进去

4、启动线程

```java
public class thread implements Runnable{
    @Override
    public void run() {
        for(int i=0;i<100;i++){
            System.out.println(Thread.currentThread().getName()+"now:"+i);
        }
    }

    public static void main(String[] args) {
        thread th=new thread();
        new Thread(th,"小明").start();
        new Thread(th,"小hong").start();
    }
}
```

2.***实现Callable接口（不常用）***

1、自定义一个MyCallable类来实现Callable接口

2、在MyCallable类中重写call()方法

3、创建FutureTask，Thread对象，并把MyCallable对象作为FutureTask类构造方法的参数传递进去，把FutureTask对象传递给Thread对象。

4、启动线程

    优点：可以获取到线程的执行结果。

    缺点：效率比较低，在获取t线程执行结果的时候，当前线程受阻塞，效率较低。

```java
public class Demo04 {
    public static void main(String[] args) throws Exception {
 
        // 第一步：创建一个“未来任务类”对象。
        // 参数非常重要，需要给一个Callable接口实现类对象。
        FutureTask task = new FutureTask(new Callable() {
            @Override
            public Object call() throws Exception { // call()方法就相当于run方法。只不过这个有返回值
                // 线程执行一个任务，执行之后可能会有一个执行结果
                // 模拟执行
                System.out.println("call method begin");
                Thread.sleep(1000 * 10);
                System.out.println("call method end!");
                int a = 100;
                int b = 200;
                return a + b; //自动装箱(300结果变成Integer)
            }
        });
 
        // 创建线程对象
        Thread t = new Thread(task);
 
        // 启动线程

        t.start();
 
        // 这里是main方法，这是在主线程中。
        // 在主线程中，怎么获取t线程的返回结果？
        // get()方法的执行会导致“当前线程阻塞”
        Object obj = task.get();
        System.out.println("线程执行结果:" + obj);
        // main方法这里的程序要想执行必须等待get()方法的结束
        // 而get()方法可能需要很久。因为get()方法是为了拿另一个线程的执行结果
        // 另一个线程执行是需要时间的。
        System.out.println("hello world!");
    }
}
```

### 1.5 - 设置和获取线程名

*   设置线程名

    *   `setName(String name)`:设置线程名
    *   通过带参构造方法设置线程名
*   获取线程名

    *   `getName()`：返回字符串形式的线程名
    *   `Thread.CurrentThread():`返回当前正在执行的线程对象

### 1.6线程方法

| 方法                           | 说明                                     |
| :--------------------------- | :------------------------------------- |
| setPriority(int newPriority) | 更改线程优先级                                |
| getPriority()                | 获取线程优先级                                |
| boolean isAlive()            | 测试线程是否还存活                              |
| void yield()                 | 使当前线程让步，重新回到争夺CPU执行权的队列中(让步不一定成功，看cpu) |
| static void sleep(long ms)   | 使当前正在执行的线程停留指定的毫秒数                     |
| void join()                  | 等死（等待当前线程销毁后，再继续执行其它的线程）               |
| void interrupt()             | 终止线程睡眠                                 |
| setDaemon（Boolean xx）        | 设置守护线程                                 |

线程停止最好让自己停止

每个对象都有一个锁，sleep不会释放锁

线程一旦死亡就不能再启动，只能启动一次

优先级范围为0\~10，优先级低意味着被调度的概率低，并不一定会后执行

## 2.线程同步

### 2.1线程同步方式

synchronized 修饰方法时，它的锁住的对象取决于方法是静态的还是非静态的。

如果方法是静态的，那么它锁住的对象就是这个类的 Class 对象，也就是说，所有访问这个类的静态 synchronized 方法的线程都需要竞争同一把锁。

如果方法是非静态的，那么它锁住的对象就是调用这个方法的对象实例，也就是说，只有访问同一个对象实例的非静态 synchronized 方法的线程才需要竞争同一把锁。

所以，对于你的问题，如果 b 是其他类的一个对象实例，那么 synchronized 修饰方法时，并不会锁定 b，而是锁定 this 或者 MyClass.class。如果你想锁定 b，你可以在方法中使用 synchronized 代码块，并传入 b 作为参数。

**synchronized修饰方法时锁的是调用方法的对象，而无法锁类里的其他实例对象如 b**

```java
public class MyClass {
    private int a;
    private OtherClass b;

    public synchronized void method1() {
        // 这个方法锁定的是 this 对象
        // TODO
    }

    public void method2() {
        synchronized (b) {
            // 这个方法锁定的是 b 对象
            // TODO
        }
    }
}

```

### 2.2如何解决线程安全问题

 是一上来就选择线程同步吗？synchronized\
        不是，synchronized会让程序的执行效率降低，用户体验不好。系统的用户吞吐量降低。用户体验差。在不得已的情况下再选择线程同步机制。

*   第一种方案：尽量使用局部变量代替“实例变量和静态变量”。  &#x20;

*   第二种方案：如果必须是实例变量，那么可以考虑创建多个对象，这样 实例变量的内存就不共享了。（一个线程对应1个对象，100个线程对应100个对象， 对象不共享，就没有数据安全问题了。）&#x20;

*   第三种方案：如果不能使用局部变量，对象也不能创建多个，这个时候
    就只能选择synchronized了。线程同步机制。

### 2.3Lock

应用场景不同，不一定要在同一个方法中进行解锁，如果在当前的方法体内部没有满足解锁需求时，可以将lock引用传递到下一个方法中，当满足解锁需求时进行解锁操作，方法比较灵活。

```java
   private Lock lock = new ReentrantLock();//定义Lock类型的锁
   public  void withdraw(double money){
        // t1和t2并发这个方法。。。。（t1和t2是两个栈。两个栈操作堆中同一个对象。）
        // 取款之前的余额
        lock.lock();//上锁
        double before = this.getBalance(); // 10000
        // 取款之后的余额
        double after = before - money;
        // 在这里模拟一下网络延迟，100%会出现问题
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
 
        // 更新余额
        // 思考：t1执行到这里了，但还没有来得及执行这行代码，t2线程进来withdraw方法了。此时一定出问题。
        this.setBalance(after);
        lock.unlock();//解锁
    }
```

lock和synchronized都是用来实现线程同步的机制，但是它们有一些**区别**。

*   来源：lock是一个接口，而synchronized是java的一个关键字，synchronized是内置的语言实现；
*   异常是否释放锁：synchronized在发生异常时候会自动释放占有的锁，因此不会出现死锁；而lock发生异常时候，不会主动释放占有的锁，必须手动unlock来释放锁，可能引起死锁的发生。
*   是否响应中断：lock等待锁过程中可以用interrupt来中断等待，而synchronized只能等待锁的释放，不能响应中断；
*   是否知道获取锁：Lock可以通过trylock来知道有没有获取锁，而synchronized不能；
*   锁的类型：synchronized只支持可重入的非公平的独占锁，而Lock可以支持多种类型的锁，如可重入锁、公平锁、读写锁等；
*   锁的状态：synchronized无法判断是否持有锁或者锁是否空闲，而Lock可以通过getState()方法来获取锁状态；
*   性能：在资源竞争不是很激烈的情况下，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况选择。
*   调度：synchronized使用Object对象本身的wait 、notify、notifyAll调度机制，而Lock可以使用Condition进行线程之间的调度。

## 3.生产者消费者问题

### 3.1Object类中的wait和notify方法

*   &#x20;第一：wait和notify方法不是线程对象的方法，是java中任何一个java对象都有的方法，因为这两个方式是Object类中自带的。wait方法和notify方法不是通过线程对象调用，不是这样的：t.wait()，也不是这样的：t.notify()..不对。

*   第二：wait()方法作用:
    `Object o = new Object();
    o.wait();`

    表示：        &#x20;

    &#x20;       让正在o对象上活动的线程进入等待状态，无期限等待，直到被唤醒为止.o.wait();方法的调用，会让“当前线程（正在o对象上活动的线程）”进入等待状态。

*   第三：notify()方法作用：
    `Object o = new Object();
    o.notify();`

    &#x20;     表示：         &#x20;

    &#x20;            唤醒正在o对象上等待的线程。还有一个notifyAll()方法：这个方法是唤醒o对象上处于等待的所有线程。

**注意**：wait方法和notify方法需要建立在synchronized线程同步的基础之上。

**重点**：o.wait()方法会让正在o对象上活动的当前线程进入等待状态，并且释放之前占有的o对象的锁；

&#x20;        o.notify()方法只会通知，不会释放之前占有的o对象的锁。

### 3.2生产者消费者模式

生产者与消费者模式是并发、多线程编程中经典的设计模式，通过wait和notifyAll方法实现。

例如：生产满了，就不能继续生产了，必须让消费线程进行消费。

&#x20;         消费完了，就不能继续消费了，必须让生产线程进行生产。

而消费和生产者共享的仓库，就为多线程共享的了，所以需要考虑仓库的线程安全问题。

wait方法和notify方法建立在线程同步的基础之上。因为多线程要同时操作一个仓库。有线程安全问题。

wait方法作用：o.wait()让正在o对象上活动的线程t进入等待状态，并且释放掉t线程之前占有的o对象的锁。

notify方法作用：o.notify()让正在o对象上等待的线程唤醒，只是通知，不会释放o对象上之前占有的锁。

**信号灯法：利用标识位解决**

```java
public class wait_notify {
    public static void main(String[] args) {
        Box box = new Box();//实例化奶箱类
 
        Producer producer = new Producer(box);//生产者对象
        Customer customer = new Customer(box);//消费者对象
 
        Thread tp = new Thread(producer);//创建生产者线程
        Thread tc = new Thread(customer);//创建消费者线程
 
        //启动线程
        tp.start();
        tc.start();
    }
}
//奶箱类
class Box{
    private int milk;  //放入奶箱中的第几瓶牛奶
    private boolean state = false; //默认奶箱为空
 
    /**
     * 生产者生产（放）牛奶
     * @param milk  第几瓶
     */
    public synchronized void put(int milk){
        if(state){  //true表示奶箱中有牛奶
            try {
                wait();  //等待，需要有人唤醒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //没有牛奶，需要生产牛奶
        this.milk = milk;
        System.out.println("王五将第" + this.milk + "瓶你牛奶放进了奶箱中");
        this.state = true;//将奶箱状态调整成有牛奶
        notifyAll();//唤醒全部正在等待的线程
    }
    /**
     * 消费者取牛奶
     */
    public synchronized void get(){
        if(!state){  //true表示奶箱中有牛奶
            try {
                wait();  //等待，需要有人唤醒
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        //有牛奶，需要取牛奶
        System.out.println("张三将第" + this.milk + "瓶牛奶拿走补了身体！");
        this.state = false;//将奶箱状态改变成空
        notifyAll();//唤醒全部正在等待的线程
    }
 
}
//生产者类
class Producer implements Runnable{
    private Box b;
 
    public Producer(Box b){
        this.b = b;
    }
 
    @Override
    public void run() {
        for (int i = 1; i < 8; i++) {
            b.put(i);//放牛奶，放几瓶
        }
    }
}
//消费者类
class Customer implements Runnable{
    private Box b;
 
    public Customer(Box b){
        this.b = b;
    }
 
    @Override
    public void run() {
        while (true){
            b.get();//消费者取牛奶
        }
    }
}
```

**管程法：利用缓冲区解决**

```java

public class ThreadTest16 {
    public static void main(String[] args) {
        // 创建1个仓库对象，共享的。
        List list = new ArrayList();
        // 创建两个线程对象
        // 生产者线程
        Thread t1 = new Thread(new Producer(list));
        // 消费者线程
        Thread t2 = new Thread(new Consumer(list));
 
        t1.setName("生产者线程");
        t2.setName("消费者线程");
 
        t1.start();
        t2.start();
    }
}
 
// 生产线程
class Producer implements Runnable {
    // 仓库
    private List list;
 
    public Producer(List list) {
        this.list = list;
    }
    @Override
    public void run() {
        // 一直生产（使用死循环来模拟一直生产）
        while(true){
            // 给仓库对象list加锁。
            synchronized (list){
                if(list.size() > 0){ // 大于0，说明仓库中已经有1个元素了。
                    try {
                        // 当前线程进入等待状态，并且释放Producer之前占有的list集合的锁。
                        list.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                // 程序能够执行到这里说明仓库是空的，可以生产
                Object obj = new Object();
                list.add(obj);
                System.out.println(Thread.currentThread().getName() + "--->" + obj);
                // 唤醒消费者进行消费
                list.notifyAll();
            }
        }
    }
}
 
// 消费线程
class Consumer implements Runnable {
    // 仓库
    private List list;
 
    public Consumer(List list) {
        this.list = list;
    }
 
    @Override
    public void run() {
        // 一直消费
        while(true){
            synchronized (list) {
                if(list.size() == 0){
                    try {
                        // 仓库已经空了。
                        // 消费者线程等待，释放掉list集合的锁
                        list.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                // 程序能够执行到此处说明仓库中有数据，进行消费。
                Object obj = list.remove(0);
                System.out.println(Thread.currentThread().getName() + "--->" + obj);
                // 唤醒生产者生产。
                list.notifyAll();
            }
        }
    }
}
```

## 4.线程池

### 4.1线程池概念

线程池就是首先创建一些线程，他们的集合称之为线程池。线程池在系统启动时会创建大量空闲线程，程序将一个任务传递给线程池，线程池就会启动一条线程来执行这个任务，执行结束后线程不会销毁（死亡），而是再次返回到线程池中成为空闲状态，等待执行下一个任务。

### 4.2使用线程池的原因

多线程运行时，系统不断创建和销毁新的线程，成本非常高，会过度的消耗系统资源，从而可能导致系统资源崩溃，使用线程池就是最好的选择。

### 4.3使用线程池

```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.ForkJoinPool;

public class Pool {
    public static void main(String[] args) {
        ExecutorService serviceser= Executors.newFixedThreadPool(10);//创建线程池
        serviceser.execute(new testpool());  //使用线程池（Runnable接口如此使用）
        serviceser.execute(new testpool());
        serviceser.execute(new testpool());
        serviceser.shutdown();
    }

}
class testpool implements Runnable{
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

