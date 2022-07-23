# Lab0-1

LOG=

• **Debug**: fine-grained statements concerning program state, typically used for debugging;

• **Info**: informational statements concerning program state, representing program events or behavior tracking;

• **Warn**: statements that describe potentially harmful events or states in the program;

• **Error**: statements that describe non-fatal errors in the application; this level is used quite often for logging handled exceptions;

• **Fatal**: statements representing the most severe of error conditions, assumedly resulting in program termination.

## 应用程序设计

- 第二章新增系统调用

```rust
/// 功能：将内存中缓冲区中的数据写入文件。
/// 参数：`fd` 表示待写入文件的文件描述符；
///      `buf` 表示内存中缓冲区的起始地址；
///      `len` 表示内存中缓冲区的长度。
/// 返回值：返回成功写入的长度。
/// syscall ID：64
fn sys_write(fd: usize, buf: *const u8, len: usize) -> isize;

/// 功能：退出应用程序并将返回值告知批处理系统。
/// 参数：`xstate` 表示应用程序的返回值。
/// 返回值：该系统调用不应该返回。
/// syscall ID：93
fn sys_exit(xstate: usize) -> !;
```

- `user/src/linker.ld` 将程序的起始物理地址调整为 0x80400000

- 寄存器

`x10~x17` : 对应 `a0~a7`

`x1` ：对应 `ra`

约定寄存器 `a0~a6` 保存系统调用的参数， `a0` 保存系统调用的返回值， 寄存器 `a7` 用来传递 syscall ID。

![image-20220722184354890](https://raw.githubusercontent.com/Yuukina/XSJ-Img/main/img/image-20220722184354890.png)

- 层层封装，实现用户态到内核态的切换

- 编译生成应用程序二进制码

## 实现批处理操作系统

`link_app.S` ，它是在 `make run` 构建操作系统时自动生成的，插入了三个应用程序的二进制镜像。

- 应用管理器

```rust
// os2/src/batch.rs
struct AppManager {
    num_app: usize,
    current_app: usize,
    app_start: [usize; MAX_APP_NUM + 1],
}
```

`load_app`时要记得清空i-cache（只读缓存）

- batch 子模块对外暴露出如下接口：

init ：调用 print_app_info 的时第一次用到了全局变量 APP_MANAGER ，它在这时完成初始化；

run_next_app ：批处理操作系统的核心操作，即加载并运行下一个应用程序。 批处理操作系统完成初始化，或者应用程序运行结束/出错后会调用该函数。

## 实现特权级的切换

- 硬件控制机制
- 换栈

```rust
impl UserStack {
    fn get_sp(&self) -> usize {
        self.data.as_ptr() as usize + USER_STACK_SIZE // 数组结尾地址+栈大小
    }
}
```

将 sp 寄存器的值修改为 get_sp 的返回值即可。

- trap 上下文

包含所有的通用寄存器 `x0~x31` ，还有 `sstatus` 和 `sepc`。`os/wrc/trap/context.rs` 中

- 上下文切换

在初始化时，需要修改 `stvec` 寄存器来执行正确的Trap处理入口点 `os/src/trap/mod.rs` 中。

上下文保存与恢复 `os/src/trap/trap.S ` 中。`__alltraps`、`__restore`

Trap分发及处理 `os/src/trap/mod.rs` 中。`trap_handler`

- 执行应用程序

为 `TrapContext` 实现 `app_init_context` 方法。

`os/src/batch.rs/run_next_app()`

