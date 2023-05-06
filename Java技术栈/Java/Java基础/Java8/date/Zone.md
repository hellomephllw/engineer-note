[toc]

## 1 java.util时间库的问题

### 1.1 Date

Date对象有几个严重的问题：它不能转换时区，除了toGMTString()可以按GMT+0:00输出外，Date总是以当前计算机系统的默认时区为基础进行输出。此外，我们也很难对日期和时间进行加减，计算两个日期相差多少天，计算某个月第一个星期一的日期等。

### 1.2 Calendar

无法转换时区。

### 1.3 SimpleDateFormat

线程不安全，千万不要使用static。

### 1.4 TimeZone

不能使用UTC，可以使用GMT。

## 2 ZoneOffset

### 2.1 of

```java
ZoneOffset zoneOffset1 = ZoneOffset.of("+8");
ZoneOffset zoneOffset2 = ZoneOffset.of("+08");
ZoneOffset zoneOffset3 = ZoneOffset.of("-07:00");
ZoneOffset zoneOffset4 = ZoneOffset.of("-07:00:00");
ZoneOffset zoneOffset5 = ZoneOffset.of("-070000");
System.out.println(zoneOffset1);
System.out.println(zoneOffset2);
System.out.println(zoneOffset3);
System.out.println(zoneOffset4);
System.out.println(zoneOffset5);
```

```
+08:00
+08:00
-07:00
-07:00
-07:00
```

### 2.2 ofTime

```java
ZoneOffset zoneOffset1 = ZoneOffset.ofHours(8);
ZoneOffset zoneOffset2 = ZoneOffset.ofHoursMinutes(8, 0);
ZoneOffset zoneOffset3 = ZoneOffset.ofHoursMinutesSeconds(8, 0, 0);
System.out.println(zoneOffset1);
System.out.println(zoneOffset2);
System.out.println(zoneOffset3);
```

```
+08:00
+08:00
+08:00
```

### 2.3 ofSeconds

```java
// 十分秒全部用秒换算（8 * 60 * 60 = 28800）
ZoneOffset zoneOffset = ZoneOffset.ofTotalSeconds(28800);
System.out.println(zoneOffset);
System.out.println(zoneOffset.getTotalSeconds());
System.out.println(zoneOffset.getId());
```

```
+08:00
28800
+08:00
```

## 3 ZoneId

### 3.1 id
```java
ZoneId zoneId1 = ZoneId.of(ZoneId.SHORT_IDS.get("CTT"));
ZoneId zoneId2 = ZoneId.of("Asia/Shanghai");
ZoneId zoneId3 = ZoneId.of("UTC+8");
ZoneId zoneId4 = ZoneId.of("UTC+08");
ZoneId zoneId5 = ZoneId.of("+8");
ZoneId zoneId6 = ZoneId.of("+08:00");
ZoneId zoneId7 = ZoneId.of("+08:00:00");
ZoneId zoneId8 = ZoneId.of(ZoneId.SHORT_IDS.get("PST"));// 洛杉矶标准时间，自动切换标准时间和夏令时
ZoneId zoneId9 = ZoneId.of("America/Los_Angeles");
ZoneId zoneId10 = ZoneId.of("UTC-7");// 使用UTC不会自动切换标准时间和夏令时
System.out.println(zoneId1);
System.out.println(zoneId2);
System.out.println(zoneId3);
System.out.println(zoneId4);
System.out.println(zoneId5);
System.out.println(zoneId6);
System.out.println(zoneId7);
System.out.println(zoneId8);
System.out.println(zoneId9);
System.out.println(zoneId10);
```

```
Asia/Shanghai
Asia/Shanghai
UTC+08:00
UTC+08:00
+08:00
+08:00
+08:00
America/Los_Angeles
America/Los_Angeles
UTC-07:00
```

### 3.2 夏令制

```java
// 查看时区是否有夏令
ZoneId zoneId = ZoneId.of("America/Los_Angeles");
boolean isDaylight = zoneId.getRules().isDaylightSavings(Instant.now());
System.out.println(isDaylight);

zoneId = ZoneId.of("UTC-7");
isDaylight = zoneId.getRules().isDaylightSavings(Instant.now());
System.out.println(isDaylight);

zoneId = ZoneId.of("Asia/Shanghai");
isDaylight = zoneId.getRules().isDaylightSavings(Instant.now());
System.out.println(isDaylight);
```

```
true
false
false
```

## 4 ZonedDateTime

### 4.1 now

```java
ZonedDateTime cttZoneDateTime = ZonedDateTime.now();
ZonedDateTime pstZoneDateTime = ZonedDateTime.now(ZoneId.of(ZoneId.SHORT_IDS.get("PST")));
System.out.println(cttZoneDateTime);
System.out.println(pstZoneDateTime);
```

```
2023-05-06T17:22:07.959+08:00[Asia/Shanghai]
2023-05-06T02:22:07.964-07:00[America/Los_Angeles]
```

### 4.2 LocalDateTime转换

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
ZoneId zoneId = ZoneId.of(ZoneId.SHORT_IDS.get("PST"));
System.out.println(dateTime);
ZonedDateTime zonedDateTime = ZonedDateTime.of(dateTime, zoneId);
System.out.println(zonedDateTime);
zonedDateTime = dateTime.atZone(zoneId);
System.out.println(zonedDateTime);
// 去掉时区
dateTime = zonedDateTime.toLocalDateTime();
System.out.println(dateTime);
```

```
2023-04-01T10:59:59.800
2023-04-01T10:59:59.800-07:00[America/Los_Angeles]
2023-04-01T10:59:59.800-07:00[America/Los_Angeles]
2023-04-01T10:59:59.800
```

### 4.3 切换时区

```java
LocalDateTime dateTime = LocalDateTime.of(2023, 4, 1, 10, 59, 59, 800 * 1000 * 1000);
System.out.println(dateTime);
ZonedDateTime zonedDateTime = ZonedDateTime.of(dateTime, ZoneId.of(ZoneId.SHORT_IDS.get("PST")));
System.out.println(zonedDateTime);
// 维持instant改变展示zone（timestamp不变）
zonedDateTime = zonedDateTime.withZoneSameInstant(ZoneId.of(ZoneId.SHORT_IDS.get("CTT")));
System.out.println(zonedDateTime);
// 维持local改变zone（timestamp改变）
zonedDateTime = zonedDateTime.withZoneSameLocal(ZoneId.of(ZoneId.SHORT_IDS.get("PST")));
System.out.println(zonedDateTime);
```

```
2023-04-01T10:59:59.800
2023-04-01T10:59:59.800-07:00[America/Los_Angeles]
2023-04-02T01:59:59.800+08:00[Asia/Shanghai]
2023-04-02T01:59:59.800-07:00[America/Los_Angeles]
```

```java
Instant instant = Instant.now();
System.out.println(instant);
// default UTC to PST
ZonedDateTime zonedDateTime = ZonedDateTime.ofInstant(instant, ZoneId.of(ZoneId.SHORT_IDS.get("PST")));
System.out.println(zonedDateTime);
// PST to CTT
zonedDateTime = zonedDateTime.withZoneSameInstant(ZoneId.of(ZoneId.SHORT_IDS.get("CTT")));
System.out.println(zonedDateTime);
```

```
2023-05-06T09:54:06.288Z
2023-05-06T02:54:06.288-07:00[America/Los_Angeles]
2023-05-06T17:54:06.288+08:00[Asia/Shanghai]
```

### 4.4 获取zoneId

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now(ZoneId.of(ZoneId.SHORT_IDS.get("PST")));
ZoneId zoneId = zonedDateTime.getZone();
System.out.println(zoneId);
```

```
America/Los_Angeles
```

### 4.5 格式化

```java
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
ZonedDateTime zonedDateTime = ZonedDateTime.now();
System.out.println(zonedDateTime);
System.out.println(zonedDateTime.format(formatter));
```

```
2023-05-06T18:18:53.304+08:00[Asia/Shanghai]
2023-05-06 18:18:53
```

### 4.6 计算

```java
ZonedDateTime zonedDateTime = ZonedDateTime.now();
System.out.println(zonedDateTime);
zonedDateTime = zonedDateTime.minusDays(1);
System.out.println(zonedDateTime);
zonedDateTime = zonedDateTime.plusYears(1);
System.out.println(zonedDateTime);
```

```
2023-05-06T18:36:28.703+08:00[Asia/Shanghai]
2023-05-05T18:36:28.703+08:00[Asia/Shanghai]
2024-05-05T18:36:28.703+08:00[Asia/Shanghai]
```

### 

## 5 OffsetDateTime

### 5.1 now

```java
OffsetDateTime cttOffsetDateTime = OffsetDateTime.now();
OffsetDateTime pstOffsetDateTime = OffsetDateTime.now(ZoneId.of(ZoneId.SHORT_IDS.get("PST")));
System.out.println(cttOffsetDateTime);
System.out.println(pstOffsetDateTime);
```

```
2023-05-06T18:41:47.535+08:00
2023-05-06T03:41:47.537-07:00
```

### 5.2 其他·

> _略，与ZoneDateTime相同_