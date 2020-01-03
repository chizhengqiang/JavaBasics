# JVM
******************************************

### 1. JVM 架构图

### 2: 类加载器 
- 负责加载class文件。class文件在开头有特定的文件标示，将class文件字节码内容加载到内存中，并将这些内容转换成方法区的运行数据结构并且ClassLoder只负责class文件的加载，至于它是否可以运行则由Execution Engine决定
#### 2.1 有哪几种加载器
- 主加载器 boostrap classLoader C++ 
- 扩展类加载器 Extension classLoader Java
- 应用程序类加载器 AppClassLoader Java也叫系统类加载器，加载当前应用的classpath的所有类
- 用户自定义加载类 Java.lang.ClassLoader的子类，用户可以定制类的加载方式
- sum.misc.launcher Java虚拟机的入口应用
#### 2.2 双亲派别
- 双亲委派机制得工作过程：
1. 类加载器收到类加载的请求；
2. 把这个请求委托给父加载器去完成，一直向上委托，直到启动类加载器；
3. 启动器加载器检查能不能加载（使用findClass()方法），能就加载（结束）；否则，抛出异常，通知子加载器进行加载。
4. 重复步骤三；
#### 2.3 沙箱安全机制
- 防止Java源代码的污染，防止对本地系统造成破坏。

### 3: native
- `native`是关键字
-  有声明、无实现 
### 4: PC寄存
- 记录了方法之间的调用和执行情况

### 5: 方法区
- 供各线程共享运行时内存区域，它存储了每一个类的结构信息
- 方法区里的是规范，不同的虚拟机里头实现是不一样的，最典型的就是永久代（`PermGen space`） 和元空间(`Metaspace`）
- 实例变量存在堆内存中，和方法区无关

### 6: 栈 `stack`
- 栈管运行，堆管存储
- 栈保存： 8种基本类型 + 对象的引用变量+实例方法都是在函数的栈内存分配
- 栈存储3类

### 7. 堆




### 8. 基本用法
#### CountDownLatch  减法
- 案例
```js
public static void main(String[] args) throws InterruptedException {
	CountDownLatch countDownLatch = new CountDownLatch(6);
	for (int i = 0; i < 6; i++) {
		new Thread(() -> {
			System.out.println(Thread.currentThread().getName() + "\t" + "离开教室");
			countDownLatch.countDown();
		}, String.valueOf(i)).start();
	}
	countDownLatch.await();
	System.out.println(Thread.currentThread().getName() + "\t班长离开教室");
}
```
- 结果
``` js
0	离开教室
4	离开教室
3	离开教室
2	离开教室
1	离开教室
5	离开教室
main	班长离开教室
```
#### CyclicBarrier  加法 
- 实例
``` js
public static void main(String[] args) {
	CyclicBarrier cyclicBarrier = new CyclicBarrier(7, () -> {
		System.out.println("召唤神龙");
	});

	for (int i = 1; i <= 7; i++) {
		final int temp = i;
		new Thread(() -> {
			System.out.println(Thread.currentThread().getName() + "\t收集" + temp + "颗龙珠");
			try {
				cyclicBarrier.await();
			} catch (InterruptedException e) {
				e.printStackTrace();
			} catch (BrokenBarrierException e) {
				e.printStackTrace();
			}
		}, String.valueOf(i)).start();
	}
}
```
- 结果
``` js
1	收集1颗龙珠
4	收集4颗龙珠
2	收集2颗龙珠
3	收集3颗龙珠
6	收集6颗龙珠
5	收集5颗龙珠
7	收集7颗龙珠
召唤神龙
```
#### Semaphore  信号灯
1. 两个操作
    - acquire(获取) 要么成功获取信号量（信号量减一） ，要么一直等下去，直到线程释放信号量，或超时
    - release（释放）实际上将信号量加一，然后唤醒等待的线程
2. 目的
    + 多个共享资源互斥使用
    + 并发线程数的控制
- 案例
``` js
public static void main(String[] args) {
	Semaphore semaphore = new Semaphore(3); //模拟资源类，有3个空车位
	for (int i = 0; i < 6; i++) {
		new Thread(() -> {
			try {
				semaphore.acquire();
				System.out.println(Thread.currentThread().getName() + "\t抢占了车位");
				TimeUnit.SECONDS.sleep(3);
				System.out.println(Thread.currentThread().getName() + "\t离开了车位");
			} catch (InterruptedException e) {
				e.printStackTrace();
			} finally {
				semaphore.release();
			}
		}, String.valueOf(i)).start();
	}
}
```
- 结果
``` js
0	抢占了车位
2	抢占了车位
1	抢占了车位
1	离开了车位
2	离开了车位
0	离开了车位
3	抢占了车位
5	抢占了车位
4	抢占了车位
3	离开了车位
4	离开了车位
5	离开了车位
```
### 9.读写锁 ReadWriteLock
``` js
class MyCache {

    private volatile Map<String, Object> map = new HashMap<>();

    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock(); 

    public void put(String key, Object value) {
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t开始写入" + key);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            map.put(key, value);
            System.out.println(Thread.currentThread().getName() + "\t写入成功" + key);
        }finally {
            readWriteLock.writeLock().unlock();
        }
    }

    public void get(String key) {
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t开始读取" + key);
            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t读取成功" + key);
        }finally {
            readWriteLock.readLock().unlock();
        }

    }
}
```
``` js
 public static void main(String[] args) {
	MyCache myCache = new MyCache();
	for (int i = 0; i < 5; i++) {

		final int tmpI = i;
		new Thread(() -> {
			myCache.put(tmpI + "", tmpI + "");
		}, String.valueOf(i)).start();
	}
	for (int i = 0; i < 5; i++) {

		final int tmpI = i;
		new Thread(() -> {
			myCache.get(tmpI + "");
		}, String.valueOf(i)).start();
	}
}
```
- 结果
``` js
2	开始写入2
2	写入成功2
0	开始写入0
0	写入成功0
1	开始写入1
1	写入成功1
3	开始写入3
3	写入成功3
4	开始写入4
4	写入成功4
0	开始读取0
1	开始读取1
2	开始读取2
3	开始读取3
4	开始读取4
0	读取成功0
1	读取成功1
2	读取成功2
3	读取成功3
4	读取成功4
```
### 10. 阻塞队列
### 10.面试
####  wait sleep 功能都是当前线程暂停，有什么区别？
- wait 暂停，放开锁
- sleep 手里还有锁