# 2.1 变量绑定与解构

## 2.1.1 变量绑定与解构

### 变量命名

对于 `type-level` 的构造 Rust 倾向于使用**驼峰命名法**（UpperCamelCase），而对于 `value-level` 的构造使用蛇形命名法（snake_case）。

### 变量绑定

```Rust
let x = 5;
```

### 变量的可变性

`Rust` 可以手动设定变量的**可变性**（是否可修改变量的值，`Erlang` 即不可变）。兼顾灵活性和安全性，还可以带来运行性能上的提升。

`Rust` 的变量在<u>默认</u>情况下是<u>不可变</u>的。`mut` 可以将变量变为可变。

```Rust
let mut x = 5;
```

### 不可变变量和常量之间的差异

常量使用 `const` 关键字，常量在程序运行的整个过程中都有效，对于需要在多处代码共享一个不可变的值时非常有用。

不可变，并且值的类型**必须**标注。

```rust
const MAX_POINTS: u32 = 100_000;
```

> Q：并不是在中途可以变，而是在定义的时候可以自主选择，那跟常量的区别在哪呢。
>
> A：const 的值在编译时可以确定，不可变变量再怎么不可变，它都还是变量啊，既然是运行时才能确定的东西当然要和 const 分开，这是再自然不过的事。一个是编译时确定，一个是运行时确定，不一定是具体的值，可以看情况而定。
> 
### 使用下划线开头忽略未使用的变量

你希望告诉 Rust **不要警告未使用的变量**，为此可以用下划线作为变量名的开头`let _x = 27`。

```rust
#[allow(unused_variables)]
```

### 变量解构

```rust
p = (1,2,3)
(x,y,z) = p
```

### 变量屏蔽

同名的新变量可以屏蔽之前的变量。

