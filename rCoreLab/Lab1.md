# Lab1

- 第三章新增系统调用

```rust
/// 功能：应用主动交出 CPU 所有权并切换到其他应用。
/// 返回值：总是返回 0。
/// syscall ID：124
fn sys_yield() -> isize;

/// 功能：获取当前的时间，保存在 TimeVal 结构体 ts 中，_tz 在我们的实现中忽略
/// 返回值：返回是否执行成功，成功则返回 0
/// syscall ID：169
fn sys_get_time(ts: *mut TimeVal, _tz: usize) -> isize;
```

## 多道程序放置与加载

程序需要放到不同的位置，指定链接时 .text 段的地址为 `0x80400000 + app_id * 0x20000`。`user/build.py`

os2中的子模块 `os/src/batch.rs` 被拆分为 `loader` 和 `task`，前者负责启动时加载应用程序，后者负责切换和调度。

## 任务切换

`os/src/task/switch.S`

## 管理多道程序

`sys_yield` 主动挂起，等待外设完成。

任务控制块，应该是PCB。

```rust
// os/src/task/task.rs

#[derive(Copy, Clone)]
pub struct TaskControlBlock {
    pub task_status: TaskStatus,
    pub task_cx: TaskContext,
}
```

`os/src/task/mod.rs` 任务管理器 `TaskManager`。

`sys_exit` 基于 `task` 子模块提供的 `exit_current_and_run_next` 接口，它的含义是退出当前的应用并切换到下个应用。

`TASK_MANAGER` 管理程序，其中可变变量用 `UPSafeCell<TaskManagerInner>` 保护访问。

分两步，先挂起，`TASK_MANAGER.mark_current_suspended();`，修改状态。

再运行下一个程序，`TASK_MANAGER.run_next_task();`

调用 `task::run_first_task` 来执行第一个应用：

## 分时多任务系统

时钟与计时器：`os/src/timer.rs/get_time_us()`

`trap_handler` 函数下新增一个分支，触发了 S 特权级时钟中断时，重新设置计时器，实现RR。

我们需要在执行第一个应用前调用 `enable_timer_interrupt()` 设置 sie.stie， 使得 S 特权级时钟中断不会被屏蔽。（默认屏蔽同级中断）

## 本章代码参考树

```
── os3-ref
   ├── build.rs
   ├── Cargo.toml
   ├── Makefile
   └── src
       ├── batch.rs(移除：功能分别拆分到 loader 和 task 两个子模块)
       ├── config.rs(新增：保存内核的一些配置)
       ├── console.rs
       ├── logging.rs
       ├── sync
       ├── entry.asm
       ├── lang_items.rs
       ├── link_app.S
       ├── linker.ld
       ├── loader.rs(新增：将应用加载到内存并进行管理)
       ├── main.rs(修改：主函数进行了修改)
       ├── sbi.rs(修改：引入新的 sbi call set_timer)
       ├── syscall(修改：新增若干 syscall)
       │   ├── fs.rs
       │   ├── mod.rs
       │   └── process.rs
       ├── task(新增：task 子模块，主要负责任务管理)
       │   ├── context.rs(引入 Task 上下文 TaskContext)
       │   ├── mod.rs(全局任务管理器和提供给其他模块的接口)
       │   ├── switch.rs(将任务切换的汇编代码解释为 Rust 接口 __switch)
       │   ├── switch.S(任务切换的汇编代码)
       │   └── task.rs(任务控制块 TaskControlBlock 和任务状态 TaskStatus 的定义)
       ├── timer.rs(新增：计时器相关)
       └── trap
           ├── context.rs
           ├── mod.rs(修改：时钟中断相应处理)
           └── trap.S
```

