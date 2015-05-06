Basic Types 基本类型
====

Introduction 介绍
----

For programs to be useful, we need to be able to work with some of the simplest units of data: numbers, strings, structures, boolean values, and the like. In TypeScript, we support much the same types as you would expected in JavaScript, with a convenient enumeration type thrown in to help things along.

为了让程序更有用，我们需要能够处理一些基本数据：数字、字符串、结构、布尔值等。在 TypeScript 中，我们提供了你希望 JavaScript 中拥有的一切类型，包括一个方便的枚举类型。

Boolean 布尔值
----

The most basic datatype is the simple true/false value, which JavaScript and TypeScript (as well as other languages) call a `boolean` value.

最基本的数据类型是真/假值，它们在 JavaScript 和 TypeScript （以及其他语言）中称作 `boolean` （布尔值）。

```ts
var isDone: boolean = false;
```

Number 数字
----

As in JavaScript, all numbers in TypeScript are floating point values. These floating point numbers get the type `number`.

与 JavaScript 一样，TypeScript 中的数字都是浮点数，它们的数据类型是 `number` （数字）。

```ts
var height: number = 6;
```

String 字符串
----

Another fundamental part of creating programs in JavaScript for webpages and servers alike is working with textual data. As in other languages, we use the type `string` to refer to these textual datatypes. Just like JavaScript, TypeScript also uses the double quote (") or single quote (') to surround string data.

处理文本数据是使用 JavaScript 创建网页和服务器程序的一个基础功能。与其他语言相同，我们使用 `string` （字符串）表示这些文本数据。和 JavaScript 一样，你也可以在 TypeScript 中使用单引号(')或双引号(")来包围字符串数据。

```ts
var name: string = "bob";
name = 'smith';
```

Array 数组
----

TypeScript, like JavaScript, allows you to work with arrays of values. Array types can be written in one of two ways. In the first, you use the type of the elements followed by `[]` to denote an array of that element type:

和 JavaScript 一样，TypeScript 允许你操作值的队列（数组）。数组类型有两种编写方式，你可以在元素类型后使用 `[]` 来表示这个数据类型的数组：

```ts
var list:number[] = [1, 2, 3];
```

The second way uses a generic array type, Array\<elemType>:

第二种方式是，Array\<元素类型>：

```ts
var list:Array<number> = [1, 2, 3];
```

Enum 枚举
----

A helpful addition to the standard set of datatypes from JavaScript is the 'enum'. Like languages like C#, an enum is a way of giving more friendly names to sets of numeric values.

我们还在 JavaScript 标准的数据类型外添加了 `enum` （枚举）类型。像 C# 等语言一样，我们可以用枚举数据为一系列的数值添加一个更友好的名字。

```ts
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

By default, enums begin numbering their members starting at 0. You can change this by manually setting the value of one its members. For example, we can start the previous example at 1 instead of 0:

默认的，枚举数据会从 0 开始为它的成员编号（0， 1， 2...）。你也可以手动指定一个成员的值来改变这种默认行为。例如，我们可以让上面例子的编号从 1 开始：

```ts
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

Or, even manually set all the values in the enum:

或者，也可以设置每一个成员的值：

```ts
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

A handy feature of enums is that you can also go from a numeric value to the name of that value in the enum. For example, if we had the value 2 but weren't sure which that mapped to in the Color enum above, we could look up the corresponding name:

枚举数据中一个方便的特点是可以从数值访问到该值的名称。例如，我们有一个值 2，但不确定它对应上面 Color 中的哪一个颜色，就可以查找它对应的名字：

```ts
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);
```

Any 任意
----

We may need to describe the type of variables that we may not know when we are writing the application. These values may come from dynamic content, eg from the user or 3rd party library. In these cases, we want to opt-out of type-checking and let the values pass through compile-time checks. To do so, we label these with the `any` type:

当我们编写应用时，很可能需要描述不知道类型的变量。这些值可能来自动态内容，如用户或第三方库。这时，我们想要取消类型检查，并让这些值通过编译时的检查。为了达到这个目的，我们将这些值标记为 `any` （任意）类型：

```ts
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay, definitely a boolean
```

The `any` type is a powerful way to work with existing JavaScript, allowing you to gradually opt-in and opt-out of type-checking during compilation.

`any` 类型对于处理现有的 JavaScript 代码很有用，让你可以逐步支持编译时的类型检测。

The `any` type is also handy if you know some part of the type, but perhaps not all of it. For example, you may have an array but the array has a mix of different types:

当你只知道一部分数据的类型，而不是全部的时候，`any` 也会很有用。例如，你可能有一个数组，但数组的成员有不同的数据类型：

```ts
var list:any[] = [1, true, "free"];

list[1] = 100;
```

Void 空
----

Perhaps the opposite in some ways to `any` is 'void', the absence of having any type at all. You may commonly see this as the return type of functions that do not return a value:

与 `any` 相反的类型应该是 `void`，没有任何类型。一般会作为不返回任何数据的函数的返回值类型。

```ts
function warnUser(): void {
    alert("This is my warning message");
}
```
