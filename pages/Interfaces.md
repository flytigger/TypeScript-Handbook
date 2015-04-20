Interfaces 接口
====

Introduction 介绍
----

One of TypeScript's core principles is that type-checking focuses on the `shape` that values have. This is sometimes called "duck typing" or "structural subtyping". In TypeScript, interfaces fill the role of naming these types, and are a powerful way of defining contracts within your code as well as contracts with code outside of your project. 

TypeScript 的一个核心原则是类型检查着眼于值的结构。这种原则有时被称作 “[鸭子类型](https://zh.wikipedia.org/wiki/%E9%B8%AD%E5%AD%90%E7%B1%BB%E5%9E%8B)” 或 “structural subtyping”。在 TypeScript 中，接口承担了命名这些类型的任务，它也是一个很有力地定义私有代码和外部代码结构的方式。

Our First Interface 第一个接口
----

The easiest way to see how interfaces work is to start with a simple example:

了解接口如何工作的最简单方式就是编写一个示例：

```ts
function printLabel(labelledObj: {label: string}) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

The type-checker checks the call to `printLabel`. The `printLabel` function has a single parameter that requires that the object passed in has a property called `label` of type string. Notice that our object actually has more properties than this, but the compiler only checks to that _at least_ the ones required are present and match the types required. 

类型检测机制会检测 `printLabel` 函数的调用。`printLabel` 函数有一个参数，该参数要求传入的对象有一个字符串类型的 `label` 属性。可以看到传入的对象拥有更多的属性，不过编译器 _只会_ 检测需要的那个属性是否存在，以及它的类型是否匹配。

We can write the same example again, this time using an interface to describe the requirement of having the `label` property that is a string:

我们再来重写一下上面的代码，使用一个接口来描述需要一个字符串类型的 `label` 属性：

```ts
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

The interface `LabelledValue` is a name we can now use to describe the requirement in the previous example. It still represents having a single property called `label` that is of type string. Notice we didn't have to explicitly say that the object we pass to `printLabel` implements this interface like we might have to in other languages. Here, it's only the shape that matters. If the object we pass to the function meets the requirements listed, then it's allowed.

`LabelledValue` 是接口名称，我们可以用它来描述上面例子的参数需求。它代表了拥有一个字符串格式的 `label` 属性。这里只要求它的结构类似，并不需要像其他语言中那样，要求传给 `printLabel` 的对象必须实现自这个接口。如果传给函数的对象符合需求列表，那它就是被允许的。

It's worth pointing out that the type-checker does not require that these properties come in any sort of order, only that the properties the interface requires are present and have the required type.

需要指出的是，类型检测机制并不要求属性的顺序，只要接口要求的属性存在，并且拥有正确的类型即可。

Optional Properties 可选属性
----

Not all properties of an interface may be required. Some exist under certain conditions or may not be there at all. These optional properties are popular when creating patterns like "option bags" where the user passes an object to a function that only has a couple properties filled in.

有些情况下，不是所有属性都必须存在。有些在特定的情况下出现，甚至不会出现。当创建类似 "option bags" 的规则时，用户只需要给函数传入一个包含部分属性的对象即可，这个时候可选属性比较重要。

Here's as example of this pattern:

下面是这种规则的一个例子：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

Interfaces with optional properties are written similar to other interfaces, which each optional property denoted with a '?' as part of the property declaration. 

包含可选属性的接口和其它接口类似，只需要将 `?` 作为属性声明的一部分就可以了。

The advantage of optional properties is that you can describe these possibly available properties while still also catching properties that you know are not expected to be available. For example, had we mistyped the name of the property we passed to `createSquare`, we would get an error message letting us know:

可选属性的一个优点是，你可以描述一个可能存在的属性（属性名和值的类型）。例如，如果我们写错了传入 `createSquare` 的属性名称，我们会得到一个错误提示：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // Type-checker can catch the mistyped name here
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});  
```

Function Types 函数类型
----

Interfaces are capable of describing the wide range of shapes that JavaScript objects can take. In addition to describing an object with properties, interfaces are also capable of describing function types.

接口可以用来描述 JavaScript 对象的许多信息。除了可以用来描述对象的属性，也可以用来描述函数的类型。

To describe a function type with an interface, we give the interface a call signature. This is like a function declaration with only the parameter list and return type given.

在接口中，我们使用调用签名来描述函数类型。它就像一个只包含参数列表和返回值类型的函数声明。

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

Once defined, we can use this function type interface like we would other interfaces. Here, we show how you can create a variable of a function type and assign it a function value of the same type.

定义好之后，我们就可以像其他接口一样来使用函数类型接口了。下面的例子展示了如何创建一个函数类型的变量，并将一个同类型的函数赋值给它。

```ts
var mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  var result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

For function types to correctly type-check, the name of the parameters do not need to match. We could have, for example, written the above example like this:

类型检测并不要求参数的名称与函数类型接口中定义的相同。例如，上面的例子也可以这么写：

```ts
var mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  var result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

Function parameters are checked one at a time, with the type in each corresponding parameter position checked against each other. Here, also, the return type of our function expression is implied by the values it returns (here _false_ and _true_). Had the function expression returned numbers or strings, the type-checker would have warned us that return type doesn't match the return type described in the SearchFunc interface.

函数的参数会按照它在接口中对应的定义被挨个检查，也会根据函数的返回值（这里是 _true_ 或 _false_ ）来检查其类型{需要重新翻译}。如果函数返回了数字或字符串，我们会得到函数返回值类型与 SearchFunc 接口中描述的不一致的警告。

Array Types 数组类型
----

Similarly to how we can use interfaces to describe function types, we can also describe array types. Array types have an `index` type that describes the types allowed to index the object, along with the corresponding return type for accessing the index.

除了用来描述函数，我们也可以用接口来描述数组。数组类型包含一个用来描述成员索引的 `index` 类型，也是访问成员索引的返回值。

```ts
interface StringArray {
  [index: number]: string;
}

var myArray: StringArray;
myArray = ["Bob", "Fred"];
```
 
There are two types of supported index types: string and number. It is possible to support both types of index, with the restriction that the type returned from the numeric index must be a subtype of the type returned from the string index.

索引的类型可以有两种：字符串和数字。{暂时无法翻译}要同时支持两种索引类型，必须保证使用数字索引的返回值类型是使用字符串索引的返回值的子类型。

While index signatures are a powerful way to describe the array and `dictionary` pattern, they also enforce that all properties match their return type. In this example, the property does not match the more general index, and the type-checker gives an error:

使用索引签名，不仅可以将数组和字典区分开，也强制所有属性必须符合它们的返回值。在下面这个例子中，这个属性不符合通常的索引类型，就会得到一个错误信息：

```ts
interface Dictionary {
  [index: string]: string;
  length: number;    // error, the type of `length` is not a subtype of the indexer
} 
```

Class Types 类类型
----

###Implementing an interface 实现接口

One of the most common uses of interfaces in languages like C# and Java, that of explicitly enforcing that a class meets a particular contract, is also possible in TypeScript.

在一些语言，如 C# 和 Java 中，使用接口来声明一个类的结构，是非常常见的用法，你也可以在 TypeScript 里这么做。

```ts
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

You can also describe methods in an interface that are implemented in the class, as we do with `setTime` in the below example:

你也可以在接口中描述类中需要实现的方法，就像下面例子中的 `setTime` ：

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

Interfaces describe the public side of the class, rather than both the public and private side. This prohibits you from using them to check that a class also has particular types for the private side of the class instance.

接口可以用来描述类的公有部分，但不能用来描述私有部分。因此，你无法使用接口来检查类中是否包含实例的私有部分。{需再次翻译}

###Difference between static/instance side of class 类的声明与实现之间的不同

When working with classes and interfaces, it helps to keep in mind that a class has _two_ types: the type of the static side and the type of the instance side. You may notice that if you create an interface with a construct signature and try to create a class that implements this interface you get an error:

在处理类和接口时，需要记住类有 _两_ 种：类的声明和实现。你会注意到，当你创建一个包含构造函数的接口，并创建一个类来实现它时，会得到一个错误：

```ts
interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

This is because when a class implements an interface, only the instance side of the class is checked. Since the constructor sits in the static side, it is not included in this check.

这是因为当类实现一个接口时，只有类的实例会被检查。因为构造函数属于声明部分，所以它不会被检查。

Instead, you would need to work with the `static` side of the class directly. In this example, we work with the class directly:

你需要直接处理类的 `静态` 部分。在这个例子中，我们来直接处理这个类：

```ts
interface ClockStatic {
    new (hour: number, minute: number);
}

class Clock  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
```

Extending Interfaces 接口的扩展
----

Like classes, interfaces can extend each other. This handles the task of copying the members of one interface into another, allowing you more freedom in how you separate your interfaces into reusable components.

像类一样，接口也可以相互扩展。它承担了将一个接口的成员复制到另一个接口的任务，使你更容易地将接口分割成可重用的组件。

```ts
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

An interface can extend multiple interfaces, creating a combination of all of the interfaces.

一个接口可以扩展多个接口，创建一个所有接口的组合。

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

Hybrid Types 混合类型
----

As we mentioned earlier, interfaces can describe the rich types present in real world JavaScript. Because of JavaScript's dynamic and flexible nature, you may occasionally encounter an object that works as a combination of some of the types described above. 

像我们前面提到的，接口可以描述 JavaScript 中复杂的数据对象。由于 JavaScript 动态和可扩展的特点，你也会偶尔遇到一个包含了许多上面描述的类型的复合型对象。

One such example is an object that acts as both a function and an object, with additional properties:

一个类似的例子，一个包含了额外属性，表现得即像函数，又像对象的对象。

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

var c: Counter;
c(10);
c.reset();
c.interval = 5.0;
```

When interacting with 3rd-party JavaScript, you may need to use patterns like the above to fully-describe the shape of the type.

当使用第三方 JavaScript 库的时候，你可能需要用上面的格式来全面描述类型的特征。