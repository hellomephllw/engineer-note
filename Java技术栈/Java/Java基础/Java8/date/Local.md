[toc]

## 1 LocalDateTime

### 1.1 当前时间

```java
LocalDateTime dateTime = LocalDateTime.now();
System.out.println(dateTime);
```

```
2023-04-16T21:48:36.266
```

### 1.2 指定时间

#### 1.2.1 of

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime);
```

```
2023-04-01T10:59:59.800
```

#### 1.2.2 with

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
LocalDateTime newYear = dateTime.withYear(2022);
System.out.println(newYear);
LocalDateTime newMonth = dateTime.withMonth(10);
System.out.println(newMonth);
LocalDateTime newDay = dateTime.withDayOfMonth(26);
System.out.println(newDay);
LocalDateTime newHour = dateTime.withHour(20);
System.out.println(newHour);
LocalDateTime newMinute = dateTime.withMinute(8);
System.out.println(newMinute);
LocalDateTime newSecond = dateTime.withSecond(30);
System.out.println(newSecond);
LocalDateTime newNano = dateTime.withNano(8 * 1000 * 1000);
System.out.println(newNano);
```

```
2022-04-01T10:59:59.800
2023-10-01T10:59:59.800
2023-04-26T10:59:59.800
2023-04-01T20:59:59.800
2023-04-01T10:08:59.800
2023-04-01T10:59:30.800
2023-04-01T10:59:59.008
```

### 1.3 获取

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime.getYear());
System.out.println(dateTime.getMonthValue());
System.out.println(dateTime.getDayOfMonth());
System.out.println(dateTime.getHour());
System.out.println(dateTime.getMinute());
System.out.println(dateTime.getSecond());
System.out.println(dateTime.getNano());
```

```
2023
4
1
10
59
59
800000000
```

### 1.4 比较

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
LocalDateTime dateTime2 = LocalDateTime.of(2023, 5, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime.isAfter(dateTime2));
System.out.println(dateTime.isBefore(dateTime2));
System.out.println(dateTime.isEqual(dateTime2));
```

```
false
true
false
```

### 1.5 转换

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime);
LocalDate date = dateTime.toLocalDate();
System.out.println(date);
LocalTime time = dateTime.toLocalTime();
System.out.println(time);
```

```
2023-04-01T10:59:59.800
2023-04-01
10:59:59.800
```

### 1.6 闰年

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime);
LocalDate date = dateTime.toLocalDate();
System.out.println(date.isLeapYear());
```

```
2023-04-01T10:59:59.800
false
```

## 2 LocalTime
略

## 3 LocalTime

略