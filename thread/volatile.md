# volatile
### 谈谈你对`volatile`的理解
1. `volatile` 是 Java虚拟机提供的轻量级的同步机制
- 保证可见行
- 不保证原子性
- 禁止指令重排

### JMM（Java内存模型）的理解

![](/Users/apple/Desktop/2.png)

1. `JMM` 谈谈
- 1.1 可见性
- 1.2 原子性
- 1.3 demo 演示可见性、原子性
```
class MyData {

    volatile int number = 0;

    /**
     * 1 验证可见性
     */
    public void addTo60() {
        this.number = 60;
    }

    /**
     * 验证原子性
     */
    public void addPlusPlus() {
        this.number++;
    }

    AtomicInteger atomicInteger = new AtomicInteger();

    /**
     * 保证原子性
     */
    public void addMyAtomic() {
        atomicInteger.getAndIncrement();
    }
}
/**
 * 1 验证volatile 可见性
 * 2 验证原子性
 * 2.1原子性值得是什么意思？
 * 不可分割，完整性，当某个线程正在做某个具体业务时，中间不可以被加载或者分割， 要么成功，要么失败
 * 2.2 代码演示
 */
public class VolatileDemo {

    public static void main(String[] args) {
        MyData myData = new MyData();

        for (int i = 0; i < 20; i++) {
            new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    myData.addPlusPlus();
                    myData.addMyAtomic();
                }
            }, String.valueOf(i)).start();
        }

        while (Thread.activeCount() > 2) {
            Thread.yield();
        }

        System.out.println(Thread.currentThread().getName() + "\t int type finally number value : " + myData.number);
        System.out.println(Thread.currentThread().getName() + "\t atomicInteger type finally number value : " + myData.atomicInteger);

    }

    /**
     * 验证可见性
     */
    public void seeOkByVolatile() {
        MyData myData = new MyData();

        new Thread(() -> {
            System.out.println(Thread.currentThread().getName() + "\t come in");
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            myData.addTo60();
            System.out.println("number update value : " + myData.number);
        }, "AAA").start();

        while (myData.number == 0) {

        }

        System.out.println(Thread.currentThread().getName() + "\t mission is over, main number value : " + myData.number);
    }
}
```
- 1.4 有序性
2. `JMM`关于同步的规定 
- 线程解锁前必须把共享变量刷新回主内存
- 线程加锁前必须读取主内存最新值到自己的工作内存
- 加锁解锁是同一把锁

### 在什么地方使用`volatile` 
#### 设计模式
```
/**
 * 设计模式 (DCL 多线程下的单列模式)
 */
public class SingletonDemo {

    //增加volatile 防止指令重排
    private static volatile SingletonDemo instance = null;

    private SingletonDemo() {
        System.out.println(Thread.currentThread().getName() + "\t 我是构造方法 SingletonDemo");
    }

    // DCL （Double Check Lock 双端检索机制）
    public static SingletonDemo getInstance() {
        if (instance == null) {
            synchronized (SingletonDemo.class) {
                if (instance == null) {
                    instance = new SingletonDemo();
                }
            }
        }
        return instance;
    }

    public static void main(String[] args) {
        for (int i = 0; i < 10; i++) {
            new Thread(() -> {
                SingletonDemo.getInstance();
            }, String.valueOf(i)).start();
        }
    }
}
```


# CAS