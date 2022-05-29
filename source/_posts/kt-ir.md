---
title: 【Kotlin Compiler】IR 介绍
date: 2022-05-29 20:37:24
categories: Kotlin
tags: Kotlin Compiler
---

【Kotlin Compiler】IR 介绍
---

> 本文会介绍 Kotlin Compiler 中 IR 是什么，以及操作 IR 的 api 是什么
> 在了解 IR 相关 api 后，可以继续学习 [IR Transform Plugin 教程]()，自己动手制作一个 Transform IR 的插件

### Kotlin IR 是什么

![](./kotin_ir.png)

IR 全称是 intermediate representation，表示编译过程中的中间信息，由编译器`前端`对源码分析后得到，随后会输入到`后端`进一步编译为机器码

IR 可以有一系列的表现方式，由高层表示逐渐下降(lowering)到低层

我们所讨论的 Kotlin IR 是抽象语法树结构（AST），是比较高层的 IR 表示类型。

有了完备的 IR，就可以利用不同的 `后端`，编出不同的目标代码，比如 `JVM` 的字节码，或者运行在 `iOS` 的机器码，这样就达到了跨端的目的，想了解更多 kotlin 跨端技术可以参考 https://kotlinlang.org/docs/multiplatform-mobile-getting-started.html

### 如何查看 Kotlin IR 结构

#### IR 树示例

我们先看一个例子，直观感受一下 IR 树是什么样子的

```kotlin 
fun main() {
  println("Hello, World!")
}
```
上述代码在转成 IR 信息后，是这样的

```

MODULE_FRAGMENT name:<main>
  FILE fqName:<root> fileName:/var/folders/lt/k622ndqs14l7_tcxst93z3cm0000gp/T/Kotlin-Compilation7335327567848552666/sources/main.kt
    FUN name:main visibility:public modality:FINAL <> () returnType:kotlin.Unit
      BLOCK_BODY
        CALL 'public final fun println (message: kotlin.Any?): kotlin.Unit [inline] declared in kotlin.io.ConsoleKt' type=kotlin.Unit origin=null
          message: CONST String type=kotlin.String value="Hello, World!"
```

#### IrElement 和 IrElementVisitor


![](./visitor_1.png)

查看 IR 的 Api 中，最重要的是 `IrElement` 和 `IrElementVisitor`

`IrElement` 是所有 IR 元素的父类，包含两个抽象方法
```kotlin
fun <R, D> accept(visitor: IrElementVisitor<R, D>, data: D): R
fun <D> acceptChildren(visitor: IrElementVisitor<Unit, D>, data: D): Unit
```
accept 一般会调用 visitor 对应的 visitXXX 方法
acceptChildren 会依次调用 IR 子元素的 accept 方法

下面看看 `IrClass` 的具体实现，便于我们理解
```kotlin
override fun <R, D> accept(visitor: IrElementVisitor<R, D>, data: D): R =
    visitor.visitClass(this, data) // 调用 visitor 对应的 visitClass

override fun <D> acceptChildren(visitor: IrElementVisitor<Unit, D>, data: D) { // 遍历 IrClass 子元素，并调用对应的 accept 方法
    thisReceiver?.accept(visitor, data)
    typeParameters.forEach { it.accept(visitor, data) }
    declarations.forEach { it.accept(visitor, data) }
}
```

##### <D,R> 输入和输出

我们注意到 Visitor 中还包含了 `<R, D>` 两个泛型参数，D 作为参数传入，R 作为返回值，这分别代表什么意思呢？

`D data` 表示在 visit ir 时的输入数据，我们可以把想要传递的上下文信息通过这个参数传入，在具体 visit 的过程中使用。

比如，我们可以修改打印 IR 元素前的缩进，或是随便插入什么其他信息都可以
```kotlin
class StringIndentVisitor : IrElementVisitor<Unit, String> {
  override fun visitElement(element: IrElement, data: String) {
    println("$data${render(element)} {")
    element.acceptChildren(this, "  $data")
    println("$data}")
  }
}
```

`R` 表示在 accept visitor 时拿到的返回结果，在较少的 Transform ir 场景我们会用到这个能力，其他情况使用 Unit 即可。下面有个真实场景不会用到的例子，但能说明返回值的用法。

```kotlin
// Not as efficient as a while loop, but exemplifies how the output type could be used
class RootParentVisitor : IrElementVisitor<IrDeclarationParent?, Nothing?> {
  override fun visitElement(element: IrElement, data: Nothing?): IrDeclarationParent? = null

  override fun visitDeclaration(declaration: IrDeclarationBase, data: Nothing?): IrDeclarationParent {
    val parent = declaration.parent
    return parent.accept(this, null) ?: parent
  }
}
```

另外。我们可以注意到 `acceptChildren` 接收的 visitor 泛型是 <D, Unit> ，也就是说我们不能在通过 acceptChildren 遍历 IR 树的过程中获取返回值，如果你需要在过程中保存一些信息，可以从外部传入一个变量保存。比如

```kotlin
class CollectingVisitor(
  private val elements: MutableList<IrElement>
) : IrElementVisitor<Unit, Nothing?> {
  override fun visitElement(element: IrElement, data: Nothing?) {
    elements.add(element)
    element.acceptChildren(this, data)
  }
}

fun collect(element: IrElement) = buildList<IrElement> {
  element.accept(CollectingVisitor(this), null)
}
```

##### 

### 自顶向下遍历 IR 树

![](./visitor_2.png)

了解了 IrElement 和 IrElementVisitor 后，我们可以自己实现一个类似 `IrElement.dump()` 的功能。通过重写 visitElement，在其中进行 acceptChildren，就能自顶向下递归遍历 IR 树，通过 `element.render()` 打印每个 IR 元素，代码如下

```kotlin
class RecursiveVisitor: IrElementVisitor<Unit, Nothing?> {
  override fun visitElement(element: IrElement, data: Nothing?) {
    println("visitElement:: ${element.render()}")
    element.acceptChildren(this, data)
  }
}
```


### 其他 IR 操作 API

#### 创建方法调用 IR

```kotlin
// 通过传入方法全限定名找到具体方法 ir，除了方法还有找 class 等 api
val printlnFunc = irPluginContext.referenceFunctions(FqName("kotlin.io.println")).single {
  val parameters = it.owner.valueParameters
  parameters.size == 1 && parameters[0].type == irPluginContext.irBuiltIns.anyNType
}

//通过传入 IrFunction 到 irCall 完成方法的调用，其中 putValueArgument 可以向目标函数传入参数
irCall(printlnFunc).also {
     it.putValueArgument(0, irString("Hello, World"))
}
```

#### 拼接字符串

```kotlin
val concat = irConcat() 
concat.addArgument(irString("a")) 
concat.addArgument(irString("b"))
```

#### 创建局部变量 IR，获取变量值

```kotlin
 val i = irTemporary(irConcat().also { // i = "Hello, World"
        it.addArgument(irString("Hello, World"))
      })

 irGet(i) // 得到 i 的值 "Hello, World"
```

#### 修改方法体

```kotlin
DeclarationIrBuilder(irPluginContext, irFunction.symbol).irBlockBody {
    +irCall(...) // 可通过加号 插入自己的方法调用
    for(statement in irBody.statements) { //原有方法体中的表达式
          +statement
    }
}
```

#### 持续补充...


### 参考

[Writing Your Second Kotlin Compiler Plugin, Part 2 — Inspecting Kotlin IR](https://blog.bnorm.dev/writing-your-second-compiler-plugin-part-2)

[Writing Your Second Kotlin Compiler Plugin, Part 3 — Navigating Kotlin IR](https://blog.bnorm.dev/writing-your-second-compiler-plugin-part-3)