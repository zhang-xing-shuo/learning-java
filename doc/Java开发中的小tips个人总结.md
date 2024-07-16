



就像写文章一样，编写代码，需要良好的“格式”和“措辞”

当前，软件工程，是以团队为单位，协作开发；

除不断增加新的代码外，代码第一次被编写后，后续可能需要重构。重构建立在读懂业务逻辑的基础上。



遵循一致的代码规范；保持一致的代码风格，有助于 代码的可读性和可维护性

+ 以驼峰命名法，命名类、方法、变量；见名知意

  反例：

  ```java
  int g = 10;
  String S = "Hello";
  ```

  正例：

  ```java
  int count = 10;
  String greeting = "Hello";
  ```

+ 多个连续的空格或制表符 ⟹ 适当的缩进和空格

  反例：

  ```java
  // 多个连续的空格和制表符
  int    a = 10;
  String   name   =   "John";
  ```

  正例：

  ```java
  // 适当的缩进和空格
  int a = 10;
  String name = "John";
  ```




设计、定义变量时，不必要的可见度，增加了不必要的潜在风险

在满足代码功能的前提下，定义变量时，**尽量缩小变量的可见范围**：

优先级：局部变量 > 实例变量 > 类变量

修改前：

```java
public class MyClass {
    private int count;
    public void someMethod() {
        // 只有该方法用到count变量
        count = 0;
        // ...
    }
}
```

修改后：

```java
public class MyClass {
    public void someMethod() {
        int count = 0;
        // ...
    }
}
```



JVM每创建一个对象，都是要消耗资源的。

一个对象的声明周期中，伴随着CPU运算、分配内存、回收垃圾等(资源开销)。

在以下场景中，设法减少创建对象，将明显提高代码的性能

+ **在频繁调用的方法中**

  善用对象池、StringBuilder等技术，以减少创建对象

  反例：

  ```java
  public String formatData(int year, int month, int day) {
      String formattedDate = String.format("%d-%02d-%02d", year, month, day); // 每次调用方法都会创建新的String对象
      return formattedDate;
  }
  ```

  正例：

  ```java
  private static final String DATE_FORMAT = "%d-%02d-%02d";
  public String formatData(int year, int month, int day) {
      return String.format(DATE_FORMAT, year, month, day); // 重复使用同一个String对象
  }
  ```

+ **在循环体中**

  优先在循环体外创建对象，或使用对象池来复用对象，从而尽量减少在循环内创建对象。

  反例：

  ```java
  // 在循环过程中频繁地创建和销毁对象，增加了垃圾回收的负担
  public List<Date> getNextWeekDates() {
      List<Date> dates = new ArrayList<>();
      for (int i = 0; i < 7; i++) {
          Calendar calendar = Calendar.getInstance();
          calendar.add(Calendar.DAY_OF_MONTH, i + 1);
          Date date = calendar.getTime(); // 在循环中频繁创建Calendar和Date对象
          dates.add(date);
      }
      return dates;
  }
  ```

  正例：

  ```java
  // 在循环外部创建对象，减少内存分配和垃圾回收的开销
  public List<Date> getNextWeekDates() {
      List<Date> dates = new ArrayList<>();
      Calendar calendar = Calendar.getInstance();
      Date date = new Date(); // 在循环外部创建Date对象
      for (int i = 0; i < 7; i++) {
          calendar.add(Calendar.DAY_OF_MONTH, 1);
          date.setTime(calendar.getTimeInMillis()); // 复用Date对象
          dates.add(date);
      }
      return dates;
  }
  ```

+ **大量地拼接字符串时**（比如：在循环体中）

  String ⟹ StringBuilder | StringBuffer

  反例：

  ```java
  // 每次循环都产生新的字符串对象
  String result = "";
  for (int i = 0; i < 1000; i++) {
      result += "Number " + i + ", ";
  }
  ```

  正例：

  ```java
  // 避免产生大量临时String对象
  // StringBuilder的内部，通过byte数组的扩容、编辑等，实现字符串的拼接
  StringBuilder result = new StringBuilder();
  for (int i = 0; i < 1000; i++) {
      result.append("Number ").append(i).append(", ");
  }
  ```






**装箱和拆箱**

避免 基本类型和包装类之间 不必要的转换，可以提高代码的性能。

修改前：

```java
Integer num = 10;
int result = num + 5;
```

修改后：

```java
int num = 10;
int result = num + 5;
```

### 数据结构和集合类

选择合适的数据结构和集合类，有助于 代码的可读性和性能。

例如，当要求数据不重复时，优先选择数据结构HashSet

修改前：

```java
int[] nums = new int[]{1, 2, 1};

List<Integer> data = new ArrayList<>();
out:
for (int i = 0; i < nums.length; i++) {
    for (Integer ele : data) {
        if (ele == nums[i]) {
            continue out;
        }
    }
    data.add(nums[i]);
}
```

修改后：

```java
int[] nums = new int[]{1, 2, 1};

Set<Integer> data = new HashSet<>();
for (int i = 0; i < nums.length; i++) {
    data.add(nums[i]);
}
```

### 魔法数 ⟹ 常量

magic number，

“魔法数值” ⟹ 常量，有助于 代码的可读性和可维护性。

反例：

```java
// 硬编码
// 读者看到后会感到莫名其妙
// 降低了代码的可读性，增加后续重构代码的难度
if (status == 1) {
    // ...
}
```

正例：

```java
// 使用常量代替魔法数值
final int STATUS_ACTIVE = 1;
if (status == STATUS_ACTIVE) {
    // ...
}
```

### 常量 ⟹ 枚举

使用枚举可以更清晰地表示一组相关的常量，并且能够提供更多的类型安全性和功能性。

反例：

```java
// 使用常量表示颜色
public static final int RED = 1;
public static final int GREEN = 2;
public static final int BLUE = 3;
```

正例：

```java
// 使用枚举表示颜色
public enum Color {
    RED, GREEN, BLUE
}
```

### 异常

正确地处理异常，有助于代码的健壮性和容错性，避免不必要的try-catch块可以提高代码性能。

反例：

```java
// 捕获所有异常，没有具体处理
try {
    // 一些可能抛出异常的操作
} catch (Exception e) {
    // 空的异常处理块
}

```

正例：

```java
// 捕获并处理特定异常，或向上抛出
try {
    // 一些可能抛出异常的操作
} catch (FileNotFoundException e) {
    // 处理文件未找到异常
} catch (IOException e) {
    // 处理其他IO异常
}
```

### 资源

最常见地，文件流、数据库连接等，是网络资源、IO资源。

使用完资源后，及时关闭它们，可以避免资源泄漏。

反例：

```java
// 未及时关闭数据库连接
Connection conn = null;
Statement stmt = null;
try {
    conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
    stmt = conn.createStatement();
    // 执行数据库查询操作
} catch (SQLException e) {
    e.printStackTrace();
} finally {
    // 数据库连接未关闭
}
```

正例：

```java
// 使用try-with-resources确保资源及时关闭，避免了数据库连接资源泄漏的问题
try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASSWORD);
    Statement stmt = conn.createStatement()) {
    // 执行数据库查询操作
} catch (SQLException e) {
    e.printStackTrace();
}
```

+ **最佳实践**：`try-with-resources`

  场景：在处理需要关闭的资源（如文件、数据库连接等），避免资源泄漏

  最佳实践：通过try-with-resources语句可以自动关闭资源。

  反例：

  ```java
  // 没有使用try-with-resources
  FileReader reader = null;
  try {
      reader = new FileReader("file.txt");
      // 执行一些操作
  } catch (IOException e) {
      // 处理异常
  } finally {
      if (reader != null) {
          try {
              reader.close();
          } catch (IOException e) {
              // 处理关闭异常
          }
      }
  }
  ```

  正例：

  ```java
  // 使用try-with-resources自动关闭资源
  try (FileReader reader = new FileReader("file.txt")) {
      // 执行一些操作
  } catch (IOException e) {
      // 处理异常
  }
  ```

  

### foreach

优先选择foreach语法，遍历集合，将简化代码，有助于代码的可读性(也可能有助于提高性能)。

反例：

```java
// 可读性不强，并且增加了方法调用的开销
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
for (int i = 0; i < names.size(); i++) {
    System.out.println(names.get(i));
}
```

正例：

```java
// 优先选择foreach语句，遍历集合
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
for (String name : names) {
    System.out.println(name);
}
```

### equals()

`==`操作符的操作数为对象时：比较2个对象的地址值。

在比较2个对象，

+ 按照设计意图，
  + Object为根类，其他所有类继承Object，继承Object所有的protect、public方法；
  + Object中，equals()方法的实现：通过==操作，比较2个对象的地址值；
  + Java官方期望，开发者重写，自定义类的equals()方法，从而实现：比较两者的内容

规范：在比较2个对象时，优先使用equals()，而不是==操作符。除非明确地，是要比较两者的地址值。

反例：

```java
String name1 = "Alice";
String name2 = new String("Alice");
// 使用==比较对象的引用，而非内容
if (name1 == name2) {
    // ...
}
```

正例：

```java
String name1 = "Alice";
String name2 = new String("Alice");
// // 使用equals比较对象的内容
if (name1.equals(name2)) {
    // ...
}
```

### 使用日志框架记录日志

System.out.println() ⟹ 通过日志框架（如Log4j、SLF4J），记录下日志：更灵活地管理日志输出和级别。

反例：

```java
// 直接输出日志到控制台
System.out.println("Error occurred"); 
```

正例：

```java
// 使用日志框架记录日志
logger.error("Error occurred"); 
```



:x: :warning::heavy_check_mark:

