---
date: 2022-09-05T14:08:08+08:00  # 创建日期
author: "Rustle Karl"  # 作者

title: "Kotlin 协程休眠函数"  # 文章标题
url:  "posts/kotlin/docs/grammar/coroutine/delay"  # 设置网页永久链接
tags: [ "kotlin", "delay" ]  # 标签
categories: [ "Kotlin 学习笔记" ]  # 分类

toc: true  # 目录
draft: true  # 草稿
---

kotlinx.coroutines.delay() 是一个协程挂起函数。它不会阻塞当前线程，可以执行改线程中的其他协程。 

Thread.sleep() 阻塞当前线程。这意味着在退出 Thread.sleep() 之前，不会执行该线程中的其他代码。

## kotlinx.coroutines.delay()

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

fun main(args: Array<String>) {
    runBlocking {
        run()
    }
}

suspend fun run() {
    coroutineScope {
        val timeInMillis = measureTimeMillis {
            val mainJob = launch {
                //Job 0
                launch {
                    print("A->")
                    delay(1000)
                    print("B->")
                }
                //Job 1
                launch {
                    print("C->")
                    delay(2000)
                    print("D->")
                }
                //Job 2
                launch {
                    print("E->")
                    delay(500)
                    print("F->")
                }

                //Main job
                print("G->")
                delay(1500)
                print("H->")
            }

            mainJob.join()
        }

        val timeInSeconds =
            String.format("%.1f", timeInMillis / 1000f)
        print("${timeInSeconds}s")
    }
}
```

主作业将首先运行，然后将被 delay() 挂起函数挂起，然后是作业 0 -> 作业 1 -> 作业 2。所有作业都在大约同时暂停和启动。然后，最短的 delay() 将首先运行。完成所有作业的 timeinSeconds 应该是最长的 delay()，即 2 秒。

```
G->A->C->E->F->B->H->D->2.0s
```

## Thread.sleep() on Dispatchers.Main

```kotlin
import kotlinx.coroutines.*
import kotlin.system.*

fun main(args: Array<String>) {
    runBlocking {
        run()
    }
}

suspend fun run() {
    coroutineScope {
        val timeInMillis = measureTimeMillis {
            val mainJob = launch {
                //Job 0
                launch {
                    print("A->")
                    delay(1000)
                    print("B->")
                }
                //Job 1
                launch {
                    print("C->")
                    Thread.sleep(2000)
                    print("D->")
                }
                //Job 2
                launch {
                    print("E->")
                    delay(500)
                    print("F->")
                }

                //Main job
                print("G->")
                delay(1500)
                print("H->")
            }

            mainJob.join()
        }

        val timeInSeconds =
            String.format("%.1f", timeInMillis / 1000f)
        print("${timeInSeconds}s")
    }
}
```

与上面的示例 1 类似，主作业将首先运行并通过 delay() 挂起函数挂起，然后是作业 0 → 作业 1。作业 0 将被挂起。但是，当在 Job 1 上运行 Thread.sleep(2000) 时，线程将被阻塞 2 秒。此时作业 2 未执行。

2 秒后，首先打印出 D，然后是 Job 2 中的 E。然后 Job 2 将被暂停。因为 Main 作业和 Job 0 被挂起不到 2 秒，它会立即运行。作业 0 将首先运行，因为挂起时间更短。

0.5 秒后，作业 2 恢复并完成。它将打印出 F。

Timestamp #1 (after 0 second)

- 主作业和作业 0 启动和暂停
- Job 1 启动并阻塞线程

Timestamp #2 (after 2 seconds)

- 作业 1 已完成
- 作业 2 已启动并暂停
- 作业 0 和主要作业恢复并完成

Timestamp #3 (after 0.5 seconds)

- 作业 3 恢复并完成
- 所以总时间消耗在 2.5 秒左右

```
G->A->C->D->E->B->H->F->2.5s
```

## Thread.sleep() on Dispatchers.Default/IO

如果使用 Dispatchers.Default 或 Dispatchers.IO 在后台线程中运行运行挂起函数会怎样。例如：

```
runBlocking {
    withContext(Dispatchers.Default) {
        run()
    }
}
```

输出变成这样：

```
A->C->G->E->F->B->H->D->2.0s
```

输出类似于上面的示例 1，其中 Thread.sleep() 似乎没有阻塞线程！为什么？

当使用 Dispatchers.Default 或 Dispatchers.IO 时，它由线程池支持。每次我们调用 launch{}，都会创建/使用不同的工作线程。

例如，这里是正在使用的工作线程：

- Main job - DefaultDispatcher-worker-1
- Job 0 - DefaultDispatcher-worker-2
- Job 1 - DefaultDispatcher-worker-3
- Job 2 - DefaultDispatcher-worker-4

要查看当前正在运行的线程，可以使用 println("Run ${Thread.currentThread().name}")

所以 Thread.sleep() 确实阻塞了该线程，但只阻塞了 DefaultDispatcher-worker-3。其他作业仍然可以继续运行，因为它们在不同的线程上。

Timestamp #1 (after 0 second)

- Main job, Job 0, Job 1 and Job 2 are started. Sequence can be random. See Note(1) below.
- Main job, Job 0 and *Job2 * are suspended.
- *Job 1 * blocks its own thread.

Timestamp #2 (after 0.5 second)

- Job 2 is resumed and done.

Timestamp #3 (after 1 second)

- Job 0 are resumed and done

Timestamp #4 (after 1.5 seconds)

- Main job are resumed and done

Timestamp #5 (after 2 seconds)

- Job 1 are resumed and done

因为每个作业都在不同的线程上运行，所以作业可以在不同的时间启动。所以 A、C、E、G 的输出可以是随机的。因此，您会看到 initiat 作业的启动顺序与上面的示例 1 中的不同。
