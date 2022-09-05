---
date: 2022-04-04T18:53:05+08:00
author: "Rustle Karl"  # 作者

title: "Kotlin 安装指南"  # 文章标题
url:  "posts/kotlin/quickstart/install"  # 设置网页永久链接
tags: [ "kotlin", "install" ]  # 标签
categories: [ "Kotlin 学习笔记" ]  # 分类

toc: true  # 目录
draft: true  # 草稿
---

## 在线运行 Kotlin

使用 Kotlin Playground（`https://play.kotlinlang.org`，一个在线沙箱）来简单尝试 Kotlin。

Kotlin Playground 提供了一种简单的方式来让你尝试 Kotlin，探索你没有使用过的特性，或者在没有本地安装编译器的系统上简单运行 Kotlin。它可以让你访问最新版本的编译器，并提供了独立且基于 Web 的编辑器，使你无须在本地安装 Kotlin 即可添加代码。

## 在本地安装 Kotlin

手动从 Github 安装或者使用适用于你的操作系统的包管理器。

http://kotlinlang.org/docs/tutorials/command-line.html 上的页面讨论了安装命令行编译器的选项。其中一项是下载一个适用于你的操作系统的包含安装器的 ZIP 文件。该页面包含指向 Kotlin 当前版本的 Github 库（https://oreil.ly/AXqXM）的链接。ZIP 文件可以用于 Linux、macOS、Windows，以及源代码发行版。只需解压缩发行版并将其 bin 子目录添加到你的路径即可。

手动安装当然是可行的，但是一些开发者更喜欢使用包管理器。包管理器可以自动执行安装程序，其中一些包管理器还允许你维护指定编译器的多个版本。

### SDKMAN

SDKMAN 最初是为基于 Unix 的 shell 设计的，目前有计划使其也可以用于其他平台。

从使用 curl 安装 SDKMAN 开始安装 Kotlin：

```shell
apt install unzip zip -y
```

```shell
curl -x 192.168.0.117:8118 -fsSL https://get.sdkman.io -o get-sdkman.sh
```

修改 curl 代理，然后执行：

```shell
bash get-sdkman.sh
```

```shell
source "/root/.sdkman/bin/sdkman-init.sh"
```

安装完成后，你可以使用 sdk 命令来安装包含 Kotlin 在内的任何产品：

```shell
sdk install java

sdk install kotlin
```

最新的版本将会被默认安装到 ~/.sdkman/candidates/kotlin 目录，以及一个指向所选版本的名为 current 的链接。

你可以使用 list 命令找出可用的版本：

```kotlin
sdk list kotlin
```

install 命令会默认选择最新版本，但是 use 命令则可以让你在必要时选择任何版本进行安装：

```kotlin
sdk use kotlin 1.3.50
```

### Windows

建议用 JetBrian IDEA 自带。

```kotlin
choco install kotlinc
```

## 在命令行中编译并运行 Kotlin

使用与 Java 类似的 kotlinc-jvm 命令以及 kotlin 命令。

Kotlin 在 JVM 上的 SDK 包括 Kotlin 编译器命令 kotlinc-jvm，以及 Kotlin 运行命令 kotlin。它们类似于 Java 文件中的 javac 与 java 一样。

在 Kotlin 的安装中包含一个名为 kotlinc-js 的脚本，用于将其编译为 JavaScript。

假设你打算使用 JVM 版本。基础脚本 kotlinc 是 kotlinc-jvm 的别名。

例如，考虑一个“Hello，Kotlin！”小型程序，我们将其写在名为hello.kt的文件中。

```shell
vim hello.kt
```

```kotlin
fun main() {
    println("Hello, Kotlin!")
}
```

```shell
kotlin-jvm hello.kt
# or
kotlinc hello.kt
```

```shell
ls
```

```
hello.kt HelloKt.class
```

```shell
kotlin HelloKt
```

编译器生成了 HelloKt.class 文件，其中包含了可以被 Java 虚拟机执行的字节码。Kotlin 不会生成 Java 源文件——因为它不是一个翻译器。它生成的是可以被 JVM 解释的字节码。被编译后的文件采用首字母大写的类名，然后在末尾加上 Kt 作为文件名。这些可以通过注解来控制。如果你希望生成一个自包含的可以被 Java 命令执行的 JAR 文件，可以添加 -include-runtime 参数。这将允许你生成一个可以使用 java 命令执行的 JAR。

```shell
kotlinc hello.kt -include-runtime -d hello.jar
```

该命令将输出一个可以被 java 命令执行的名为 hello.jar 的文件。

```shell
java -jar hello.jar
Hello, Kotlin!
```

移除 -include-runtime 标记将生成一个 JAR 文件，该文件需要 Kotlin 运行时在类路径上执行。

不带任何参数的 kotlinc 命令将启动交互式 Kotlin REPL（Read-Eval-Print Loop）。

## 使用 Kotlin REPL

在交互式的 shell 中运行 Kotlin。通过在命令行键入 kotlinc 来使用 Kotlin REPL。Kotlin 拥有一个交互式的编译器会话管理器，名叫 REPL，由无参数的 kotlinc 命令触发。进入 REPL 后，你可以运行任意的 Kotlin 命令并立即查看结果。

Kotlin REPL 同时也允许 Android Studio 与 IntelliJ IDEA 作为 Kotlin REPL 实体，可以在 Tools → Kotlin 菜单中找到这个功能。

```kotlin
Welcome to Kotlin version 1.5.20 (JRE 11+28)
Type :help for help, :quit for quit
>>> println("Hello, World!")
Hello, World!
>>> println("Hello, World!")
Hello, World!
>>> var name = "Dolly"
>>> println("Hello, $name!")
Hello, Dolly!
>>>
```

REPL 是一种无须启动完整的 IDE 即可快速简单地计算 Kotlin 表达式的工具。如果你不想创建一个完整的工程或基于 IDE 的文件集，又或者你想通过编写一个快捷的 demo 来帮助其他开发者，再或者你没有首选的 IDE 时，都可以使用它。

## 执行 Kotlin 脚本

在以.kts 结尾的文件中输入代码。然后使用 kotlinc 命令的-script 选项来执行它。

kotlinc 命令支持一系列的命令行选项，其中一种允许 Kotlin 作为脚本语言使用。使用 Kotlin 源代码编写的脚本文件（其中包含可执行代码）拥有.kts 文件扩展名。

举个简单的例子，示例文件 southpole.kts 显示了南极的当前时间，并打印当前是否处于夏令时。该脚本使用了 java.time 包。

```kotlin
import java.time.*

val instant = Instant.now()
val southPole = instant.atZone(ZoneId.of("Antarctica/South_Pole"))
val dst = southPole.zone.rules.isDaylightSavings(instant)
println("It is ${southPole.toLocalTime()} (UTC ${southPole.offset}) at the South Pole")
println("The South Pole ${if (dst) "is" else "is not"} on Daylight Savings Time")
```

```kotlin
kotlinc -script southpole.kts

It is 14:14:53.548909 (UTC +12:00) at the South Pole
The South Pole is not on Daylight Savings Time
```

脚本包含通常会显示在类的标准 main 方法中的代码。Kotlin 可以作为 JVM 上的脚本语言来使用。
