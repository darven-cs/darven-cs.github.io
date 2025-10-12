---
title: Java21的新特性
index_img: /img/java.png
date: 2025-10-08 18:43:02
tags:
  - Java
  - Java 21
  - 新特性
categories:
  - Java
---
<meta name="referrer" content="no-referrer"/>

> 最近学习Spring AI，在 JDK 21 中，又新增了一些特性，

### 1. **虚拟线程（Virtual Threads）—— 正式版（JEP 444）**
虚拟线程是轻量级线程，由 JVM 管理而非操作系统，能在有限的操作系统线程上高效运行大量并发任务（可轻松创建数百万个），大幅降低高并发场景下的资源消耗和编程复杂度。
类似Go语言的协程，虚拟线程与传统线程相比，更轻量级、更简单，且更适合高并发场景。
- 无需修改现有代码中 `Runnable`/`Callable` 接口的使用，通过 `Thread.startVirtualThread()` 或 Executor 框架（如 `Executors.newVirtualThreadPerTaskExecutor()`）即可创建。
- 适合 I/O 密集型任务（如网络请求、文件读写），避免传统线程的上下文切换开销。


### 2. **记录模式（Record Patterns）—— 正式版（JEP 440）**
记录模式用于解构 **记录（Record）** 类型的数据，配合模式匹配简化数据提取逻辑，尤其适合嵌套结构的处理。  
例如：
```java
record Point(int x, int y) {}
record Circle(Point center, int radius) {}

// 解构 Circle 中的 Point
if (obj instanceof Circle(Point(int x, int y), int r)) {
    System.out.println("圆心坐标：(" + x + "," + y + ")，半径：" + r);
}
```


### 3. **模式匹配的 switch 增强（JEP 441）**
完善了 switch 表达式的模式匹配能力，支持更多场景（如 null 处理、卫语句），让分支逻辑更简洁。
- **支持 null 作为 case 标签**：无需在 switch 外单独判断 null。
  ```java
  String s = null;
  switch (s) {
      case null -> System.out.println("值为 null");
      case "hello" -> System.out.println("匹配成功");
      default -> System.out.println("其他值");
  }
  ```  
- **卫语句（Guards）**：通过 `when` 关键字添加额外条件，细化模式匹配。
  ```java
  switch (obj) {
      case Integer i when i > 0 -> System.out.println("正整数：" + i);
      case Integer i -> System.out.println("非正整数：" + i);
  }
  ```


### 4. **未命名模式与未命名变量（Unnamed Patterns and Variables）—— 正式版（JEP 443）**
引入下划线 `_` 作为“未命名”标记，用于忽略不需要的变量或模式组件，提升代码可读性。
- 未命名变量：忽略方法参数或循环变量。
  ```java
  // 忽略异常信息（只关心是否抛出）
  try { ... } catch (Exception _) { 
      System.out.println("发生异常"); 
  }
  ```  
- 未命名模式：解构时忽略部分字段。
  ```java
  if (obj instanceof Point(int x, _)) { // 只关心 x，忽略 y
      System.out.println("x 坐标：" + x);
  }
  ```


### 5. **字符串模板（String Templates）—— 预览版（JEP 430）**
提供更安全、灵活的字符串拼接方式，支持嵌入表达式（类似其他语言的“插值字符串”），避免 `+` 拼接的繁琐和 SQL 注入等风险。  
通过 `STR` 处理器使用：
```java
String name = "Java";
int version = 21;
String info = STR."Hello, \{name} \{version}!"; // 结果："Hello, Java 21!"
```  
还支持自定义处理器（如 SQL 模板处理器自动转义参数）。


### 6. **向量 API（Vector API）—— 第五孵化器（JEP 448）**
旨在简化高性能数值计算，通过向量指令（如 SIMD）利用硬件并行性，提升数组运算（如图像处理、科学计算）的效率。  
例如，对两个数组进行元素级加法：
```java
VectorSpecies<Integer> SPECIES = IntVector.SPECIES_256;
IntVector va = IntVector.fromArray(SPECIES, a, 0);
IntVector vb = IntVector.fromArray(SPECIES, b, 0);
IntVector vc = va.add(vb); // 并行计算每个元素的和
vc.intoArray(c, 0);
```


### 7. **作用域值（Scoped Values）—— 预览版（JEP 446）**
替代线程局部变量（`ThreadLocal`），为虚拟线程等场景提供更安全、高效的共享数据方式。作用域值具有不可变性，且会自动随线程生命周期（如虚拟线程的任务传递）传播，避免 `ThreadLocal` 的内存泄漏风险。


### 其他特性
- **结构化并发（Structured Concurrency）—— 预览版（JEP 453）**：简化多线程任务的管理，将相关子任务视为一个整体，确保失败时统一处理，避免僵尸线程。
- **ZGC 优化**：默认启用并发线程栈处理，进一步降低垃圾回收（GC）的停顿时间。


Java 21 的特性聚焦于 **简化并发编程**（虚拟线程、作用域值）、**提升代码可读性**（模式匹配、未命名变量）和 **增强性能**（向量 API），尤其对高并发应用和数据处理场景友好。
