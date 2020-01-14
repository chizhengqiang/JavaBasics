# CAS

## 什么是CAS?

`compareAndSwap` 期望值和更新值、主内存的值和期望值比较，相同就修改成功，不相同就修改失败。

## CAS 底层原理？

1. 通过`unsafe` 类 `unsafe.getAndAddInt(this, valueOffset, 1)`
2. CAS 思想 

```
public final int getAndIncrement() {
	//this：当前对象， valueOffset：内存地址偏移量 
	return unsafe.getAndAddInt(this, valueOffset, 1);
}
```
```
public final int getAndAddInt(Object var1, long var2, int var4) {
	int var5;
	do {
		var5 = this.getIntVolatile(var1, var2);
	} while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));

	return var5;
}
```

## CAS 缺点

循环时间长开销大

只能保一个共享变量的原子操作

引出ABA问题

## ABA 问题

![]()



![](/Users/apple/Desktop/2.png)