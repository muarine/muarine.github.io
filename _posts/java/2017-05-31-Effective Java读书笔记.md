---
layout: post
title: Effective Java-读书笔记
categories: JVM
description: Effective Java-读书笔记
---

Effective Java 读书笔记

### 适用人群
JAVA开发人员，不局限于日常开发工作想做进一步技术提升，同样的功能需求代码实现方式却不相同，个人认为满足需求为先，其次在自己代码中追求最优实现，最常用法，考虑到团队协作，Effective中的编程方式为大众效仿

### 目的
一来自己记性不好，新的知识点必须经过脑子和手写才记得住，二来做一些笔记把常用的编码习惯记下来，以便于后续忘记了比较快速的找到然后粘贴拷贝，哈哈哈。。。

### 技术细节

#### 创建和销毁对象
1. 静态工厂方法代替构造器
```java
public static Singleton getInstance(){

}
```
> 优势:
> 1. 静态工厂有名称，能表达构造对象的含义
> 2. 返回原返回对象的任何子类对象(继承和多态)

#### 泛型

限定参数类型或者返回参数类型，方便一些错误在编译器就能被排查出


1. 有限制泛型


2. 无限制泛型
> ? extends E

```java
public static <E> Set<E> union(Set<? extends E> s1, Set<? extends E> s2) {
		Set<E> result = new HashSet<E>(s1);
		result.addAll(s2);
		return result;
	}

	// Simple program to exercise flexible generic method
	public static void main(String[] args) {
		Set<Integer> integers = new HashSet<Integer>();
		integers.add(1);
		integers.add(3);
		integers.add(5);

		Set<Double> doubles = new HashSet<Double>();
		doubles.add(2.0);
		doubles.add(4.0);
		doubles.add(6.0);

		// Won't compile; see page 137
		// Set<Number> numbers = union(integers, doubles);

		// Explicit type parameter is necessary here
		Set<Number> numbers = Union.<Number> union(integers, doubles);

		System.out.println(numbers);
	}
```

> ? super E

```java
public class RecursiveTypeBound {
	public static <T extends Comparable<? super T>> T max(List<? extends T> list) {
		Iterator<? extends T> i = list.iterator();
		T result = i.next();
		while (i.hasNext()) {
			T t = i.next();
			if (t.compareTo(result) > 0)
				result = t;
		}
		return result;
	}

	public static void main(String[] args) {
		List<String> argList = Arrays.asList(args);
		System.out.println(max(argList));
	}
}
```


#### 枚举

> 枚举实质上为int类型(可参考枚举ordinal函数)，编译器就初始化了(其中的耗时和占用内容在做Web开发可以忽略)，一般我平时的应用就是一类共同属性的常量或者数据表的状态字段

##### 实现接口
```java
public interface Operation {
	double apply(double x, double y);
}

public enum ExtendedOperation implements Operation {
	EXP("^") {
		public double apply(double x, double y) {
			return Math.pow(x, y);
		}
	},
	REMAINDER("%") {
		public double apply(double x, double y) {
			return x % y;
		}
	};

	private final String symbol;

	ExtendedOperation(String symbol) {
		this.symbol = symbol;
	}

	@Override
	public String toString() {
		return symbol;
	}
}
```

##### 常用的枚举功能
```java
public enum StatusEnum {

    WAIT_START(0, "待启动"),
    STARTED(1, "进行中"),
    PAUSED(2, "暂停"),
    END(3, "已结束"),
    AUDITING(5, "审核中"),
    WAIT_AUDIT(6, "待审核"),
    REJECT(7, "已驳回"),
    DELETE(8, "已删除");

    private final int     code;
    private final String  desc;

    StatusEnum(int code, String desc) {
        this.code = code;
        this.desc = desc;
    }

    // 普通方法
    public static String getDesc(int code) {
        for (StatusEnum c : StatusEnum.values()) {
            if (c.getCode() == code) {
                return c.desc;
            }
        }
        return null;
    }

    public int getCode() {
        return code;
    }

    public String getDesc() {
        return desc;
    }
}
```

#### 注解
> 基本用法

```java
// 作用范围
@Target({ElementType.METHOD, ElementType.TYPE})
// 注解保留：SOURCE CLASS RUNTIME
@Retention(RetentionPolicy.RUNTIME)
//文档注释，解释说明注解的含义
@Documented
public @interface RequestMapping {
}
```
> 结合反射常用操作

```java
public static void main(String[] args) throws Exception {
		int tests = 0;
		int passed = 0;
		Class testClass = Class.forName(args[0]);
		for (Method m : testClass.getDeclaredMethods()) {

      // 匹配单个异常类
			if (m.isAnnotationPresent(Test.class)) {
				tests++;
				try {
					m.invoke(null);
					passed++;
				} catch (InvocationTargetException wrappedExc) {
					Throwable exc = wrappedExc.getCause();
					System.out.println(m + " failed: " + exc);
				} catch (Exception exc) {
					System.out.println("INVALID @Test: " + m);
				}
			}

			// 匹配多个异常类
			if (m.isAnnotationPresent(ExceptionTest.class)) {
				tests++;
				try {
					m.invoke(null);
					System.out.printf("Test %s failed: no exception%n", m);
				} catch (Throwable wrappedExc) {
					Throwable exc = wrappedExc.getCause();
					Class<? extends Exception>[] excTypes = m.getAnnotation(
							ExceptionTest.class).value();
					int oldPassed = passed;
					for (Class<? extends Exception> excType : excTypes) {
						if (excType.isInstance(exc)) {
							passed++;
							break;
						}
					}
					if (passed == oldPassed)
						System.out.printf("Test %s failed: %s %n", m, exc);
				}
			}
		}
		System.out.printf("Passed: %d, Failed: %d%n", passed, tests - passed);
	}
```
