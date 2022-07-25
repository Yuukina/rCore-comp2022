# Lab0-0

## 实验环境

采用Github Classroom方式进行在线OS 环境配置。

![image-20220719154420852](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220719154420852.png)

- cargo-binutils

```shell
[编译代码]
$ cargo build

[文件格式]
$ file target/riscv64gc-unknown-none-elf/debug/os

[文件头信息]
$ rust-readobj -h target/riscv64gc-unknown-none-elf/debug/os

[反汇编导出汇编程序]
$ rust-objdump -S target/riscv64gc-unknown-none-elf/debug/os

[执行程序] QEMU User mode
$ qemu-riscv64 target/riscv64gc-unknown-none-elf/debug/os

[编译生成ELF格式的执行文件]
$ cargo build --release

[把ELF执行文件转成bianary文件]
$ rust-objcopy --binary-architecture=riscv64 target/riscv64gc-unknown-none-elf/release/os --strip-all -O binary target/riscv64gc-unknown-none-elf/release/os.bin

[执行程序] QEMU System mode
$ qemu-system-riscv64 -machine virt -nographic -bios ../bootloader/rustsbi-qemu.bin -device loader,file=target/riscv64gc-unknown-none-elf/release/os.bin,addr=0x80200000
```

- tmux
- riscv64-unknown-elf-gdb

## 实验流程

- 将程序的目标平台换成**裸机平台** `riscv64gc-unknown-none-elf`

```shell
$ rustup target add riscv64gc-unknown-none-elf
```

```
# os/.cargo/config
[build]
target = "riscv64gc-unknown-none-elf"
```

**交叉编译**：编译平台 $\neq$ 运行平台。

- 将对标准库 std 的引用换成核心库 core

```rust
// os/src/main.rs
#![no_std]
```

- 告知编译器采用我们自己实现的错误处理函数 （移除 std 带来的问题

```rust
// os/src/lang_items.rs
use core::panic::PanicInfo; // 错误信息

#[panic_handler]
fn panic(_info: &PanicInfo) -> ! {
    loop {}
}
```

```rust
// os/src/main.rs
mod lang_items;
```

- 告诉编译器我们没有一般意义上的 main 函数 （移除 std 带来的问题

```rust
// os/src/main.rs
#![no_main]
```

移除了很多东西变成了空程序，现在要把该加的加上。

---

- 提供入口函数 `_start()`

```rust
// os/src/main.rs
#[no_mangle] // 不让Rust修改此函数的名称
extern "C" fn _start() {
}
```

- 通过操作系统提供的 `exit` 系统调用来退出程序，具体实现见后。`SYSCALL_EXIT`
- 向屏幕输出`"Hello World"`

基于 `print` 函数，实现Rust语言 **格式化宏**，`os/src/console.rs`，可以当做黑盒。

```rust
// os/src/main.rs
#[macro_use] // 复用宏
mod console;
```

已经实现了用户态的最小执行环境，现在实现裸机上的最小执行环境，将把 Hello world! 应用程序从**用户态**搬到**内核态**。

---

- 实现关机功能 `os/src/sbi.rs -> shutdown()`
- 设置正确的程序内存布局 `linker.ld`

```rust
// os/.cargo/config

[target.riscv64gc-unknown-none-elf]
rustflags = [
    "-Clink-arg=-Tsrc/linker.ld", "-Cforce-frame-pointers=yes"
]
```

![image-20220720171228900](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220720171228900.png)

- 设置正确的栈空间布局 `os/src/entry.asm`

```rust
core::arch::global_asm!(include_str!("entry.asm"));

#[no_mangle]
pub fn rust_main() -> ! {
    shutdown();
}
```

![image-20220720171521830](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220720171521830.png)

- 清空 `.bss` 段

## 本章代码树

![代码树](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220720173323393.png)
