[toc]

## 1 基本概念

### 1.1 特点

* 元素是特定类型的对象，形成一个队列。Java中的Stream并不会向集合那样存储和管理元素，而是按需计算。也不会改变原数据，可以一直往后传递惰性操作。
* 数据源流的来源可以是集合Collection、数组Array、I/O Channel，产生器generator等。
* 聚合操作类似SQL语句一样的操作，比如filter，map，reduce，find，match，sorted等。

### 1.2 流类型

#### 1.2.1 串行

Stream就如同一个迭代器（Iterator），单向、不可往复，并且数据只能遍历一次，遍历过一次后即用尽了。就好比流水从面前流过，一去不复返。

#### 1.2.2 并行

Stream可以并行化操作。使用并行去遍历时，数据会被分成多个段，其中每一个都在不同的线程中处理，然后将结果一起输出。parallelStream底层是利用Fork/Join实现的。

### 1.3 操作类型

#### 1.3.1 中间操作（Intermediate operations）

中间操作其实就是进行逻辑处理。这个操作可以有一个或者多个连续操作，将一个流转换成另一个流，这些操作不会消耗流，其目的是建立一个流水线，直到终端操作发生后，才会做数据的最终执行。

##### 1) 无状态（Stateless）

指数据处理时，不受前面元素的影响。如filter、map、peek等。

##### 2) 有状态（Stateful）

指数据处理时，必须等到所有元素处理之后才知道最终结果。如distinct、sorted、limit、skip等。

#### 1.3.2 终端操作（Terminal operations）

一个 Stream 对象只能有一个终端操作（Terminal operations），这个操作一旦发生，就会真实处理数据，生成对应的处理结果。

##### 1) 非短路操作

指必须处理所有元素才能得到最终结果。如forEach、toArray、reduce、collect、max/min、count等。

##### 2) 短路操作
指遇到某些符合条件的元素就可以得到最终结果。 如match、find等。

## 2 数据转Stream

```java
// 多个数据直接转换为Stream
Stream<String> stream1 = Stream.of("a", "b", "c");

// 数组转换为Stream
String[] strArrays = new String[]{"a", "b"};
Stream<String> stream2 = Arrays.stream(strArrays);

// list转换为Stream
List<String> strList = new ArrayList<>();
strList.add("a");
strList.add("b");
Stream<String> stream3 = strList.stream();

// set转换为Stream
Set<String> strSet = new HashSet<>();
strSet.add("a");
strSet.add("b");
Stream<String> stream4 = strSet.stream();

// map转换为Stream
Map<String, Integer> map = new HashMap<>();
map.put("a", 100);
map.put("b", 200);
Stream<Map.Entry<String, Integer>> stream5 = map.entrySet().stream();
```

## 3 操作详解

### 3.1 无状态

#### 3.1.1 map

##### 1) map

```java
List<String> list = Arrays.asList("a", "b", "c");
List<String> results = list.stream().map(item -> item + "!").collect(Collectors.toList());
System.out.println(results);
```

```
[a!, b!, c!]
```

##### 2) mapToInt

```java
// mapToLong、mapToDouble同理
// mapTo无法使用Collectors.toList()
List<String> list = Arrays.asList("a", "ab", "abc");
List<Integer> results = list.stream().mapToInt(String::length).collect(ArrayList::new, ArrayList::add, ArrayList::addAll);
System.out.println(results);
```

```
[1, 2, 3]
```

##### 3) flatMap

```java
// flat的作用是把二维数组扁平化
List<String> sub1 = Arrays.asList("a", "b", "c");
List<String> sub2 = Arrays.asList("d", "e", "f");
List<List<String>> list = new LinkedList<>();
list.add(sub1);
list.add(sub2);
List<String> results = list.stream().flatMap(Collection::stream).collect(Collectors.toList());
// List<String> results = list.stream().flatMap(sub -> sub.stream()).collect(Collectors.toList());
System.out.println(results);
```

```
[a, b, c, d, e, f]
```

##### 4) flatMapToLong

```java
List<String> sub1 = Arrays.asList("a", "ab", "abc");
List<String> sub2 = Arrays.asList("abcd", "abcde", "abcdef");
List<List<String>> list = new LinkedList<>();
list.add(sub1);
list.add(sub2);
List<Long> results =
        list.stream()
                .flatMapToLong(arr -> arr.stream().mapToLong(item -> (long) item.length()))
                .collect(ArrayList::new, ArrayList::add, ArrayList::addAll);
System.out.println(results);
```

```
[1, 2, 3, 4, 5, 6]
```

#### 3.1.2 filter

```java
List<String> list = Arrays.asList("a", "b", "c");
System.out.println(list.stream().filter(item -> item.equals("a")).collect(Collectors.toList()));
```

```
[a]
```

#### 3.1.3 peek

```java
List<String> list = Arrays.asList("a", "b", "c");
// peek只做执行, 不改变输入输出结果
System.out.println(list.stream().peek(String::toUpperCase).collect(Collectors.toList()));
```

```
[a, b, c]
```

### 3.2 有状态

#### 3.2.1 去重

##### 1) 简单去重

```java
List<String> list = Arrays.asList("a", "b", "c", "a");
System.out.println(list.stream().distinct().collect(Collectors.toList()));
// 对象去重会用到equals, 与hashcode无关, 但是一般还是建议hashcode和equals都重写
List<DistinctObject> distinctObjects = new LinkedList<>();
distinctObjects.add(new DistinctObject(1, "张三"));
distinctObjects.add(new DistinctObject(2, "李四"));
distinctObjects.add(new DistinctObject(1, "张三"));
System.out.println(distinctObjects.stream().distinct().collect(Collectors.toList()));

@AllArgsConstructor
@Getter
@Setter
@ToString
static class DistinctObject {

    private int id;
    private String name;

    @Override
    public int hashCode() {
        return 1;
    }

    @Override
    public boolean equals(Object obj) {
        DistinctObject distinctObject = (DistinctObject) obj;
        return distinctObject.getId() == id;
    }
}
```

```
[a, b, c]
[Stateless.DistinctObject(id=1, name=张三), Stateless.DistinctObject(id=2, name=李四)]
```

##### 2) collector去重

```java
List<DistinctObject> list = Arrays.asList(
                new DistinctObject(1, "张三"),
                new DistinctObject(2, "李四"),
                new DistinctObject(1, "张三"));
List<DistinctObject> newList = list.stream().collect(
        Collectors.collectingAndThen(
                Collectors.toCollection(
                        () -> new TreeSet<>(Comparator.comparing(DistinctObject::getId))), ArrayList::new)
);
System.out.println(newList);
newList = list.stream().collect(
        Collectors.collectingAndThen(
                Collectors.toCollection(
                        () -> new TreeSet<>(Comparator.comparing(DistinctObject::getId).thenComparing(DistinctObject::getName))), ArrayList::new)
);
System.out.println(newList);
```

```
[DistinctDemo.DistinctObject(id=1, name=张三), DistinctDemo.DistinctObject(id=2, name=李四)]
```

##### 3) filter自定义lambda去重

```java
List<DistinctObject> list = Arrays.asList(
                new DistinctObject(1, "张三"),
                new DistinctObject(2, "李四"),
                new DistinctObject(1, "张三"));
System.out.println(list.stream().filter(distinctByKey(DistinctObject::getName)).collect(Collectors.toList()));

private static <T> Predicate<T> distinctByKey(Function<? super T, ?> keyExtractor) {
    Set<Object> seen = ConcurrentHashMap.newKeySet();
    return t -> seen.add(keyExtractor.apply(t));
}
```

```
[DistinctDemo.DistinctObject(id=1, name=张三), DistinctDemo.DistinctObject(id=2, name=李四)]
```

#### 3.2.2 排序

##### 1) 简单排序

```java
List<String> list = Arrays.asList("a", "c", "d", "b");
// 升序
System.out.println(list.stream().sorted().collect(Collectors.toList()));
// 降序
System.out.println(list.stream().sorted(Comparator.reverseOrder()).collect(Collectors.toList()));
```

```
[a, b, c, d]
[d, c, b, a]
```

##### 2) 对象排序

```java
List<SortObject> sortObjects = Arrays.asList(
                new SortObject(1, "张三", 18),
                new SortObject(2, "李四", 16),
                new SortObject(3, "王五", 20),
                new SortObject(5, "周六", 16),
                new SortObject(4, "郑七", 16));
System.out.println(sortObjects.stream()
        .sorted(Comparator.comparing(SortObject::getAge)).collect(Collectors.toList()));
```

```
[
    SortDemo.SortObject(id=2, name=李四, age=16),
    SortDemo.SortObject(id=5, name=周六, age=16),
    SortDemo.SortObject(id=4, name=郑七, age=16),
    SortDemo.SortObject(id=1, name=张三, age=18),
    SortDemo.SortObject(id=3, name=王五, age=20)
]
```

##### 3) 多字段排序

```java
List<SortObject> sortObjects = Arrays.asList(
                new SortObject(1, "张三", 18),
                new SortObject(2, "李四", 16),
                new SortObject(3, "王五", 20),
                new SortObject(5, "周六", 16),
                new SortObject(4, "郑七", 16));
System.out.println(sortObjects.stream()
        .sorted(Comparator.comparing(SortObject::getAge).thenComparing(SortObject::getId))
        .collect(Collectors.toList()));
```

```
[
    SortDemo.SortObject(id=2, name=李四, age=16),
    SortDemo.SortObject(id=4, name=郑七, age=16),
    SortDemo.SortObject(id=5, name=周六, age=16),
    SortDemo.SortObject(id=1, name=张三, age=18),
    SortDemo.SortObject(id=3, name=王五, age=20)
]
```

##### 4) 倒序

```java
List<SortObject> sortObjects = Arrays.asList(
                new SortObject(1, "张三", 18),
                new SortObject(2, "李四", 16),
                new SortObject(3, "王五", 20),
                new SortObject(5, "周六", 16),
                new SortObject(4, "郑七", 16));
// .reversed()只能统一都降序
System.out.println(sortObjects.stream()
        .sorted(Comparator.comparing(SortObject::getAge)
            .thenComparing(SortObject::getId)
            .reversed())
        .collect(Collectors.toList()));
// 可以自定义升降序
System.out.println(sortObjects.stream()
        .sorted(Comparator.comparing(SortObject::getAge, Comparator.reverseOrder())
                .thenComparing(SortObject::getId, Comparator.reverseOrder()))
        .collect(Collectors.toList()));
```

```
[
    SortDemo.SortObject(id=3, name=王五, age=20),
    SortDemo.SortObject(id=1, name=张三, age=18),
    SortDemo.SortObject(id=5, name=周六, age=16),
    SortDemo.SortObject(id=4, name=郑七, age=16),
    SortDemo.SortObject(id=2, name=李四, age=16)
]
[
    SortDemo.SortObject(id=3, name=王五, age=20),
    SortDemo.SortObject(id=1, name=张三, age=18),
    SortDemo.SortObject(id=5, name=周六, age=16),
    SortDemo.SortObject(id=4, name=郑七, age=16),
    SortDemo.SortObject(id=2, name=李四, age=16)
]
```

##### 5) null处理

```java
List<SortObject> sortObjects = Arrays.asList(
                    new SortObject(1, "张三", 18),
                    new SortObject(2, "李四", 16),
                    new SortObject(3, "王五", 20),
                    new SortObject(5, "周六", 16),
                    new SortObject(4, "郑七", 16),
                    new SortObject(null, "", 15));
// Comparator.nullsFirst把null排到前面
// Comparator.nullsLast把null排到后面
System.out.println(sortObjects.stream()
        .sorted(Comparator.comparing(SortObject::getId, Comparator.nullsFirst(Integer::compareTo)))
        .collect(Collectors.toList()));
```

```
[
    SortObject{id=null, name='', age=15},
    SortObject{id=1, name='张三', age=18},
    SortObject{id=2, name='李四', age=16},
    SortObject{id=3, name='王五', age=20},
    SortObject{id=4, name='郑七', age=16},
    SortObject{id=5, name='周六', age=16}
]
```

#### 3.2.3 limit

```java
List<String> list = Arrays.asList("a", "c", "d", "b");
System.out.println(list.stream().limit(3).collect(Collectors.toList()));
```

```
[a, b, c]
```

#### 3.2.4 skip

```java
List<String> list = Arrays.asList("a", "b", "c", "d");
System.out.println(list.stream().skip(1).collect(Collectors.toList()));
```

```
[b, c, d]
```

### 3.3 非短路

#### 3.3.1 forEach

```java
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(System.out::print);
```

```
abc
```

#### 3.3.2 forEachOrdered

```java
List<String> list = Arrays.asList("a", "b", "c");
// parallelStream使用线程池不保证顺序
list.parallelStream().forEach(System.out::print);
list.parallelStream().forEachOrdered(System.out::print);
```

```
bac
abc
```

#### 3.3.3 toArray

```java
List<String> list = Arrays.asList("a", "b", "c");
// 完整写法
String[] result1 = list.stream().toArray(str -> new String[list.size()]);
System.out.println(Arrays.toString(result1));
// 简写
String[] result2 = list.stream().toArray(String[]::new);
System.out.println(Arrays.toString(result2));
```

```
[a, b, c]
[a, b, c]
```

#### 3.3.4 reduce

```java
List<Integer> list = Arrays.asList(1, 1, 1, 1);
// 从0开始累加
System.out.println(list.stream().reduce(0, Integer::sum));
// 从10开始累加
System.out.println(list.stream().reduce(10, (result, curr) -> result + curr));
```

```
4
14
```

#### 3.3.5 collect

```java
List<Integer> list = Arrays.asList(1, 1, 1, 1);
System.out.println(list.stream().collect(Collectors.toList()));
```

```
[1, 1, 1, 1]
```

#### 3.3.6 minAndMax

```java
// 数字
List<Integer> list = Arrays.asList(2, 10, 30, 9);
System.out.println(list.stream().max(Integer::compare).get());
System.out.println(list.stream().max(Comparator.comparing(Integer::valueOf)).get());
// 字母
List<String> strList = Arrays.asList("C", "G", "A");
System.out.println(strList.stream().min(String::compareTo).get());
System.out.println(strList.stream().min(Comparator.comparing(String::valueOf)).get());
// 对象
List<ObjectData> objectDataList = Arrays.asList(
        new ObjectData(1, "zhangsan", 18),
        new ObjectData(2, "lisi", 17),
        new ObjectData(3, "wangwu", 25));
System.out.println(objectDataList.stream().min(Comparator.comparing(ObjectData::getAge)).get());
```

```
30
30
A
A
NonShort.ObjectData(id=2, name=lisi, age=17)
```

#### 3.3.7 count

```java
List<Integer> list = Arrays.asList(2, 1, 3, 4);
System.out.println(list.stream().filter(item -> item > 2).count());
```

```
2
```

### 3.4 短路

#### 3.4.1 match

##### 1) anyMatch

```java
List<Integer> list = Arrays.asList(1, 3, 5);
// 任何一个匹配都可以
System.out.println(list.stream().anyMatch(item -> item.equals(3)));
```
```
true
```

##### 2) allMatch

```java
List<Integer> list = Arrays.asList(1, 1, 1);
// 所有都满足才可以
System.out.println(list.stream().allMatch(item -> item.equals(1)));
```

```
true
```

##### 3) noneMatch

```java
List<Integer> list = Arrays.asList(1, 3, 5);
// 都不满足才可以
System.out.println(list.stream().noneMatch(item -> item.equals(2)));
```

```
true
```

#### 3.4.2 find

##### 1) findFirst

```java
List<Integer> list = Arrays.asList(1, 3, 5);
System.out.println(list.stream().findFirst().get());
```

```
1
```

##### 2) findAny

```java
List<Integer> list = Arrays.asList(1, 3, 5);
// 不会严格保证返回的是第一个
System.out.println(list.stream().findAny().get());
```

```
1
```

## 4 输出结果

### 4.1 toCollection

```java
Stream<String> stream = Stream.of("A", "B", "C", "D");
List<String> list = stream.collect(Collectors.toList());
Set<String> set = stream.collect(Collectors.toSet());
// 指定实现类
ArrayList<String> arrayList = stream.collect(Collectors.toCollection(ArrayList::new));
HashSet<String> hashSet = stream.collect(Collectors.toCollection(HashSet::new));
```

### 4.2 toMap

```java
List<Student> students = Arrays.asList(new Student(1, "张三"), new Student(2, "李四"));
Map<Integer, String> nameMap = students.stream().collect(Collectors.toMap(Student::getId, Student::getName));
System.out.println(nameMap);
Map<Integer, Student> studentMap = students.stream().collect(Collectors.toMap(Student::getId, student -> student));
System.out.println(studentMap);
// 重复key处理
List<Student> duplicatedStudents = Arrays.asList(new Student(1, "张三"), new Student(2, "李四"), new Student(1, "王五"));
Map<Integer, Student> duplicatedStudentMap = duplicatedStudents.stream().collect(
        Collectors.toMap(
                Student::getId,
                student -> student,
                (preStudent, currStudent) -> preStudent));
System.out.println(duplicatedStudentMap);
// 重复key放入集合
Map<Integer, List<Student>> duplicatedStudentListMap = duplicatedStudents.stream().collect(
        Collectors.toMap(
                Student::getId,
                student -> {
                    List<Student> currStudents = new ArrayList<>();
                    currStudents.add(student);
                    return currStudents;
                },
                (List<Student> preStudents, List<Student> currStudents) -> {
                    // 每一个对象都会生成一个集合, 把集合汇总相当于汇总对象
                    preStudents.addAll(currStudents);
                    return preStudents;
                }));
System.out.println(duplicatedStudentListMap);
```

```
{1=张三, 2=李四}
{1=Student(id=1, name=张三), 2=Student(id=2, name=李四)}
{1=Student(id=1, name=张三), 2=Student(id=2, name=李四)}
{1=[Student(id=1, name=张三), Student(id=1, name=王五)], 2=[Student(id=2, name=李四)]}
```

### 4.3 groupingBy

```java
List<Student> students = Arrays.asList(
                new Student(1, "张三"),
                new Student(2, "李四"),
                new Student(3, "王五"),
                new Student(4, "王五"),
                new Student(4, "郑六"));
Map<String, List<Student>> map = students.stream().collect(Collectors.groupingBy(Student::getName));
System.out.println(map);
Map<String, Map<Integer, List<Student>>> deepMap = students.stream().collect(
        Collectors.groupingBy(Student::getName, Collectors.groupingBy(Student::getId)));
System.out.println(deepMap);
// 统计数量
Map<String, Long> countMap = students.stream().collect(Collectors.groupingBy(Student::getName, Collectors.counting()));
System.out.println(countMap);
List<Integer> list = Arrays.asList(3, 1, 4, 3, 1, 1, 5);
Map<Integer, Long> listCountMap = list.stream().collect(Collectors.groupingBy(Function.identity(), Collectors.counting()));
System.out.println(listCountMap);
```

```
{
    李四=[Student(id=2, name=李四)],
    张三=[Student(id=1, name=张三)], 
    王五=[Student(id=3, name=王五), Student(id=4, name=王五)],
    郑六=[Student(id=4, name=郑六)]
}
{
    李四={2=[Student(id=2, name=李四)]}, 
    张三={1=[Student(id=1, name=张三)]}, 
    王五={3=[Student(id=3, name=王五)], 4=[Student(id=4, name=王五)]}, 
    郑六={4=[Student(id=4, name=郑六)]}
}
{
    李四=1, 张三=1, 王五=2, 郑六=1
}
{
    1=3, 3=2, 4=1, 5=1
}
```

### 4.4 partitioningBy

```java
List<Student> students = Arrays.asList(new Student(1, "张三"), new Student(2, "李四"), new Student(3, "王五"));
Map<Boolean, List<Student>> map = students.stream().collect(Collectors.partitioningBy(student -> student.getId() > 1));
System.out.println(map);
```

```
{false=[Student(id=1, name=张三)], true=[Student(id=2, name=李四), Student(id=3, name=王五)]}
```

### 4.5 join

```java
System.out.println(Stream.of("a", "b", "c").collect(Collectors.joining()));
System.out.println(Stream.of(1, 3, 5).map(String::valueOf).collect(Collectors.joining()));
System.out.println(Stream.of(1, 3, 5).map(String::valueOf).collect(Collectors.joining(",")));
System.out.println(Stream.of(1, 3, 5).map(String::valueOf).collect(Collectors.joining(",", "(", ")")));
```

```
abc
135
1,3,5
(1,3,5)
```

## 5 串行/并行

```java
// stream
List<String> list = new LinkedList<>();
list.add("a");
list.add("b");
list.add("c");
List<String> results = list.stream().map(item -> item + "1").collect(Collectors.toList());
System.out.println(results);

// parallelStream并行, 利用fork/join
results = list.parallelStream().map(item -> {
    Thread thread = Thread.currentThread();
    System.out.println(thread.getName());
    return item + "1";
}).collect(Collectors.toList());
System.out.println(results);
```

```
[a1, b1, c1]

ForkJoinPool.commonPool-worker-9
ForkJoinPool.commonPool-worker-9
main
[a1, b1, c1]
```