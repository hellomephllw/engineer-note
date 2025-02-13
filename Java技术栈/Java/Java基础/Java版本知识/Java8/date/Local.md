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

### 1.3 加减

#### 1.3.1 加

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime.plusYears(1));
System.out.println(dateTime.plusMonths(1));
System.out.println(dateTime.plusDays(1));
System.out.println(dateTime.plusHours(1));
System.out.println(dateTime.plusMinutes(1));
System.out.println(dateTime.plusSeconds(1));
System.out.println(dateTime.plusNanos(1));
```

```
2024-04-01T10:59:59.800
2023-05-01T10:59:59.800
2023-04-02T10:59:59.800
2023-04-01T11:59:59.800
2023-04-01T11:00:59.800
2023-04-01T11:00:00.800
2023-04-01T10:59:59.800000001
```

#### 1.3.2 减

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime.minusYears(1));
System.out.println(dateTime.minusMonths(1));
System.out.println(dateTime.minusDays(1));
System.out.println(dateTime.minusHours(1));
System.out.println(dateTime.minusMinutes(1));
System.out.println(dateTime.minusSeconds(1));
System.out.println(dateTime.minusNanos(1));
```

```
2022-04-01T10:59:59.800
2023-03-01T10:59:59.800
2023-03-31T10:59:59.800
2023-04-01T09:59:59.800
2023-04-01T10:58:59.800
2023-04-01T10:59:58.800
2023-04-01T10:59:59.799999999
```

### 1.4 获取

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

### 1.5 比较

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

### 1.6 转换

#### 1.6.1 Local转换
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

#### 1.6.2 Instant转换

```java
LocalDateTime dateTime = LocalDateTime.ofInstant(Instant.now(), ZoneId.of("UTC+8"));
System.out.println(dateTime);
```

```
2023-05-07T18:46:37.049
```

### 1.7 闰年

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