[toc]

## 1 of

### 1.1 of

```java
Optional<String> opt = Optional.of("is string");// 不能放null值
System.out.println(opt);
```

```
Optional[is string]
```

### 1.2 ofNullable

```java
Optional<String> opt = Optional.ofNullable("is string");
System.out.println(opt);
opt = Optional.ofNullable(null);
System.out.println(opt);
```

```
Optional[is string]
Optional.empty
```

## 2 isPresent

### 2.1 isPresent

```java
Optional<String> opt = Optional.of("is string");
System.out.println(opt.isPresent());
System.out.println(Optional.empty().isPresent());
```

```
true
false
```

### 2.2 ifPresent

```java
Optional<String> opt = Optional.of("is string");
opt.ifPresent(str -> System.out.println(str));// 存在值, 会打印值
Optional.empty().ifPresent(str -> System.out.println(str));// 不存在值, 不会打印
```

```
is string
```

## 3 equals

```java
Optional<String> opt = Optional.of("is string");
System.out.println(opt.equals(Optional.of("is string")));
System.out.println(opt.equals(Optional.of("is num")));
```

```
true
false
```

## 4 get

### 4.1 get

```java
Optional<String> opt = Optional.of("is string");
System.out.println(opt.get());
```

```
is string
```

### 4.2 orElse

```java
Optional<String> opt = Optional.of("is string");
System.out.println(opt.orElse("other string"));
System.out.println(Optional.ofNullable(null).orElse("other string"));
```

```
is string
other string
```

### 4.3 orElseGet

```java
Optional<String> opt = Optional.of("is string");
System.out.println(opt.orElseGet(() -> "other string"));
System.out.println(Optional.ofNullable(null).orElseGet(() -> "other string"));
```

```
is string
other string
```

### 4.4 orElseThrow

```java
Optional<String> opt = Optional.of("is string");
System.out.println(opt.orElseThrow(() -> new RuntimeException("不存在值")));// 没有值会抛出异常
```

```
is string
```

## 5 map

### 5.1 map

```java
System.out.println(
    Optional.of(new Country())
        .map(country -> country.provinces.get(0))
        .map(province -> province.cities.get(0))
        .map(City::getName)
        .map(String::toUpperCase));
```

```
Optional[CDC]
```

### 5.2 flatMap

```java
System.out.println(
    Optional.of(new Country())
        .flatMap(country -> Optional.of(country.provinces.get(0)))
        .flatMap(province -> Optional.of(province.cities.get(0)))
        .flatMap(city -> Optional.of(city.getName()))
        .flatMap(name -> Optional.of(name.toUpperCase())));
```

```
Optional[CDC]
```

## 6 filter

```java
Optional<String> opt = Optional.of("this is a string");
System.out.println(opt.filter(str -> str.length() > 3).get());
System.out.println(opt.filter(str -> str.startsWith("that")).orElse("no match"));
```

```
this is a string
no match
```
