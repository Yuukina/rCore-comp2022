# 2.2 基本类型

**静态**类型语言，<u>基本类型</u>和<u>复合类型</u>。

## 2.2.1 数值类型

有符号整数 (`i8`, `i16`, <u>`i32`（默认）</u>, `i64`, `isize`)、 无符号整数 (`u8`, `u16`, `u32`, `u64`, `usize`) 、浮点数 (`f32`, <u>`f64`（默认）</u>)。

Rust 的数值上可以使用方法。

### 整数类型

| 长度       | 有符号类型                        | 无符号类型    |
| ---------- | --------------------------------- | ------------- |
| 8 位       | `i8`                              | `u8`          |
| 16 位      | `i16`                             | `u16`         |
| 32 位      | `i32`                             | `u32`         |
| 64 位      | `i64`                             | `u64`         |
| 128 位     | `i128`                            | `u128`        |
| 视架构而定 | `isize`                           | `usize`       |
| 数字范围   | $-(2^{n - 1}) \to  2^{n - 1} - 1$ | $0 \to 2^n-1$ |

| 数字字面量         | 示例          |
| ------------------ | ------------- |
| 十进制             | `98_222`      |
| 十六进制           | `0xff`        |
| 八进制             | `0o77`        |
| 二进制             | `0b1111_0000` |
| 字节 (仅限于 `u8`) | `b'A'`        |

> X：下划线是为了增加数字的可读性。

整数溢出，debug模式报错，release模式补码循环溢出。

> X：`i8::MAX` 可以得到 `i8` 类型的最大值。

### 浮点类型

- 避免在浮点数上测试相等性
- 当结果在数学上可能存在未定义时，需要格外的小心

### 有理数和负数

有理数和复数并未包含在标准库中，好在社区已经开发出高质量的 Rust 数值库：[num](https://crates.io/crates/num)。

### NaN

`NaN` 不能用来比较，可以使用 `is_nan()` 等方法，可以用来判断一个数值是否是 `NaN`。

### 数字运算、位运算

只有同类型，才能运算。（不会自动转换，**必须是显式的**）

![image-20220711154142423](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220711154142423.png)

### 序列

Rust 提供了一个非常简洁的方式序列（Range），用来生成连续的数值，例如 `1..5`，生成从 1 到 4 的连续数字，**不包含** 5 ；`1..=5`，生成从 1 到 5 的连续数字，**包含** 5，它的用途很简单，常常用于循环中。序列只允许用于数字或**字符类型**，字符类型：`'a'..='z'`。浮点数好像不行，倒过来好像也不可行。

## 2.2.2 字符、布尔、单元类型

### 字符类型

Rust 的字符不仅仅是 `ASCII`，**<u>所有的 `Unicode` 值</u>**都可以作为 Rust 字符，包括单个的中文、日文、韩文、emoji 表情符号等等，都是合法的字符类型，存储为 **<u>4 个字节</u>**。

> X：Rust 的字符只能用 `''` 来表示， `""` 是留给字符串的。

### 布尔类型

`true`和`false`，存储为 **<u>1 个字节</u>**。

### 单元类型

即 `()` ，其唯一的值也是 `()`，存储为 **<u>0 个字节</u>**。

这种用法和 Go 语言的 ***struct{}*** 类似，可以作为一个值用来占位，但是完全**不占用**任何内存。

`main` 函数就返回这个单元类型 `()`，你不能说 `main` 函数无返回值，因为没有返回值的函数在 Rust 中是有单独的定义的。

## 2.2.3 语句与表达式

Rust 的函数体是由一系列语句组成，最后由一个**表达式来返回值**。

```rust
let y = {
    let x = 3;
    x + 1
};
```

这也可以，神神奇奇，**语句块**也是一个表达式，返回值不需要 `;`。

## 2.2.4 函数

```rust
fn plus_or_minus(x:i32) -> i32 {
    if x > 5 {
        return x - 5
    }

    x + 5
}
```

- 函数的位置可以随便放，Rust 不关心我们在哪里定义了函数，只要有定义即可
- 可以用 `return` 提前返回表达式。

**发散函数**：当用 `!` 作函数返回类型的时候，表示该函数永不返回( diverge function )