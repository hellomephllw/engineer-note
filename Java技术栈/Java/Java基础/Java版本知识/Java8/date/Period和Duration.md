[toc]

## 1 ChronoUnit

```java
Instant instant = Instant.now();
System.out.println(instant);
System.out.println(instant.plus(1, ChronoUnit.SECONDS));
System.out.println(instant.plus(1, ChronoUnit.MILLIS));
System.out.println(instant.plus(1, ChronoUnit.NANOS));
```

```
2023-04-29T02:28:39.550Z
2023-04-29T02:28:40.550Z
2023-04-29T02:28:39.551Z
2023-04-29T02:28:39.550000001Z
```

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime);
System.out.println(dateTime.plus(1, ChronoUnit.YEARS));
System.out.println(dateTime.plus(1, ChronoUnit.MONTHS));
System.out.println(dateTime.plus(1, ChronoUnit.DAYS));
```

```
2023-04-01T10:59:59.800
2024-04-01T10:59:59.800
2023-05-01T10:59:59.800
2023-04-02T10:59:59.800
```

## 2 Duration

### 2.1 间隔时间

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
LocalDateTime dateTimeOld = LocalDateTime.of(1990, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
Duration duration = Duration.between(dateTimeOld, dateTime);
System.out.println(duration);
System.out.println(duration.isNegative());// 开始时间大于结束时间
System.out.println(duration.toDays());
System.out.println(duration.toHours());
System.out.println(duration.toMinutes());
```

```
PT289272H
false
12053
289272
17356320
```

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
LocalDateTime dateTimeOld = LocalDateTime.of(1990, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
Instant instant = Instant.ofEpochMilli(dateTime.toInstant(ZoneOffset.of("+0")).toEpochMilli());
Instant instantOld = Instant.ofEpochMilli(dateTimeOld.toInstant(ZoneOffset.of("+0")).toEpochMilli());
Duration duration = Duration.between(instantOld, instant);
System.out.println(duration);
System.out.println(duration.isNegative());
System.out.println(duration.toDays());
System.out.println(duration.toHours());
System.out.println(duration.toMinutes());
```

```
PT289272H
false
12053
289272
17356320
```

### 2.2 计算

```java
Instant instant = Instant.now();
System.out.println(instant);
Duration duration = Duration.ofDays(1);
System.out.println(instant.plus(duration));
System.out.println(instant.plus(duration.plus(1, ChronoUnit.HOURS)));

LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime);
System.out.println(dateTime.plus(duration));
System.out.println(dateTime.plus(duration.plus(1, ChronoUnit.HOURS)));
```

```
2023-04-29T12:29:41.660Z
2023-04-30T12:29:41.660Z
2023-04-30T13:29:41.660Z
2023-04-01T10:59:59.800
2023-04-02T10:59:59.800
2023-04-02T11:59:59.800
```

## 3 Period

### 3.1 间隔时间

```java
LocalDate date = LocalDate.of(2023, 4, 1);
LocalDate dateOld = LocalDate.of(1990, 10, 1);
Period period = Period.between(dateOld, date);
System.out.println(period);
System.out.println(period.isNegative());
System.out.println(period.getYears());
System.out.println(period.getMonths());
System.out.println(period.getDays());
```

```
P32Y6M
false
32
6
0
```

### 3.2 计算

```java
LocalDate dateTime = LocalDate.of(2023, 4, 1);
Period period = Period.ofDays(1);
System.out.println(dateTime);
System.out.println(dateTime.plus(period));
System.out.println(dateTime.plus(period.plusYears(1)));
```

```
2023-04-01
2023-04-02
2024-04-02
```