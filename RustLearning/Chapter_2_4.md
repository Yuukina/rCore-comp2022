# 2.4 复合类型

`\#![allow(unused_variables)]` 该标记会告诉编译器忽略未使用的变量，不要抛出 `warning` 警告。

`unimplemented!()` 告诉编译器该函数尚未实现，类似的标记还有 `todo!()`，通常意味着我们期望快速完成主要代码，回头再通过搜索这些标记来完成次要代码。

## 2.4.1 字符串与切片

### 切片（slice）

```rust
let s = String::from("hello world");
len = s.len();

let hello = &s[0..5]; //[0,5)
let world = &s[6..len];
```

切片也是借用。

### 字符串

```rust
let s = "Hello, world!";
```

s 是 `&str` 类型，和 `String` 并不一样。不可变引用。**字符串是 UTF-8 编码**。

**遍历**

```rust
for c in "中国人".chars() {
    println!("{}", c);
}
```

> X：字符串不能直接索引，UTF-8无法精准定位，O(1)实现。
>
> X：字符串切片需要格外注意，需要落在字符的边界上。

### String

**追加**

在字符串尾部可以使用 `push()` 方法追加字符 `char`，也可以使用 `push_str()` 方法追加字符串字面量。

**插入**

可以使用 `insert()` 方法插入单个字符 `char`，也可以使用 `insert_str()` 方法插入字符串字面量。

这俩方法需要传入**两个参数**，第一个参数是字符（串）插入位置的索引，第二个参数是要插入的字符（串），索引从 0 开始计数，如果越界则会发生错误。

**替换**

- `replace(A, B)` A 替换成 B，新字符串
- `replacen(A, B, n)` 前 n 个 A 替换成 B，新字符串 
- `replace_range(a..b, A)` a 到 b-1 替换成 A，原字符串

**删除**

- `pop()` 删除并返回字符串的最后一个字符，原字符串
- `remove(x)` 删除并返回字符串中指定位置x的字符，原字符串（注意字符边界）
- `truncate(x)` 删除字符串中从指定位置x开始到结尾的全部字符，原字符串（注意字符边界）
- `clear()` 清空字符串，原字符串

**连接**

- 使用 `+` 或者 `+=` 连接字符串：要求右边的参数必须为字符串的**切片引用（Slice）类型**，+ 和 += 都是返回一个**新的字符串**。所以变量声明可以不需要 mut 关键字修饰。左边的所有权会被转移进 `add()` 函数。
- 使用 format! 连接字符串：与 `print!` 的用法类似。返回值 String。

### String 与 &str 的转换

- `String::from("hello,world")`
- `"hello,world".to_string()`
- 取引用将 `String` 类型转为 `&str` 类型

## 2.4.2 元组

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    let (x, y, z) = tup; //用模式匹配解构元组
    let five_hundred = x.0; //.的访问方式,元组的索引从 0 开始
}
```

## 2.4.3 结构体

```rust
struct User {
    active: bool,
    username: String,
    email: String,
    sign_in_count: u64,
}
```

```rust
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

**每个字段**都需要进行初始化。

可以直接使用缩略的方式进行初始化。

`..user1` 所有权转移。

### 元组结构体

```rust
struct Color(i32, i32, i32);
```

### 单元结构体

```rust
struct AlwaysEqual;
```

但是不关心该类型的内容, 只关心它的行为时，之后再具体实现。

### 匿名结构体

没有名字 `{x: i32, y: i32}`

### 结构体打印

```rust
#[derive(Debug)]
println!("rect1 is {:?}", rect1);
println!("rect1 is {:#?}", rect1);
dbg!(&rect1); //debug 宏，可以打印各种东西
```

## 2.4.4 枚举

```rust
enum PokerCard {
    Clubs(u8),
    Spades(u8),
    Diamonds(char),
    Hearts(char),
}

fn main() {
   let c1 = PokerCard::Spades(5);
   let c2 = PokerCard::Diamonds('A');
}
```

用好了有点强。

### 同一化类型

也就是用同一个函数来处理这两个连接。

### Option枚举用于处理空值

```rust
enum Option<T> {
    Some(T),
    None,
}
```

None，可以更好的处理空值。

## 2.4.5 数组

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

数组切片。
