# BurgerCo

c++11 coroutine

## 协程是什么

轻量级的调度运行单位

## 协程到底解决什么问题

请求mysql，redis，web

同步和异步

当req发送给服务器，等待rsp，就是一个同步操作(请求数据和响应数据在同一个流程，recv/send与epoll_wait在同一流程)

异步操作反之

hints: 注意异步io和io异步操作的区别

异步性能 > 同步性能

协程 --> 异步的性能，同步的编程方式

## 如何把同步改成异步

1. commit 

2. callback

3. create_context()

4. destroy_context()

## yiled and resume

yiled 让出cpu

resume 重新恢复运行

实现方案 ： 

1. stjmp / longjmp

2. ucontext

3. 自己实现切换(汇编操作)

```assembly
leaq (%rsp),%rax
movq %rax, 104(%rdi)
movq %rbx, 96(%rdi)
movq %rcx, 88(%rdi)
movq %rdx, 80(%rdi)
movq 0(%rax), %rax
movq %rax, 72(%rdi) 
movq %rsi, 64(%rdi)
movq %rdi, 56(%rdi)
movq %rbp, 48(%rdi)
movq %r8, 40(%rdi)
movq %r9, 32(%rdi)
movq %r12, 24(%rdi)
movq %r13, 16(%rdi)
movq %r14, 8(%rdi)
movq %r15, (%rdi)
xorq %rax, %rax

movq 48(%rsi), %rbp
movq 104(%rsi), %rsp
movq (%rsi), %r15
movq 8(%rsi), %r14
movq 16(%rsi), %r13
movq 24(%rsi), %r12
movq 32(%rsi), %r9
movq 40(%rsi), %r8
movq 56(%rsi), %rdi
movq 80(%rsi), %rdx
movq 88(%rsi), %rcx
movq 96(%rsi), %rbx
leaq 8(%rsp), %rsp
pushq 72(%rsi)

movq 64(%rsi), %rsi
ret
```

yield 和 resume都是调用switch去做切换

## 协程如何定义

fd 和 ctx关联

struct {
    int fd;
    context ctx;
    void *func(void*);
    void *params;

    void *stack;
    size_t stack_size;

    usigned int status;  // ready, running, defer, wait

    queue_node(coroutine) ready_node;

    rbtree_node(coroutine) defer_node;
    rbtree_node(coroutine) wait_node;
};

调度器 ：决定调度哪个单位

## 协程分配的数量多少个合适

## 调度器

## 多核模式,多个CPU

粘合 -- 与cpu绑定

进程 --> fork() --> cpu  --> 进程和进程之间，协程不共享

多线程  需要共用一个调度器，要加锁

红黑树的线程安全

