---
date: 2022-04-04T18:53:05+08:00
author: "Rustle Karl"  # 作者

title: "用 GraalVM 构建独立程序"  # 文章标题
url:  "posts/kotlin/quickstart/graalvm"  # 设置网页永久链接
tags: [ "kotlin", "graalvm" ]  # 标签
categories: [ "Kotlin 学习笔记" ]  # 分类

toc: true  # 目录
draft: true  # 草稿
---

## 简介

使用 GraalVM 编译器与原生镜像(native-image)工具，生成一个可以在命令行执行且不需要任何额外依赖项的应用程序。

GraalVM（https://www.graalvm.org）是一个高性能的虚拟机，它提供了一个跨语言的运行时来运行由各种语言编写的应用程序。你可以使用诸如 Java 或 Kotlin 之类的基于 JVM 的语言编程，并与 JavaScript、Ruby、Python、R 等语言集成。GraalVM 的一个很棒的特性就是你可以用它为你的代码创建一个原生的可执行程序文件。

## 安装

### Windows

必须安装指定的 Java：

```shell
choco install graalvm-java17
```

```shell
sdk install java 22.0.0.2.r17-grl
```

```shell
gu install native-image
```

要生成原生镜像，首先需使用 -include-runtime 选项编译脚本。

```shell
kotlinc hello.kt -include-runtime -d hello.jar
```

GraalVM 系统包含了原生镜像工具，可以使用它生成原生的可执行文件。

使用 GraalVM 构建一个原生的可执行文件：

```shell
native-image -jar hello.jar
```

### Ubuntu

```
apt install -y gcc build-essential zlib1g zlib1g-dev
```

> 为了进行编译，原生镜像依赖本地工具链，因此请确保系统上可以使用 glibc-devel、zlib-devel（C 库的头文件与 zlib）以及 gcc。

CPU 差的话，编译相当慢，而且占内存资源。

输出结果将是一个可以在命令行调用的名为 hello 的文件。在 Mac 或其他基于 Unix 的系统上你将看到如下输出：

```shell
./hello
```

```shell
Hello, World!
```

## 三种方法比较

现在我们有三种可以运行源文件的方法：

- 使用 kotlinc-jvm 编译并用 kotlin 来执行。
- 编译引入运行时并使用 java 来执行结果 JAR 文件。
- 使用 kotlinc 编译，使用 GraalVM 创建一个原生镜像，然后使用命令行执行它。

这三种方式编译产生的结果文件在大小上有很大的不同。编译后的字节码文件 HelloKt.class 只有 700 字节。hello.jar 文件以及引入的运行时一共大概 1.2 MB。原生镜像则很大，大概有 2.1MB。但是即使是在如此小的源文件上，速度差异也很大。

```shell
kotlinc hello.kt

time kotlin HelloKt
```

```shell
Hello, Kotlin!

real    0m0.981s
user    0m0.897s
sys     0m0.195s
```

```shell
time java -jar hello.jar
```

```shell
Hello, Kotlin!

real    0m0.582s
user    0m0.339s
sys     0m0.083s
```

```shell
time ./hello
```

```shell
Hello, Kotlin!

real    0m0.012s
user    0m0.000s
sys     0m0.012s
```

相对值将说明问题。尽管 JAR 文件比直接运行 kotlin 快一些，但原生映像实际上要快一个数量级。在本示例中，只需要大约 8 毫秒即可运行。


如果你是 Gradle 用户，你可以使用名为 grade-graal 的 GraalVM 插件。它会将一个原生镜像任务（和其他任务）添加到你的构建中。

## 为 Gradle 添加 Kotlin 插件（Groovy 语法）

通过在构建文件中使用 Groovy DSL 标签添加 Kotlin 依赖与插件。

Gradle 构建工具（https : //gradle.org）支持使用 JetBrains 提供的插件在 JVM 上编译 Kotlin。kotlin-gradle-plugin 已在 Gradle 插件仓库（https://plugins.gradle.org）中注册了添加到 Gradle 构建脚本的操作，并且可以添加到 Gradle 构建脚本。这段代码展示了如何将名为 build.gradle 的文件添加到工程根目录。

使用 plugins 块添加 Kotlin 插件（Groovy DSL）

```shell
plugins {
    id "org.jetbrains.kotlin.jvm" version "1.3.50"
}
```

Groovy DSL 语法，该语法支持单引号与双引号字符串。与 Kotlin 一样，Groovy 在进行插值 [ 插图 ] 时会使用双引号字符串，但是由于此处不需要进行插值，因此单引号字符串也可以使用。plugins 块不需要你手动声明去哪里寻找插件，下一个示例中的 repositories 块是用于做这件事的。在 Gradle 插件仓库中注册的任何 Gradle 插件都是如此。使用 plugins 块还可以自动“应用”（apply）插件，因此也不需要 apply 语句。

虽不做强制要求，但建议工程中要有 settings.gradle 文件。它在 Gradle 的初始化阶段进行处理，这是在 Gradle 确定需要分析哪些工程构建文件时进行的。在多工程构建中，设置文件显示了根目录下的哪些子目录本身也是 Gradle 工程。Gradle 可以在子工程之间共享设置与依赖性，可以使一个子工程依赖于另一个子工程，甚至可以并行处理子工程的构建。有关详细信息，请参见 Gradle 用户手册（https://oreil.ly/mwGJW）中的多工程构建部分。

## 为 Gradle 添加 Kotlin 插件（Kotlin 语法）

通过在构建文件中使用 Kotlin DSL 标签添加 Kotlin 依赖与插件。

使用 plugins 块添加 Kotlin 插件(Kotlin DSL):

```shell
plugins {
    kotlin("jvm") version "1.3.50"
}
```

Kotlin DSL 的默认构建文件名是 settings.gradle.kts 与 build.gradle.kts。

可以看到，Kotlin DSL 与 Groovy DSL 语法的最大不同如下所示：·在任何字符串中都应该使用双引号。· Kotlin DSL 中需要括号。· Kotlin 使用等号（=）来赋值，而不是冒号（：）。虽不做强制要求，但建议工程中要有 settings.gradle.kts 文件。它在 Gradle 的初始化阶段进行处理，这是在 Gradle 确定需要分析哪些工程构建文件时进行的。在多工程构建中，设置文件显示了根的哪些子目录本身也是 Gradle 工程。Gradle 可以在子工程之间共享设置与依赖性，可以使一个子工程依赖于另一个子工程，甚至可以并行处理子工程的构建。有关详细信息，请参见 Gradle 用户手册（https://oreil.ly/L3BUe）中的多工程构建部分。

Kotlin 源代码可以与 Java 源代码混合放在 src/main/java 或 src/main/kotlin 路径中，或者你可以使用 Gradle 的 sourceSets 属性以添加自己的源文件。查看 Gradle 文档（https://oreil.ly/XG4EN）以获取更多细节。
