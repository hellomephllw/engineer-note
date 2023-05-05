[toc]

## 1 线程安全

SimpleDateFormat不是线程安全的，使用的时候，只能在方法内部创建新的局部变量；DateTimeFormatter不但是不变对象，它还是线程安全的。

## 2 时间格式化

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
System.out.println(formatter.format(dateTime));
System.out.println(dateTime.format(formatter));
DateTimeFormatter formatterCN = DateTimeFormatter.ofPattern("EEE, dd MMM yyyy HH:mm:ss", Locale.CHINA);
System.out.println(formatterCN.format(dateTime));
DateTimeFormatter formatterUS = DateTimeFormatter.ofPattern("EEE, dd MMM yyyy HH:mm:ss", Locale.US);
System.out.println(formatterUS.format(dateTime));
```

```
2023-04-01 10:59:59
2023-04-01 10:59:59
星期六, 01 四月 2023 10:59:59
Sat, 01 Apr 2023 10:59:59
```

## 3 时间解析

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
LocalDateTime dateTime = LocalDateTime.parse("2023-04-01 10:59:59", formatter);
System.out.println(dateTime);
```

```
2023-04-01T10:59:59
```