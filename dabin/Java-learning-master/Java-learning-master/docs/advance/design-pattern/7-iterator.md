---
sidebar: heading
title: 设计模式之迭代器模式
category: 设计模式
tag:
  - 设计模式
head:
  - - meta
    - name: keywords
      content: 迭代器模式,设计模式,迭代器
  - - meta
    - name: description
      content: 设计模式常见面试题总结，让天下没有难背的八股文！
---

# 迭代器模式

提供一种方法顺序访问一个聚合对象中的各个元素, 而又不暴露其内部的表示。

把在元素之间游走的责任交给迭代器，而不是聚合对象。

**应用实例：**JAVA 中的 iterator。

**优点：** 1、它支持以不同的方式遍历一个聚合对象。 2、迭代器简化了聚合类。 3、在同一个聚合上可以有多个遍历。 4、在迭代器模式中，增加新的聚合类和迭代器类都很方便，无须修改原有代码。

**缺点：**由于迭代器模式将存储数据和遍历数据的职责分离，增加新的聚合类需要对应增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性。

**使用场景：** 1、访问一个聚合对象的内容而无须暴露它的内部表示。 2、需要为聚合对象提供多种遍历方式。 3、为遍历不同的聚合结构提供一个统一的接口。

**迭代器模式在JDK中的应用**

ArrayList的遍历：

```java
Iterator<Integer> iter = null;

System.out.println("ArrayList：");
iter = arrayList.iterator();
while (iter.hasNext()) {
    System.out.print(iter.next() + "\t");
}
```



