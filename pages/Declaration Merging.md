Declaration Merging 声明合并
====

Introduction 介绍
----

Some of the unique concepts in TypeScript come from the need to describe what is happening to the shape of JavaScript objects at the type level. One example that is especially unique to TypeScript is the concept of 'declaration merging'. Understanding this concept will give you an advantage when working with existing JavaScript in your TypeScript. It also opens the door to more advanced abstraction concepts.

TypeScript 中的一些独特概念，来自对于 JavaScript 对象类型层面的变化进行描述的需求。声明合并是这些概念中最为独特的一个。理解这个概念，将会让你更好的在 TypeScript 中应用已经存在的 JavaScript。这也敞开了通往更高级的抽象概念的大门。

First, before we get into how declarations merge, let's first describe what we mean by 'declaration merging'.

首先，在我们学习如何合并声明之前，我们先搞明白 '声明合并' 的含义。

For the purposes of this article, declaration merging specifically means that the compiler is doing the work of merging two separate declarations declared with the same name into a single definition. This merged definition has the features of both of the original declarations. Declaration merging is not limited to just two declarations, as any number of declarations can be merged. 

在本文中，'声明合并' 特指编译器自动将两个同名的声明合并成一个。合并后的定义包含了所有原始的定义。声明合并并不限制声明的数量。

Basic Concepts 基本概念
----

In TypeScript, a declaration exists in one of three groups: namespace/module, type, or value. Declarations that create a namespace/module are accessed using a dotted notation when writing a type. Declarations that create a type do just that, create a type that is visible with the declared shape and bound to the given name. Lastly, declarations create a value are those that are visible in the output JavaScript (eg, functions and variables).

在 TypeScript 中，声明可以有三种形式：namespace/module, type, 或 value。创建命名空间或模块的声明，在书写一个类型的时候需要通过 '.''来访问。{无法翻译}

|| Declaration Type || Namespace || Type || Value ||
| Module | X | | X |
| Class | | X | X |
| Interface | | X | |
| Function | | | X |
| Variable | | | X |

Understanding what is created with each declaration will help you understand what is merged when you perform a declaration merge.

理解每种声明创建了什么，有助于了解声明合并中被合并的是什么。

Merging Interfaces 合并接口
----

The simplest, and perhaps most common, type of declaration merging is interface merging. At the most basic level, the merge mechanically joins the members of both declarations into a single interface with the same name.

最简单，或许也是最常见的声明合并是接口的合并。简单来讲，这种合并只是机械地讲两个同名声明的成员合并到一个中。

```ts
interface Box {
    height: number;
    width: number;
}

interface Box {
    scale: number;
}

var box: Box = {height: 5, width: 6, scale: 10};
```

Non-function members of the interfaces must be unique. The compiler will issue an error if the interfaces both declare a non-function member of the same name.

接口的非函数成员必须是唯一的。如果接口中定义了同名的非函数成员，编译器会发出错误提示。

For function members, each function member of the same name is treated as describing an overload of the same function. Of note, too, is that in the case of interface A merging with later interface A (here called A'), the overload set of A' will have a higher precedence than that of interface A. 

对于函数成员来说，每个同名的函数都被当做该函数的重载。值得注意的是，当接口 A 和 A' 合并的时候，A' 中的重载函数有更高的优先权。

That is, in the example:

来看这个例子：

```ts
interface Document {
    createElement(tagName: any): Element;
}
interface Document {
    createElement(tagName: string): HTMLElement;
}
interface Document {
    createElement(tagName: "div"): HTMLDivElement; 
    createElement(tagName: "span"): HTMLSpanElement;
    createElement(tagName: "canvas"): HTMLCanvasElement;
}
```

The two interfaces will merge to create a single declaration. Notice that the elements of each group maintains the same order, just the groups themselves are merged with later overload sets coming first:

两个接口会合并成一个声明。注意，后被合并的成员会排在前面：

```ts
interface Document {
    createElement(tagName: "div"): HTMLDivElement; 
    createElement(tagName: "span"): HTMLSpanElement;
    createElement(tagName: "canvas"): HTMLCanvasElement;
    createElement(tagName: string): HTMLElement;
    createElement(tagName: any): Element;
}
```

Merging Modules 合并模块
----

Similarly to interfaces, modules of the same name will also merge their members. Since modules create both a namespace and a value, we need to understand how both merge.

和接口类似，同名模块的成员也会合并。由于模块创建了命名空间和值，我们需要分别了解两种值的合并方式。

To merge the namespaces, type definitions from exported interfaces declared in each module are themselves merged, forming a single namespace with merged interface definitions inside.

为了合并命名空间，导出的接口会先在各自模块中合并。

To merge the value, at each declaration site, if a module already exists with the given name, it is further extended by taking the existing module and adding the exported members of the second module to the first.

为了合并值，在每个声明区域中，如果已经存在同名的模块，则先将第二个模块导出的成员合并到第一个模块中。

The declaration merge of 'Animals' in this example:

该例子中 'Animal' 的声明合并：

```ts
module Animals {
    export class Zebra { }
}

module Animals {
    export interface Legged { numberOfLegs: number; }
    export class Dog { }
}
```

is equivalent to:

等同于：

```ts
module Animals {
    export interface Legged { numberOfLegs: number; }
    
    export class Zebra { }
    export class Dog { }
}
```

This model of module merging is a helpful starting place, but to get a more complete picture we need to also understand what happens with non-exported members. on-exported members are only visible in the original (un-merged) module. This means that after merging, merged members that came from other declarations can not see non-exported members.

模块合并的模式是一个不错的起点，不过为了更深入的了解，我们也需要知道未导出的成员发生了什么。未导出的成员只在原始模块中可见，所以在合并后，来自其他模块的成员不应该看到这些未导出成员。

We can see this more clearly in this example:

我们可以在这个例子中更清晰的看到：

```ts
module Animal {
    var haveMuscles = true;

    export function animalsHaveMuscles() {
        return haveMuscles;
    }
}

module Animal {
    export function doAnimalsHaveMuscles() {
        return haveMuscles;  // <-- error, haveMuscles is not visible here
    }
}
```

Because 'haveMuscles' is not exported, only the 'animalsHaveMuscles' function that shares the same un-merged module can see the symbol. The 'doAnimalsHaveMuscles' function, even though it's part of the merged Animal module can not see this un-exported member.

由于 'haveMuscles' 未导出，只有 'animalsHaveMuscles' 函数可以访问同一个未合并模块的值。虽然 'doAnimalsHaveMuscles' 也是合并后模块的成员，但它无法访问该未导出值。

Merging Modules with Classes, Functions, and Enums 将模块与类、函数和枚举进行合并
----

Modules are flexible enough to also merge with other types of declarations. To do so, the module declaration must follow the declaration it will merge with. The resulting declaration has properties of both declaration types. TypeScript uses this capability to model some of patterns in JavaScript as well as other programming languages.

模块也可以与其他类型的声明进行合并。{无法翻译}

The first module merge we'll cover is merging a module with a class. This gives the user a way of describing inner classes.

第一个模块合并展示了模块和类的合并，这可以用来描述内部类。

```ts
class Album {
    label: Album.AlbumLabel;
}
module Album {
    export class AlbumLabel { }
}
```

The visibility rules for merged members is the same as described in the 'Merging Modules' section, so we must export the AlbumLabel class for the merged class to see it. The end result is a class managed inside of another class. You can also use modules to add more static members to an existing class.

{}

In addition to the pattern of inner classes, you may also be familiar with JavaScript practice of creating a function and then extending the function further by adding properties onto the function. TypeScript uses declaration merging to build up definitions like this in a type-safe way.

{}

```ts
function buildLabel(name: string): string {
    return buildLabel.prefix + name + buildLabel.suffix;
}

module buildLabel {
    export var suffix = "";
    export var prefix = "Hello, ";
}

alert(buildLabel("Sam Smith"));
```

Similarly, modules can be used to extend enums with static members:

类似的，模块可以用来扩展枚举中的静态成员：

```ts
enum Color {
    red = 1,
    green = 2,
    blue = 4
}

module Color {
    export function mixColor(colorName: string) {
        if (colorName == "yellow") {
            return Color.red + Color.green;
        }
        else if (colorName == "white") {
            return Color.red + Color.green + Color.blue;
        }
        else if (colorName == "magenta") {
            return Color.red + Color.blue;
        }
        else if (colorName == "cyan") {
            return Color.green + Color.blue;
        }
    }
}
```

Disallowed Merges 禁止的合并
----

Not all merges are allowed in TypeScript. Currently, classes can not merge with other classes, variables and classes can not merge, nor can interfaces and classes. For information on mimicking classes merging, see the [Mixins in TypeScript] section.

并非所有 TypeScript 中的合并都可以进行。目前，类之间不可以合并，变量和类不可以合并，接口和类也不可以合并。可以从 [Mixins in TypeScript] 一节中获得更多关于模仿类合并的信息。
