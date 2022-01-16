## 1 基础语法

### 1.1 参数

#### 1.1.1 显式

```java
public class Param {

    public static void main(String[] args) {
        Calculator calculator = (Integer param) -> {
            return param + 1;
        };
        System.out.println(calculator.execute(1));
    }

    interface Calculator {
        int execute(Integer param);
    }

}
```

#### 1.1.2 隐式

```java
Calculator calculator = (param) -> {
    return param + 1;
};
```

#### 1.1.3 单参数

```java
Calculator calculator = param -> {
    return param + 1;
};
```

#### 1.1.4 无参数

```java
Calculator calculator = () -> {
    return param + 1;
};
```

### 1.2 表达式

#### 1.2.1 完整表达式

```java
(int x, int y) -> {
	return x + y; 
};
```

#### 1.2.2 简写

```java
(x, y) -> x + y;
```

#### 1.2.3 无返回结果

```java
(x, y) -> {
	System.out.println(x + y);
};
```

#### 1.2.4 无返回结果简写

```java
(x, y) -> System.out.println(x + y);
```

## 2 @FunctionalInterface

```java
// 不写此注解也可以
@FunctionalInterface
public interface functional {

	// 只能有一个抽象方法
	int execute(Integer x, Integer y);

	// 可以写默认方法
	default void doSomeThing() {

	}

	// 可以写静态方法
	static int doAnotherThing() {
		return 1;
	}

	// 可以包含Object的public方法
	// 不会被当作抽象方法，因为
	boolean equals(Object obj);

}
```

## 3 行为参数化

### 3.1 参数决定最终计算行为
```java
public class ParamBehavior {

    public static void main(String[] args) {
        magic((x, y) -> x + y);
        magic((x, y) -> x * y);
    }

    public static void magic(Calculator calculator) {
        int x = 10, y = 20;
        System.out.println(calculator.execute(x, y));
    }

    @FunctionalInterface
    interface Calculator {
        int execute(int x, int y);
    }

}
```

### 3.2 重载

```java
public class ParamBehaviorOverload {

    public static void main(String[] args) {
        // 可以指定类型
        magic((int x, int y) -> x + y);
        magic((long x, long y) -> x * y);
        // 可以强转
        magic((IntegerCalculator) ((x, y) -> x / y));
    }

    public static void magic(IntegerCalculator calculator) {
        int x = 10, y = 20;
        System.out.println(calculator.execute(x, y));
    }

    public static void magic(LongCalculator calculator) {
        long x = 10, y = 20;
        System.out.println(calculator.execute(x, y));
    }

    @FunctionalInterface
    interface IntegerCalculator {
        int execute(int x, int y);
    }

    @FunctionalInterface
    interface LongCalculator {
        long execute(long x, long y);
    }
    
}
```

## 4 函数式接口

### 4.1 Consumer

#### 4.1.1 代码

```java
Consumer<String> consumer1 = str -> System.out.print(str.split(",")[0]);
Consumer<String> consumer2 = str -> System.out.println("->" + str.split(",")[1]);
for (String str : Arrays.asList("保时捷,白色", "法拉利,红色")) {
    consumer1.andThen(consumer2).accept(str);
}
```

#### 4.1.2 结果

```
保时捷->白色
法拉利->红色
```

#### 4.1.3 说明

通过accept传入结果，先执行consumer1，执行完毕之后，再把最早传入的结果传递给consumer2执行。
可以实现统一结果，多个消费者链式消费。

### 4.2 Supplier

#### 4.2.1 代码

```java
Supplier<String> supplier = () -> "获取一个指定值";
System.out.println(supplier.get());
```

#### 4.2.2 结果

```
获取一个指定值
```


### 4.3 Function

#### 4.3.1 代码

```java
Function<Integer, Integer> func1 = x -> x + 1;
System.out.println(func1.apply(10));

Function<Integer, Integer> func2 = x -> x * x;
Function<Integer, Integer> func3 = x -> x * 2;
System.out.println(func2.compose(func3).apply(1));

Function<Integer, Integer> func4 = x -> x * x;
Function<Integer, Integer> func5 = x -> x * 2;
System.out.println(func4.andThen(func5).apply(3));

Function<Integer, Integer> func6 = Function.identity();
System.out.println(func6.apply(10));
```

#### 4.3.2 结果

```
11
4
18
10
```

#### 4.3.3 说明

##### 1) apply


通过apply传入计算值，执行lambda表达式。


##### 2) compose


通过apply传入初始计算值，先执行func3，再把结果传递给func2作为参数执行得到最后结果(从后到前)。


##### 3) andThen

通过apply传入初始计算值，先执行func4，再把结果传递给func5作为参数执行得到最后结果(从前到后)。


##### 4) identity

默认定义好的lambda表达式，该表达式直接返回结果。

### 4.4 Predicate

#### 4.4.1 代码

```java
Predicate<String> predicate1 = str -> str.length() > 0;
Predicate<String> predicate2 = Objects::nonNull;
Predicate<String> predicate3 = str -> str.equals("abc");
System.out.println(predicate1.and(predicate2).or(predicate3).test("abc"));

System.out.println(predicate1.and(predicate2).and(predicate3).negate().test("abc"));

System.out.println(Predicate.isEqual("abc").test("abc"));
```

#### 4.4.2 结果

```
true
false
true
```

#### 4.4.3 说明

##### 1) and和or

and相当于&&，or相当于||。

##### 2) negate

取反。

##### 3) isEqual

静态方法，是否相等。


## 5 引用

### 5.1 静态方法

#### 5.1.1 示例

##### 1) 代码

```java
// from
BiFunction<Integer, Integer, Integer> func1 = (x, y) -> Integer.sum(x, y);
System.out.println(func1.apply(2, 3));
// to
BiFunction<Integer, Integer, Integer> func2 = Integer::sum;
System.out.println(func2.apply(2, 3));
```

##### 2) 结果

```
5
5
```

#### 5.1.2 多态示例

##### 1) 代码

```java
Function<Integer, Integer> func3 = Integer::valueOf;
BiFunction<String, Integer, Integer> func4 = Integer::valueOf;
System.out.println(func3.apply(7));
System.out.println(func4.apply("101010101010", 2));
```

##### 2) 结果

```
7
2730
```

### 5.2 实例方法

#### 1)

```java
// from
Supplier<Integer> supplier = () -> "test".length();
System.out.println(supplier.get());
// to
Supplier<Integer> supplier1 = "test"::length;
System.out.println(supplier1.get());
```

```
4
4
```

#### 2)

```java
Util util = new Util();
// from
Consumer<String> consumer = str -> util.print(str);
consumer.accept("print sth");
// to
Consumer<String> consumer1 = util::print;
consumer1.accept("print sth too");
```

```
print sth
print sth too
```

#### 3)

```java
// from
Function<String, Integer> strLengthFunc = String::length;
String name = "this length";
int len = strLengthFunc.apply(name);
System.out.println("name  = " + name + ", length = " + len);
// to
name = "the other length";
len = strLengthFunc.apply(name);
System.out.println("name  = " + name + ", length = " + len);
```

```
name  = this length, length = 11
name  = the other length, length = 16
```

### 5.3 父类实例方法

```java
public static void main() {
    Reference ref = new Reference();
    ref.doSth();
}
private void doSth() {
    Function<String, String> strSay = this::saySth;
    System.out.println(strSay.apply("hi"));

    strSay = Reference.super::saySth;
    System.out.println(strSay.apply("hello"));
}
```

```
hi
hello
```

### 5.4 构造函数

#### 1)

```java
// from
Function<String, String> func1 = str -> new String(str);
System.out.println(func1.apply("test str"));
// to
Function<String,String> func2  = String::new;
System.out.println(func2.apply("test str too"));
```

```
test str
test str too
```

#### 2)

```
Supplier<String> func3 = String::new;
System.out.println("empty test string:" + func3.get());
```

```
empty test string:
```

## 6 作用域



## 7 递归

