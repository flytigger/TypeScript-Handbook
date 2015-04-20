Classes 类
====

Introduction 介绍
----

Traditional JavaScript focuses on functions and prototype-based inheritance as the basic means of building up reusable components, but this may feel a bit awkward to programmers more comfortable with an object-oriented approach, where classes inherit functionality and objects are built from these classes. Starting with ECMAScript 6, the next version of JavaScript, JavaScript programmers will be able to build their applications using this object-oriented class-based approach. In TypeScript, we allow developers to use these techniques now, and compile them down to JavaScript that works across all major browsers and platforms, without having to wait for the next version of JavaScript.

传统的 JavaScript 通过函数和基于原型继承的方式来构建可复用组件，但对于习惯了基于类继承和由类创建对象的面向对象语言的开发者来说，这是非常奇怪的做法。从 JavaScript 的下一个版本——ECMAScript 6 开始，JavaScript 开发者就可以使用面向对象的基于类的方式来创建应用了。在 TypeScript 中，开发者无需等待新版本 JavaScript 发布，现在就可以使用这些新技术了。编译器会将代码编译成兼容所有主流浏览器和平台的代码来执行。

Classes 类
----

Let's take a look at a simple class-based example:

我们来看一下一个基于类的例子：

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
```

The syntax should look very familiar if you've used C# or Java before. We declare a new class `Greeter`. This class has three members, a property called `greeting`, a constructor, and a method `greet`. 

你会发现它的语法与你曾经用过的 C# 和 Java 类似。我们声明了一个类 `Greeter`，它有三个成员，一个 `greeting` 属性，一个构造函数，和一个 `greet` 方法。

You'll notice that in the class when we refer to one of the members of the class we prepend 'this.'. This denotes that it's a member access.

你会发现我们通过在类的成员前加上 `this` 来访问它，这代表访问一个成员。

In the last line we construct an instance of the Greeter class using `new`. This calls into the constructor we defined earlier, creating a new object with the Greeter shape, and running the constructor to initialize it.

在最后一行，我们使用 `new` 创建了 Greeter 类的一个实例。这个动作调用了之前定义的构造函数，创建一个与 Greeter 结构相同的对象，并运行构造函数去初始化它。

Inheritance 继承
----

In TypeScript, we can use common object-oriented patterns. Of course, one of the most fundamental patterns in class-based programming is being able to extend existing classes to create new ones using inheritance.

在 TypeScript 中，我们可以使用许多面向对象的方式。当然，其中最基本的一项是通过继承来扩展一个类，创建出新的类。

Let's take a look at an example:

来看一个实例：

```ts
class Animal {
    name:string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number = 0) {
        alert(this.name + " moved " + meters + "m.");
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 5) {
        alert("Slithering...");
        super.move(meters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(meters = 45) {
        alert("Galloping...");
        super.move(meters);
    }
}

var sam = new Snake("Sammy the Python");
var tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
``` 

This example covers quite a bit of the inheritance features in TypeScript that are common to other languages. Here we see using the `extends` keywords to create a subclass. You can see this where `Horse` and `Snake` subclass the base class `Animal` and gain access to its features.

这个实例包含了 TypeScript 中的许多继承功能，这些功能在其他语言中也十分普遍。我们使用关键词 `extends` 来创建一个子类。 `Horse` 和 `Snake` 都是 `Animal` 的子类，可以访问到它的资源。

The example also shows off being able to override methods in the base class with methods that are specialized for the subclass. Here both `Snake` and `Horse` create a `move` method that overrides the `move` from `Animal`, giving it functionality specific to each class.

这个例子也展示了在子类中重写父类中方法。 `Snake` 和 `Horse` 都重写了 `Animal` 的 `move` 方法，让它们有各自特定的功能。

Private/Public modifiers 私有/共有操作符
----

###Public by default 默认为共有

You may have noticed in the above examples we haven't had to use the word `public` to make any of the members of the class visible. Languages like C# require that each member be explicitly labelled `public` to be visible. In TypeScript, each member is public by default. 

你可能已经注意到，在上面的例子中我们没有使用 `public` 来让类的成员可见。其他语言（如 C#）必须使用 `public` 来标记成员可见。在 TypeScript 中，成员默认就是共有的。

You may still mark members a private, so you control what is publicly visible outside of your class. We could have written the `Animal` class from the previous section like so:

你可以将成员标记为私有，来控制其在类外不可见。我们可以将上一节的 `Animal` 类这么写：

```ts
class Animal {
    private name:string;
    constructor(theName: string) { this.name = theName; }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

###Understanding private 理解私有

TypeScript is a structural type system. When we compare two different types, regardless of where they came from, if the types of each member are compatible, then we say the types themselves are compatible. 

TypeScript 是一个结构类型系统。当我们比较两个不同的类型时，不论它们来自哪里，只要它们的成员类型都兼容，我们就认为这两个类型兼容。

When comparing types that have `private` members, we treat these differently. For two types to be considered compatible, if one of them has a private member, then the other must have a private member that originated in the same declaration. 

我们用另一种方式来比较包含私有成员的类型时。对于两个需要比较的类型，如果其中一个包含私有成员，那另一个必须包含具有相同声明的私有成员。

Let's look at an example to better see how this plays out in practice:

让我们通过实例来了解如何实践：

```ts
class Animal {
    private name:string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
	constructor() { super("Rhino"); }
}

class Employee {
    private name:string;
    constructor(theName: string) { this.name = theName; }	
}

var animal = new Animal("Goat");
var rhino = new Rhino();
var employee = new Employee("Bob");

animal = rhino;
animal = employee; // Error: Animal and Employee are not compatible
```

In this example, we have an `Animal` and a `Rhino`, with `Rhino` being a subclass of `Animal`. We also have a new class `Employee` that looks identical to `Animal` in terms of shape. We create some instances of these classes and then try to assign them to each other to see what will happen. Because `Animal` and `Rhino` share the private side of their shape from the same declaration of 'private name: string' in `Animal`, they are compatible. However, this is not the case for `Employee`. When we try to assign from an `Employee` to `Animal` we get an error that these types are not compatible. Even though `Employee` also has a private member called `name`, it is not the same one as the one created in `Animal`. 

在这个例子中，`Rhino` 是 `Animal` 的子类。还有一个与 `Animal` 外观一致的 `Employee` 类。我们创建了每个类的实例，然后尝试让它们相互赋值，看看会有什么发生。因为 `Animal` 和 `Rhino` 拥有相同的在 `Animal` 中声明的私有部分 'private name: string' ，它们是兼容的。但对于 `Employee` 来说又是另一回事，当我们将它赋值给 `Animal` 时，我们得到了类型不兼容的错误。虽然 `Employee` 也有一个叫做 `name` 的私有成员，但并不是 `Animal` 中创建的那个。

###Parameter properties 参数属性

The keywords `public` and `private` also give you a shorthand for creating and initializing members of your class, by creating parameter properties. The properties let you can create and initialize a member in one step. Here's a further revision of the previous example. Notice how we drop `theName` altogether and just use the shortened 'private name: string' parameter on the constructor to create and initialize the `name` member.

关键词 `public` 和 `private` 让你可以使用参数属性快速创建和初始化类成员。下面是之前实例的更先进版本。可以看到我们丢弃了全部 `theName` 声明，直接在构造函数中使用 'private name: string' 作为参赛来创建和声明 `name` 成员。

```ts
class Animal {
    constructor(private name: string) { }
    move(meters: number) {
        alert(this.name + " moved " + meters + "m.");
    }
}
```

Using `private` in this way creates and initializes a private member, and similarly for `public`. 

使用 `provate` 通过这种方式创建和初始化私有成员，`public` 也是如此。

Accessors 存取器
----

TypeScript supports getters/setters as a way of intercepting accesses to a member of an object. This gives you a way of having finer-grained control over how a member is accessed on each object.

TypeScript 支持使用 getters/setters 来拦截对象属性的访问。这给了你精确控制如何访问对象成员的能力。

Let's convert a simple class to use `get` and `set`. First, let's start with an example without getters and setters.

我们可以尝试将类使用 `get` 和 `set` 来编写。在这之前，我们先创建一个没有使用存取器的类。

```ts
class Employee {
    fullName: string;
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

While allowing people to randomly set fullName directly is pretty handy, this might get us in trouble if we people can change names on a whim. 

允许用户偶尔更新一下全名是非常方便的，不过，用户可以随便乱改名字，会给我们带来麻烦。

In this version, we check to make sure the user has a secret passcode available before we allow them to modify the employee. We do this by replacing the direct access to fullName with a `set` that will check the passcode. We add a corresponding `get` to allow the previous example to continue to work seamlessly.

在这个例子中，我们用户是否有密码，来验证是否允许他更新 employee。我们使用 `set` 替换对 fullName 的直接访问，来实现密码检查的功能。我们添加了相应的 `get` 方法，是其与之前的例子表现相同。

```ts
var passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }
	
    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            alert("Error: Unauthorized update of employee!");
        }
    }
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

To prove to ourselves that our accessor is now checking the passcode, we can modify the passcode and see that when it doesn't match we instead get the alert box warning us we don't have access to update the employee.

为了验证存取器正常工作，我们可以修改验证码来查看结果，如果不匹配，我们会得到警告我们没有修改权限的弹窗。

Note: Accessors require you to set the compiler to output ECMAScript 5.

注意：使用存取器，需要设置编译器输出 ECMAScript 5 的代码。

Static Properties 静态属性
----

Up to this point, we've only talked about the _instance_ members of the class, those that show up on the object when its instantiated. We can also create _static_ members of a class, those that are visible on the class itself rather than on the instances. In this example, we use `static` on the origin, as it's a general value for all grids. Each instance accesses this value through prepending the name of the class. Similarly to prepending 'this.' in front of instance accesses, here we prepend 'Grid.' in front of static accesses.

截止到目前，我们只讨论了类的 _实例_ 成员，它们只会在类的实例中出现。我们也可以创建类的 _静态_ 成员，它们只在类中可见，在其实例中不可见。在这个例子中，我们使用 `static` 声明了 origin，使其作为所有 grid 的公共值。每个实例都可以通过类名 `Grid` 作为前缀访问这个值，像使用 `this`  那样。

```ts
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        var xDist = (point.x - Grid.origin.x);
        var yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

var grid1 = new Grid(1.0);  // 1x scale
var grid2 = new Grid(5.0);  // 5x scale

alert(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
alert(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

Advanced Techniques 高级技术
----

###Constructor functions 构造函数

When you declare a class in TypeScript, you are actually creating multiple declarations at the same time. The first is the type of the _instance_ of the class.

当我们在 TypeScript 中声明一个类时，实际上同时完成了多个声明。首先是类实例的类型。

```ts
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter: Greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

Here, when we say 'var greeter: Greeter', we're using Greeter as the type of instances of the class Greeter. This is almost second nature to programmers from other object-oriented languages. 

这里，当我们使用 'var greeter: Greeter' 时，我们指定了 Greeter 类实例的类型为 Greeter。这几乎是开发者第二熟悉的面向对象编程的特点了。
 
We're also creating another value that we call the _constructor function_. This is the function that is called when we `new` up instances of the class. To see what this looks like in practice, let's take a look at the JavaScript created by the above example:

同时，我们创建了一个叫做 _构造函数_ 的值。这个函数在我们使用 `new` 创建类的实例时被调用。为了明白它的用法，我们先看如何使用 JavaScript 构建上面的例子：

```ts
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

var greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

Here, `var Greeter` is going to be assigned the constructor function. When we call `new` and run this function, we get an instance of the class. The constructor function also contains all of the static members of the class. Another way to think of each class is that there is an _instance_ side and a _static_ side.

这里， `var Greeter` 将被用作构造函数。当我们通过 `new` 调用它时，会得到一个它的实例。构造函数也包含了类的所有静态成员。另一个看待类的方式是它具有静态和实例两个部分。

Let's modify the example a bit to show this difference:

我们对这个例子稍加修改，来看看它们的不同：

```ts
class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        }
        else {
            return Greeter.standardGreeting;
        }
    }
}

var greeter1: Greeter;
greeter1 = new Greeter();
alert(greeter1.greet());

var greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";
var greeter2:Greeter = new greeterMaker();
alert(greeter2.greet());
```

In this example, `greeter1` works similarly to before. We instantiate the `Greeter` class, and use this object. This we have seen before.

在这个例子中，`greeter1` 和之前相同。我们创建并使用了 `Greeter` 类的实例，在之前的例子中也是这么做的。

Next, we then use the class directly. Here we create a new variable called `greeterMaker`. This variable will hold the class itself, or said another way its constructor function. Here we use 'typeof Greeter', that is "give me the type of the Greeter class itself" rather than the instance type. Or, more precisely, "give me the type of the symbol called Greeter", which is the type of the constructor function. This type will contain all of the static members of Greeter along with the constructor that creates instances of the Greeter class. We show this by using `new` on `greeterMaker`, creating new instances of `Greeter` and invoking them as before.

{需要翻译}

###Using a class as an interface 将类作为接口使用

As we said in the previous section, a class declaration creates two things: a type representing instances of the class and a constructor function. Because classes create types, you can use them in the same places you would be able to use interfaces.

像我们上一节说的，类声明创建了两个东西：一个代表了类实例的类型，和一个构造函数。因为雷创建了类型，所以你可以在任何使用接口的地方使用类。

```ts
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

var point3d: Point3d = {x: 1, y: 2, z: 3};
```
