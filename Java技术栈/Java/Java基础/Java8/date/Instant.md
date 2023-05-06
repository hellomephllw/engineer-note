[toc]

## 1 时间戳转换

### 1.1 时间戳转Instant

```java
Instant instant = Instant.ofEpochMilli(1682726691131L);
System.out.println(instant);
instant = Instant.ofEpochSecond(1682726691);
System.out.println(instant);
```

```
2023-04-29T00:04:51.131Z
2023-04-29T00:04:51Z
```

### 1.2 Instant转时间戳

```java
Instant instant = Instant.now();
// 带毫秒时间戳
System.out.println(instant.toEpochMilli());
// 秒时间戳
System.out.println(instant.getEpochSecond());
// 纳秒
System.out.println(instant.getNano());
```

```
1682726691131
1682726691
131000000
```

## 2 加减

### 2.1 加

```java
Instant instant = Instant.now();
System.out.println(instant);
System.out.println(instant.plusSeconds(1));
System.out.println(instant.plusMillis(1));
System.out.println(instant.plusNanos(1));
```

```
2023-04-29T01:12:43.321Z
2023-04-29T01:12:44.321Z
2023-04-29T01:12:43.322Z
2023-04-29T01:12:43.321000001Z
```

### 2.2 减

```java
Instant instant = Instant.now();
System.out.println(instant);
System.out.println(instant.minusSeconds(1));
System.out.println(instant.minusMillis(1));
System.out.println(instant.minusNanos(1));
```

```
2023-04-29T01:27:52.221Z
2023-04-29T01:27:51.221Z
2023-04-29T01:27:52.220Z
2023-04-29T01:27:52.220999999Z
```

### 3 Date转换

```java
Date now = new Date();
System.out.println(now.toInstant());
System.out.println(Date.from(now.toInstant()));
```

```
2023-05-06T10:32:20.613Z
Sat May 06 18:32:20 CST 2023
```