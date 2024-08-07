
##### Java8总结

##### 函数式编程

Functional Programming

面向对象编程：面向对象的语言，一切皆对象，如果想要调用一个函数，函数必须属于一个类或对象，然后在使用类或对象进行调用。面向对象编程可能需要多写很多重复的代码行。

```java
        Runnable runnable = new Runnable() {
            @Override
            public void run() {
                System.out.println("do something...");
            }
        };

```

函数式编程：在某些编程语言中，如js、c++，我们可以直接写一个函数，然后在需要的时候进行调用，即函数式编程。

##### Lambda 表达式

场景：字符串排序

+ Java8以前

  使用`Collections`的sort方法

  ```java
  List<String> names = Arrays.asList("dabin", "tyson", "sophia");
  
  Collections.sort(names, new Comparator<String>() {
      @Override
      public int compare(String a, String b) {
          return b.compareTo(a);
      }
  });
  ```

+ Java8开始

  可以使用lambda表达式

  ```java
  List<String> names = Arrays.asList("dabin", "tyson", "sophia");
  //使用lambda表达式
  Collections.sort(names, (String a, String b) -> b.compareTo(a));
  //进一步，可以省略入参类型。Java 编译器能够根据类型推断机制判断出参数类型
  names.sort((a, b) -> b.compareTo(a));
  ```

  可以看到，代码变得很简短并且易于阅读。

##### 函数式接口

若用 @FunctionalInterface 标记某个类，则称该类为函数式接口（Functional Interface），反之亦然。

若用 @FunctionalInterface 标记某个类，则限制：该类包含抽象方法的个数 = 1。若 ≠ 1，编译器将抛出错误提示。

只有当一个接口为函数式接口的情况下，才能简写成 Lambda 表达式



```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F from);
}

public class FunctionalInterfaceTest {
    public static void main(String[] args) {
        Converter<String, Integer> converter = (from) -> Integer.valueOf(from);
        Integer converted = converter.convert("666");
        System.out.println(converted);
    }
    /**
     * output
     * 666
     */
}
```



从Java 8开始，@FunctionalInterface 标记了已存在的 Comparator 和 Runnable

+ 因此，Comparator 和 Runnable，可以称为“内置的”函数式接口。
+ 因此，Comparator 和 Runnable的场景，可以使用 Lambda 表达式，简化代码。

###### Comparator

```java
public class ComparatorTest {
    public static void main(String[] args) {
        Comparator<Person> comparator = Comparator.comparing(p -> p.firstName);

        Person p1 = new Person("dabin", "wang");
        Person p2 = new Person("xiaobin", "wang");

        // 打印-20
        System.out.println(comparator.compare(p1, p2));
        // 打印20
        System.out.println(comparator.reversed().compare(p1, p2));
    }

}

class Person {
    public String firstName;
    public String lastName;

    public Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }
}
```

###### Predicate

predicate：断言。

Predicate：一个函数式接口，泛型，返回 boolean 值。用来组合一个复杂的逻辑判断。

```java
Predicate<String> predicate = (s) -> s.length() > 0;
predicate.test("dabin"); // true
```

###### Consumer

Consumer：一个函数式接口，泛型，然后调用 accept，对这个参数做一系列消费操作。

Consumer 源码：

```java
@FunctionalInterface
public interface Consumer<T> {

    void accept(T t);
    
    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

示例1：

```java
public class ConsumerTest {
    public static void main(String[] args) {
        Consumer<Integer> consumer = x -> {
            int a = x + 6;
            System.out.println(a);
            System.out.println("大彬" + a);
        };
        consumer.accept(660);
    }
    /**
     * output
     * 666
     * 大彬666
     */
}
```

示例2：在stream里，对入参做一些操作，主要是用于forEach，对传入的参数，做一系列的业务操作。

```java
// CopyOnWriteArrayList
public void forEach(Consumer<? super E> action) {
    if (action == null) throw new NullPointerException();
    Object[] elements = getArray();
    int len = elements.length;
    for (int i = 0; i < len; ++i) {
        @SuppressWarnings("unchecked") E e = (E) elements[i];
        action.accept(e);
    }
}

CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>();
list.add(1);
list.add(2);
//forEach需要传入Consumer参数
list
    .stream()
    .forEach(System.out::println);
list.forEach(System.out::println);
```

示例3：addThen方法使用。

```java
public class ConsumersTest {
    public static void main(String[] args) {
        Consumer<Integer> consumer1 = x -> System.out.println("first x : " + x);
        Consumer<Integer> consumer2 = x -> {
            System.out.println("second x : " + x);
            throw new NullPointerException("throw exception second");
        };
        Consumer<Integer> consumer3 = x -> System.out.println("third x : " + x);

        consumer1.andThen(consumer2).andThen(consumer3).accept(1);
    }
    /**
     * output
     * first x : 1
     * second x : 1
     * Exception in thread "main" java.lang.NullPointerException: throw exception second
     * 	at com.dabin.java8.ConsumersTest.lambda$main$1(ConsumersTest.java:15)
     * 	...
     */
}
```

##### Stream

使用 `java.util.Stream` 对一个包含一个或多个元素的集合做各种操作，原集合不变，返回新集合。只能对实现了 `java.util.Collection` 接口的类做流的操作。`Map` 不支持 `Stream` 流。`Stream` 流支持同步执行，也支持并发执行。

###### Filter 过滤

Filter` 的入参是一个 `Predicate，用于筛选出我们需要的集合元素。原集合不变。filter 会过滤掉不符合特定条件的，下面的代码会过滤掉`nameList`中不以大彬开头的字符串。

```java
public class StreamTest {
    public static void main(String[] args) {
        List<String> nameList = new ArrayList<>();
        nameList.add("大彬1");
        nameList.add("大彬2");
        nameList.add("aaa");
        nameList.add("bbb");

        nameList
                .stream()
                .filter((s) -> s.startsWith("大彬"))
                .forEach(System.out::println);
    }
    /**
     * output
     * 大彬1
     * 大彬2
     */
}
```

###### Sorted 排序

自然排序，不改变原集合，返回排序后的集合。

```java
public class StreamTest1 {
    public static void main(String[] args) {
        List<String> nameList = new ArrayList<>();
        nameList.add("大彬3");
        nameList.add("大彬1");
        nameList.add("大彬2");
        nameList.add("aaa");
        nameList.add("bbb");

        nameList
                .stream()
                .filter((s) -> s.startsWith("大彬"))
                .sorted()
                .forEach(System.out::println);
    }
    /**
     * output
     * 大彬1
     * 大彬2
     * 大彬3
     */
}
```

逆序排序：

```java
nameList
    .stream()
    .sorted(Comparator.reverseOrder());
```

对元素某个字段排序：

```java
list.stream().sorted(Comparator.comparing(Student::getAge).reversed());
list.stream().sorted(Comparator.comparing(Student::getAge));
```

###### Map 转换

将每个字符串转为大写。

```java
public class StreamTest2 {
    public static void main(String[] args) {
        List<String> nameList = new ArrayList<>();
        nameList.add("aaa");
        nameList.add("bbb");

        nameList
                .stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
    }
    /**
     * output
     * AAA
     * BBB
     */
}
```

###### Match 匹配

验证 nameList 中的字符串是否有以`大彬`开头的。

```java
public class StreamTest3 {
    public static void main(String[] args) {
        List<String> nameList = new ArrayList<>();
        nameList.add("大彬1");
        nameList.add("大彬2");

        boolean startWithDabin =
                nameList
                    .stream()
                    .map(String::toUpperCase)
                    .anyMatch((s) -> s.startsWith("大彬"));

        System.out.println(startWithDabin);
    }
    /**
     * output
     * true
     */
}
```

###### Count 计数

统计 `stream` 流中的元素总数，返回值是 `long` 类型。

```java
public class StreamTest4 {
    public static void main(String[] args) {
        List<String> nameList = new ArrayList<>();
        nameList.add("大彬1");
        nameList.add("大彬2");
        nameList.add("aaa");

        long count =
                nameList
                    .stream()
                    .map(String::toUpperCase)
                    .filter((s) -> s.startsWith("大彬"))
                    .count();

        System.out.println(count);
    }
    /**
     * output
     * 2
     */
}
```

###### Reduce

类似拼接。可以实现将 `list` 归约成一个值。它的返回类型是 `Optional` 类型。

```java
public class StreamTest5 {
    public static void main(String[] args) {
        List<String> nameList = new ArrayList<>();
        nameList.add("大彬1");
        nameList.add("大彬2");

        Optional<String> reduced =
                nameList
                        .stream()
                        .sorted()
                        .reduce((s1, s2) -> s1 + "#" + s2);

        reduced.ifPresent(System.out::println);
    }
    /**
     * output
     * 大彬1#大彬2
     */
}
```


###### flatMap

flatMap 用于将多个Stream连接成一个Stream。

下面的例子，把几个小的list转换到一个大的list。

```java
public class StreamTest6 {
    public static void main(String[] args) {
        List<String> team1 = Arrays.asList("大彬1", "大彬2", "大彬3");
        List<String> team2 = Arrays.asList("大彬4", "大彬5");

        List<List<String>> players = new ArrayList<>();
        players.add(team1);
        players.add(team2);

        List<String> flatMapList = players.stream()
                .flatMap(pList -> pList.stream())
                .collect(Collectors.toList());

        System.out.println(flatMapList);
    }
    /**
     * output
     * [大彬1, 大彬2, 大彬3, 大彬4, 大彬5]
     */
}
```

下面的例子中，将words数组中的元素按照字符拆分，然后对字符去重。

```java
public class StreamTest7 {
    public static void main(String[] args) {
        List<String> words = new ArrayList<String>();
        words.add("大彬最强");
        words.add("大彬666");

        //将words数组中的元素按照字符拆分，然后对字符去重
        List<String> stringList = words.stream()
                .flatMap(word -> Arrays.stream(word.split("")))
                .distinct()
                .collect(Collectors.toList());
        stringList.forEach(e -> System.out.print(e + ", "));
    }
    /**
     * output
     * 大, 彬, 最, 强, 6,
     */
}
```

##### Parallel-Streams

并行流。`stream` 流是支持**顺序**和**并行**的。顺序流操作是单线程操作，串行化的流无法带来性能上的提升，通常我们会使用多线程来并行执行任务，处理速度更快。

```java
public class StreamTest7 {
    public static void main(String[] args) {
        int max = 100;
        List<String> strs = new ArrayList<>(max);
        for (int i = 0; i < max; i++) {
            UUID uuid = UUID.randomUUID();
            strs.add(uuid.toString());
        }

        List<String> sortedStrs = strs.stream().sorted().collect(Collectors.toList());
        System.out.println(sortedStrs);
    }
    /**
     * output
     * [029be6d0-e77e-4188-b511-f1571cdbf299, 02d97425-b696-483a-80c6-e2ef51c05d83, 0632f1e9-e749-4bce-8bac-1cf6c9e93afa, ...]
     */
}
```

##### Map 集合

Java8 针对 map 操作增加了一些方法，非常方便

1、删除元素使用`removeIf()`方法。

```java
public class MapTest {
    public static void main(String[] args) {

        Map<Integer, String> map = new HashMap<>();
        map.put(1, "dabin1");
        map.put(2, "dabin2");

        //删除value没有含有1的键值对
        map.values().removeIf(value -> !value.contains("1"));

        System.out.println(map);
    }
    /**
     * output
     * {1=dabin1}
     */
}
```

2、`putIfAbsent(key, value) ` 如果指定的 key 不存在，则 put 进去。

```java
public class MapTest1 {
    public static void main(String[] args) {

        Map<Integer, String> map = new HashMap<>();
        map.put(1, "大彬1");

        for (int i = 0; i < 3; i++) {
            map.putIfAbsent(i, "大彬" + i);
        }
        map.forEach((id, val) -> System.out.print(val + ", "));
    }
    /**
     * output
     * 大彬0, 大彬1, 大彬2
     */
}
```

3、map 转换。

```java
public class MapTest2 {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("1", 1);
        map.put("2", 2);

        Map<String, String> newMap = map.entrySet().stream()
                .collect(Collectors.toMap(e -> e.getKey(), e -> "大彬" + String.valueOf(e.getValue())));

        newMap.forEach((key, val) -> System.out.print(val + ", "));
    }
    /**
     * output
     * 大彬1, 大彬2,
     */
}
```

4、map遍历。

```java
public class MapTest3 {
    public static void main(String[] args) {
        Map<Integer, String> map = new HashMap<>();
        map.put(1, "大彬1");
        map.put(2, "大彬2");

        //方式1
        map.keySet().forEach(k -> {
            System.out.print(map.get(k) + ", ");
        });

        //方式2
        map.entrySet().iterator().forEachRemaining(e -> System.out.print(e.getValue() + ", "));

        //方式3
        map.entrySet().forEach(entry -> {
            System.out.print(entry.getValue() + ", ");
        });

        //方式4
        map.values().forEach(v -> {
            System.out.print(v + ", ");
        });
    }
}
```





**参考资料**：https://juejin.im/post/5c3d7c8a51882525dd591ac7#heading-16
