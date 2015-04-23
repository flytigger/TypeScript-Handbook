Generics 泛型
====

Introduction 介绍
----

A major part of software engineering is building components that not only have well-defined and consistent APIs, but are also reusable. Components that are capable of working on the data of today as well as the data of tomorrow will give you the most flexible capabilities for building up large software systems.

软件工程的一个主要部分是不仅要创建包含明确定义的和兼容的 API 的组件，也要保证它们可重用。如果组件不仅支持当前的数据，也支持未来的数据，会让你拥有灵活创建大型软件系统的能力。

In languages like C# and Java, one of the main tools in the toolbox for creating reusable components is 'generics', that is, being able to create a component that can work over a variety of types rather than a single one. This allows users to consume these components and use their own types.

在一些编程语言，如 C# 和 Java 中，'泛型' 是用来构建可重用组件的重要工具之一。就是可以创建支持多种数据类型的组件。这允许用户用他们自己的类型来使用这些组件。

Hello World of Generics 泛型的 Hello World
----

To start off, let's do the "hello world" of generics: the identity function. The identity function is a function that will return back whatever is passed in. You can think of this in a similar way to the 'echo' command. 

为了了解泛型，我们来写一个 "hello world"：一个 identify 函数。这个函数会返回任何传入的值，你可以认为它和 'echo' 命令类似。

Without generics, we would either have to give the identity function a specific type:

如果不使用泛型，我们必须给这个 identify 函数制定一个类型：

```ts
function identity(arg: number): number {
    return arg;
}
```

Or, we could describe the identity function using the 'any' type:

或者，我们可以用 'any' 类型来描述这个 identify 函数：

```ts
function identity(arg: any): any {
    return arg;
}
```

While using 'any' is certainly generic in that will accept any and all types for the type of 'arg', we actually are losing the information about what that type was when the function returns. If we passed in a number, the only information we have is that any type could be returned. 

当我们使用 'any' 时，函数允许传入任何类型的值，这就是泛型。不过我们丢失了函数返回值类型的信息。如果我们传入一个数字，函数可能返回任何类型的值。

Instead, we need a way of capturing the type of the argument in such a way that we can also use it to denote what is being returned. Here, we will use a _type variable_, a special kind of variable that works on types rather than values. 

我们需要捕捉参数的类型，这样我们就可以用它来标记函数的返回值类型。这里，我们使用 _类型变量_ ，一个用来处理类型而不是值的变量。

```ts
function identity<T>(arg: T): T {
    return arg;
}
```

We've now added a type variable 'T' to the identity function. This 'T' allows us to capture the type the user provides (eg, number), so that we can use that information later. Here, we use 'T' again as the return type. On inspection, we can now see the same type is used for the argument and the return type. This allows us to traffic that type information in one side of the function and out the other.

我们给 identify 函数添加了一个类型变量 'T'，它允许我们捕捉用户提供的变量类型（如数字），然后我们可以在后面使用这些信息。这里，我们将 'T' 用作函数的返回值类型。我们现在已经将这个类型用作参数和返回值的类型了。这使我们可以将类型信息从函数的一边传递到另一边。

We say that this version of the 'identity' function is generic, as it works over a range of types. Unlike using 'any', it's also just as precise (ie, it doesn't lose any information) as the first 'identity' function that used numbers for the argument and return type.

由于这个版本的 'identify' 函数可以处理多种类型的数据，我们称之为泛型。与使用 'any' 不同，它和之前使用数字作为参数和返回值类型的函数一样精确（即没有丢失任何信息）。

Once we've written the generic identity function, we can call it in one of two ways. The first way is to pass all of the arguments, including the type argument, to the function:

在定义完使用泛型的 identify 函数后，我们可以用两种方式调用它。第一种是我们将所有参数传递给函数，包括类型参数：

```ts
var output = identity<string>("myString");  // type of output will be 'string'
```

Here we explicitly set 'T' to be string as one of the arguments to the function call, denoted using the <> around the arguments rather than ().

这里，我们特别指明 'T' 是字符串类型，作为函数调用的一个参数，要注意使用 <> 包围该参数，而不是 ()。

The second way is also perhaps the most common. Here we use 'type argument inference', that is, we want the compiler to set the value of T for us automatically based on the type of the argument we pass in:

第二种方式应该是最常用的。我们使用了 '参数类型推断' ，让编译器根据传入的参数类型自动设置 T 的值：

```ts
var output = identity("myString");  // type of output will be 'string'
```

Notice that we didn't have explicitly pass the type in the angle brackets (<>), the compiler just looked at the value "myString", and set T to its type. While type argument inference can be a helpful tool to keep code shorter and more readable, you may need to explicitly pass in the type arguments as we did in the previous example when the compiler fails to infer the type, as may happen in more complex examples.

要注意我们没有必要通过尖括号（<>）传入类型，编译器会检查 "myString" 的类型，并将其设置到 T。虽然参数类型推断是一个保持代码简洁可读的有用的工具，当编译器无法获取类型时（在某些更复杂的情况下），你仍然需要像之前的例子一样明确的传入一个类型。

Working with Generic Type Variables 使用泛型的类型变量
----

When you begin to use generics, you'll notice that when you create generic functions like 'identity', the compiler will enforce that you use any generically typed parameters in the body of the function correctly. That is, that you actually treat these parameters as if they could be any and all types.

在你刚开始使用泛型的时候，你会发现在你创建了类似 'identity' 的泛型函数时，不论你在函数体中使用哪种类型的参数，编译器都会正确执行。这是因为你自己认为参数可能是任何类型，并用这种方式来处理他。

Let's take our 'identity' function from earlier:

再来看一下之前的 'identify' 函数：

```ts
function identity<T>(arg: T): T {
    return arg;
}
```

What if want to also log the length of the argument 'arg' to the console with each call. We might be tempted to write this:

如果我们想在每次调用时都打印 'arg' 参数的长度，我们可能会这么写：

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

When we do, the compiler will give us an error that we're using the ".length" member of 'arg', but nowhere have we said that 'arg' has this member. Remember, we said earlier that these type variables stand in for any and all types, so someone using this function could have passed in a 'number' instead, which does not have a ".length" member. 

当我们这么做时，编译器会告诉我们 'arg' 没有 ".length" 成员。要记住，类型变量代表了任何类型，所以当某些用户传入一个 '数字' 来使用这个函数时，它就没有 ".length" 成员。

Let's say that we've actually intended this function to work on arrays of T rather that T directly. Since we're working with arrays, the .length member should be available. We can describe this just like we would create arrays of other types:

假设这个函数是用来处理 T 的数组，而非 T 的值。既然是数组，.length 成员一定是可用的。我们可以用类似创建数组类型的方式来描述它：

```ts
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

You can read the type of logging Identity as "the generic function loggingIdentity, takes a type parameter T, and an argument 'arg' which is an array of these T's, and returns an array of T's. If we passed in an array of numbers, we'd get an array of numbers back out, as T would bind to number. This allows us to use our generic type variable 'T' as part of the types we're working with, rather than the whole type, giving us greater flexibility. 

{暂时无法翻译}

We can alternatively write the sample example this way:

我们也可以这么编写同一个例子：

```ts
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

You may already be familiar with this style of type from other languages. In the next section, we'll cover how you can create your own generic types like Array<T>.

你或许已经从其它语言中熟悉了这种类型的风格。在下一节，我们将会讲到如何创建自己的泛型类型。

Generic Types 泛型类型
----

In previous sections, we created generic identity functions that worked over a range of types. In this section, we'll explore the type of the functions themselves and how to create generic interfaces.

在上一节，我们创建了支持各种类型数据的泛型 identity 函数。在这一节，我们会探索函数本身的类型，以及如何创建泛型接口。

The type of generic functions is just like those of non-generic functions, with the type parameters listed first, similarly to function declarations:

泛型函数的类型和非泛型函数的类型类似，参数类型列表在前面，和函数声明一样：

```ts
function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: <T>(arg: T)=>T = identity;
```

We could also have used a different name for the generic type parameter in the type, so long as the number of type variables and how the type variables are used line up.

我们也可以为泛型类型参数使用一个不同的名字，只要列出所有的参数变量即可。

```ts
function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: <U>(arg: U)=>U = identity;
```

We can also write the generic type as a call signature of an object literal type:

我们也可以将泛型类型写成对象文本类型的调用签名：

```ts
function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: {<T>(arg: T): T} = identity;
```

Which leads us to writing our first generic interface. Let's take the object literal from the previous example and move it to an interface:

我们开始写第一个泛型接口。我们将上面例子中的对象文本移动到接口中：

```ts
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: GenericIdentityFn = identity;
```

In a similar example, we may want to move the generic parameter to be a parameter of the whole interface. This lets us see what type(s) we're generic over (eg Dictionary<string> rather than just Dictionary). This makes the type parameter visible to all the other members of the interface. 

```ts
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: GenericIdentityFn<number> = identity;
```

Notice that our example has changed to be something slightly different. Instead of describing a generic function, we now have a non-generic function signature that is a part of a generic type. When we use GenericIdentityFn, we now will also need to specify the corresponding type argument (here: number), effectively locking in what the underlying call signature will use. Understanding when to put the type parameter directly on the call signature and when to put it on the interface itself will be helpful in describing what aspects of a type are generic.

In addition to generic interfaces, we can also create generic classes. Note that it is not possible to create generic enums and modules.

Generic Classes
----

A generic class has a similar shape to a generic interface. Generic classes have a generic type parameter list in angle brackets (<>) following the name of the class.

```ts
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

var myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

This is a pretty literal use of the 'GenericNumber' class, but you may have noticed that nothing is restricting is to only use the 'number' type. We could have instead used 'string' or even more complex objects.

```ts
var stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function(x, y) { return x + y; };

alert(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

Just as with interface, putting the type parameter on the class itself lets us make sure all of the properties of the class are working with the same type.

As we covered in [Classes|Classes in TypeScript], a class has two side to its type: the static side and the instance side. Generic classes are only generic over their instance side rather than their static side, so when working with classes, static members can not use the class's type parameter.

Generic Constraints
----

If you remember from an earlier example, you may sometimes want to write a generic function that works on a set of types where you have some knowledge about what capabilities that set of types will have. In our 'loggingIdentity' example, we wanted to be able access the ".length" property of 'arg', but the compiler could not prove that every type had a ".length" property, so it warns us that we can't make this assumption.

```ts
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

Instead of working with any and all types, we'd like to constrain this function to work with any and all types that also have the ".length" property. As long as the type has this member, we'll allow it, but it's required to have at least this member. To do so, we must list our requirement as a constraint on what T can be.

To do so, we'll create an interface that describes our constraint. Here, we'll create an interface that has a single ".length" property and then we'll use this interface and the {{extends}} keyword to denote our constraint:

```ts
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

Because the generic function is now constrained, it will no longer work over any and all types:

```ts
loggingIdentity(3);  // Error, number doesn't have a .length property
```

Instead, we need to pass in values whose type has all the required properties:

```ts
loggingIdentity({length: 10, value: 3});  
```

###Using Type Parameters in Generic Constraints

In some cases, it may be useful to declare a type parameter that is constrained by another type parameter. For example,

```ts
function find<T, U extends Findable<T>>(n: T, s: U) {   // errors because type parameter used in constraint
  // ...
} 
find (giraffe, myAnimals);
```

You can achieve the pattern above by replacing the type parameter with its constraint. Rewriting the example above,

```ts
function find<T>(n: T, s: Findable<T>) {   
  // ...
} 
find(giraffe, myAnimals);
```

_Note:_ The above is not strictly identical, as the return type of the first function could have returned 'U', which the second function pattern does not provide a means to do.

###Using Class Types in Generics

When creating factories in TypeScript using generics, it is necessary to refer to class types by their constructor functions. For example,

```ts
function create<T>(c: {new(): T; }): T { 
    return new c();
}
```

A more advanced example uses the prototype property to infer and constrain relationships between the constructor function and the instance side of class types.

```ts
class BeeKeeper {
    hasMask: boolean;
}

class ZooKeeper {
    nametag: string; 
}

class Animal {
    numLegs: number;
}

class Bee extends Animal {
    keeper: BeeKeeper;
}

class Lion extends Animal {
    keeper: ZooKeeper;
}

function findKeeper<A extends Animal, K> (a: {new(): A; 
    prototype: {keeper: K}}): K {

    return a.prototype.keeper;
}

findKeeper(Lion).nametag;  // typechecks!
```
