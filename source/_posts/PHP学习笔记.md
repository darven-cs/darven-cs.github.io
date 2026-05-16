---
title: PHP学习笔记
index_img: /img/php.png
date: 2026-01-09 16:57:14
tags: [PHP]
categories: [PHP]
---
<meta name="referrer" content="no-referrer"/>

# 学习php心得
实习的时候，需要维护一个php项目，所以借此机会学习啦一下php的语法。

# 基础语法
## 1. PHP 标签

PHP 代码需要写在特定的标签内：

```php
<?php
// PHP 代码放在这里
?>
```

如果文件只包含 PHP 代码，建议**省略结束标签 `?>`**，避免意外输出空白字符。

## 2. 向浏览器输出内容

- **`echo`**：输出一个或多个字符串（常用）
- **`print`**：输出一个字符串，返回值总是 1

```php
<?php
echo "Hello, PHP!";      // 无换行
print "Welcome";         // 打印后返回1
echo "<br>";             // 输出 HTML 换行
?>
```

注意：每条语句必须以分号 `;` 结尾。

## 3. 注释

```php
<?php
// 这是单行注释

# 这也是单行注释（不常用）

/*
   多行注释
   可以跨行
*/
?>
```

## 4. 变量

- 所有变量以 `$` 开头，后面跟变量名。
- 变量名区分大小写，只能包含字母、数字、下划线，且不能以数字开头。

```php
<?php
$name = "张三";      // 字符串
$age = 25;          // 整数
$height = 1.75;     // 浮点数
$isStudent = true;  // 布尔值
?>
```

## 5. 数据类型

PHP 支持以下基本数据类型：

- **字符串（string）**：单引号或双引号包围。双引号会解析变量。
- **整型（int）**
- **浮点型（float）**
- **布尔型（bool）**
- **数组（array）**
- **对象（object）**
- **NULL**
- **资源（resource）**

示例：

```php
<?php
$str1 = '单引号字符串，变量 $name 不会被解析';
$str2 = "双引号字符串，变量 $name 会被解析为：$name";
echo $str2;  // 输出：双引号字符串，变量 张三 会被解析为：张三
?>
```

## 6. 常量

一旦定义就无法修改或取消。使用 `define()` 或 `const` 关键字。

```php
<?php
define("SITE_NAME", "我的网站");
const PI = 3.14159;

echo SITE_NAME;  // 不需要 $ 符号
?>
```

## 7. 运算符

| 类型     | 运算符示例                    |
| -------- | ----------------------------- |
| 算术     | `+ - * / %`                   |
| 赋值     | `= += -= *= /= .=`（`.` 是字符串拼接） |
| 比较     | `== === != !== > < >= <=`     |
| 逻辑     | `&& || ! and or xor`          |
| 字符串   | `.`（拼接） `.=`（拼接赋值）   |

```php
<?php
$a = 10;
$b = 20;
$sum = $a + $b;
echo "结果：" . $sum;  // 输出：结果：30

$str = "Hello";
$str .= " World";
echo $str;  // Hello World
?>
```

## 8. 控制结构

### 8.1 条件语句

**if...elseif...else**

```php
<?php
$score = 85;
if ($score >= 90) {
    echo "优秀";
} elseif ($score >= 60) {
    echo "及格";
} else {
    echo "不及格";
}
?>
```

**switch**

```php
<?php
$day = "Monday";
switch ($day) {
    case "Monday":
        echo "周一";
        break;
    case "Friday":
        echo "周五";
        break;
    default:
        echo "其他";
}
?>
```

### 8.2 循环

**while 循环**

```php
<?php
$i = 1;
while ($i <= 5) {
    echo "数字: $i <br>";
    $i++;
}
?>
```

**for 循环**

```php
<?php
for ($i = 1; $i <= 5; $i++) {
    echo "当前是第 $i 次循环<br>";
}
?>
```

**foreach 循环**（专用于数组）

```php
<?php
$colors = array("红", "绿", "蓝");
foreach ($colors as $color) {
    echo "颜色: $color<br>";
}
?>
```

## 9. 数组

PHP 中的数组实际上是**有序映射**，可以用作索引数组、关联数组或列表。

### 9.1 索引数组

```php
<?php
$fruits = array("苹果", "香蕉", "橘子");
// 或 $fruits = ["苹果", "香蕉", "橘子"];
echo $fruits[0];   // 输出：苹果
echo count($fruits); // 3
?>
```

### 9.2 关联数组（键值对）

```php
<?php
$student = array("name" => "李华", "age" => 18, "grade" => "A");
echo $student["name"]; // 李华
?>
```

### 9.3 遍历数组

```php
<?php
$student = ["name"=>"李华", "age"=>18];
foreach ($student as $key => $value) {
    echo "$key : $value<br>";
}
?>
```

## 10. 函数

使用 `function` 关键字定义函数，支持参数和返回值。

```php
<?php
function sayHello($name) {
    return "你好, " . $name;
}

echo sayHello("王小明"); // 输出：你好, 王小明
?>
```

**默认参数值**

```php
<?php
function multiply($a, $b = 2) {
    return $a * $b;
}
echo multiply(5);   // 10
echo multiply(5, 3); // 15
?>
```

## 11. 超级全局变量

PHP 内置了许多预定义变量，在任何作用域都可直接使用。常用的有：

- `$_GET`：通过 URL 参数传递的数据（`?name=Tom`）
- `$_POST`：通过 HTTP POST 方法传递的数据（如表单提交）
- `$_SERVER`：服务器和执行环境信息
- `$_SESSION`：会话变量
- `$_COOKIE`：浏览器 Cookie

**示例：处理表单提交**

```php
<!-- form.html -->
<form method="post" action="process.php">
    姓名: <input type="text" name="username">
    <input type="submit">
</form>
```

```php
<?php
// process.php
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = htmlspecialchars($_POST["username"]);
    echo "欢迎, " . $name;
}
?>
```

## 12. 包含文件

可以将重复的代码（如页头、页脚）保存到单独文件中，然后包含进来。

- `include`：如果文件不存在，产生警告，脚本继续执行。
- `require`：如果文件不存在，产生致命错误，脚本停止。

```php
<?php
include 'header.php';
// 页面主体内容
require 'footer.php';
?>
```

## 13. 错误报告（开发阶段）

开发时建议开启所有错误提示：

```php
<?php
error_reporting(E_ALL);
ini_set('display_errors', 1);
?>
```

## 14. 一个完整的示例

```php
<!DOCTYPE html>
<html>
<head>
    <title>PHP 基础示例</title>
</head>
<body>
    <?php
    // 定义变量
    $name = "张三";
    $age = 28;
    
    // 条件判断
    if ($age >= 18) {
        $status = "成年人";
    } else {
        $status = "未成年人";
    }
    
    // 输出内容
    echo "<h1>用户信息</h1>";
    echo "<p>姓名: $name</p>";
    echo "<p>年龄: $age</p>";
    echo "<p>状态: $status</p>";
    
    // 数组循环
    $hobbies = ["阅读", "游泳", "编程"];
    echo "<p>爱好：</p><ul>";
    foreach ($hobbies as $hobby) {
        echo "<li>$hobby</li>";
    }
    echo "</ul>";
    ?>
</body>
</html>
```

---

## 总结

这里我维护的项目比较老，使用的是php5.4,使用的变量都是弱引用类型的。
