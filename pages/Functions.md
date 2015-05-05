Functions 函数
====

Introduction 介绍
----

Functions are the fundamental building block of any applications in JavaScript. They're how you build up layers of abstraction, mimicking classes, information hiding, and modules. In TypeScript, while there are classes and modules, function still play the key role in describing how to `do` things. TypeScript also adds some new capabilities to the standard JavaScript functions to make them easier to work with.

函数是所以 JavaScript 应用中的基本构建模块。你可以用它构建抽象层、模拟类、信息隐藏和模块。在 TypeScript 中，虽然已经有了类和模块，函数仍是描述如何 `做` 的关键角色。TypeScript 给标准的 JavaScript 函数增加了一些新能力，使它用起来更简单。

Functions 函数
----

To begin, just as in JavaScript, TypeScript functions can be created both as a named function or as an anonymous function. This allows you to choose the most appropriate approach for your application, whether you're building a list of functions in an API or a one-off function to hand off to another function.

像 JavaScript 中一样，TypeScript 中的函数也可以用命名函数或匿名函数的方式创建。这让你可以选择应用中最合适的方式，不论你要创建 API 中的一系列函数，还是交给其他函数使用的一次性函数。

To quickly recap what these two approaches look like in JavaScript:

来看一下这两种方式在 JavaScript 中是什么样的：

```js
// Named function
function add(x, y) {
    return x+y;
}

// Anonymous function
var myAdd = function(x, y) { return x+y; };
```

Just as in JavaScript, functions can return to variables outside of the function body. When they do so, they're said to `capture` these variables. While understanding how this works, and the trade-offs when using this technique, are outside of the scope of this article, having a firm understanding how this mechanic is an important piece of working with JavaScript and TypeScript. 

像 JavaScript 中一样，函数可以访问函数外的变量，我们称之为 `捕获` 变量。这篇文章中，我们不会探讨它的细节，以及何时该使用这种技术。不过深入了解这种机制对于使用 JavaScript 和 TypeScript 十分重要。

```ts
var z = 100;

function addToZ(x, y) {
    return x+y+z;
}
```

Function Types 函数类型
----

###Typing the function 为函数添加类型

Let's add types to our simple examples from earlier:

我们为之前的例子添加类型：

```ts
function add(x: number, y: number): number {
    return x+y;
}

var myAdd = function(x: number, y: number): number { return x+y; };
```

We can add types to each of the parameters and then to the function itself to add a return type. TypeScript can figure the return type out by looking at the return statements, so we can also optionally leave this off in many cases.

我们可以为每个参数添加类型，并为函数添加返回值类型。TypeScript 会通过 return 声明来检测返回值的类型，我们也可以在某些情况下省去添加返回值类型的步骤。

###Writing the function type 编写函数类型

Now that we've typed the function, let's write the full type of the function out by looking at the each piece of the function type. 

我们已经为函数添加了类型，{暂时无法翻译}我们来逐个部分编写函数类型。

```ts
var myAdd: (x:number, y:number)=>number = 
    function(x: number, y: number): number { return x+y; };
```

A function's type has the same two parts: the type of the arguments and the return type. When writing out the whole function type, both parts are required. We write out the parameter types just like a parameter list, giving each parameter a name and a type. This name is just to help with readability. We could have instead written:

函数类型也包含两部分：参数类型和返回值类型。当编写整个函数类型时，两个部分都是必须的。参数类型的编写方式和参数列表类似，给每一个参数名称和类型。名称只是用来增加可读性。我们也可以这么写：

```ts
var myAdd: (baseValue:number, increment:number)=>number = 
    function(x: number, y: number): number { return x+y; };
```

As long as the parameter types line up, it's considered a valid type for the function, regardless of the names you give the parameters in the function type. 

当启用参数类型时，它被当做函数中一个可用的类型，不论你在函数类型中写的参数名是什么。

The second part is the return type. We make it clear which is the return type by using a fat arrow (=>) between the parameters and the return type. As mentioned before, this is a required part of the function type, so if the function doesn't return a value, you would use `void` instead of leaving it off.

第二部分是返回值类型。我们在参数和返回值类型之间使用一个箭头（'=>'）来声明返回值的类型。像前面提到的，函数的返回值类型是必须指定的。如果函数没有返回值，可以用 `void` 来代替。

Of note, only the parameters and the return type make up the function type. Captured variables are not reflected in the type. In effect, captured variables are part of the 'hidden state' of any function and do not make up its API.

值得注意的是，函数类型只包含参数和返回值类型，不包含捕获的变量类型。捕获的变量是所有函数的 '隐藏状态'，并不是接口的一部分。

###Inferring the types 类型推断

In playing with the example, you may notice that the TypeScript compiler can figure out the type if you have types on one side of the equation but not the other:

在上面的例子中，你会注意到如果你只在方程的一边指定了类型， TypeScript 的编译器会自动断定其他值的类型。

```ts
// myAdd has the full function type
var myAdd = function(x: number, y: number): number { return x+y; };

// The parameters `x` and `y` have the type number
var myAdd: (baseValue:number, increment:number)=>number = 
    function(x, y) { return x+y; };
```

This is called 'contextual typing', a form of type inference. This helps cut down on the amount of effort to keep your program typed.

这种方式称作 '上下文类型'，类型接口的形式之一。它为你省去了许多给程序添加类型的工作。

Optional and Default Parameters 可选参数和默认参数
----

Unlike JavaScript, in TypeScript every parameter to a function is assumed to be required by the function. This doesn't mean that it isn't a `null` value, but rather, when the function is called the compiler will check that the user has provided a value for each parameter. The compiler also assumes that these parameters are the only parameters that will be passed to the function. In short, the number of parameters to the function has to match the number of parameters the function expects.

与 JavaScript 不同，TypeScript 中认为函数的每个参数都必须存在。这并不是说它不能是 `null` 值，而是当函数被调用时，编译器会检查用户是否为每一个参数提供了值。编译器也假设这些参数就是提供给函数的所有参数。简单来说，提供给函数的参数数量必须和函数定义的参数数量相同。

```ts
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}

var result1 = buildName("Bob");  // error, too few parameters
var result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
var result3 = buildName("Bob", "Adams");  // ah, just right
```

In JavaScript, every parameter is considered optional, and users may leave them off as they see fit. When they do, they're assumed to be undefined. We can get this functionality in TypeScript by using the '?' beside parameters we want optional. For example, let's say we want the last name to be optional:

在 JavaScript 中，每个参数都被当做可选的，用户可以随便省略它。当参数被省略时，它的值是 undefinde。我们可以在 TypeScript 中的参数后加上 '?' 来表示该参数可以被省略。例如，我们想让 lastName 参数可选：

```ts
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

var result1 = buildName("Bob");  // works correctly now
var result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
var result3 = buildName("Bob", "Adams");  // ah, just right
```

Optional parameters must follow required parameters. Had we wanted to make the first name optional rather than the last name, we would need to change the order of parameters in the function, putting the first name last in the list.

可选参数必须放在必选参数之后。如果我们想让 firstName 而不是 lastName 可选，那必须将 firstName 参数放在 lastName 参数之后。

In TypeScript, we can also set up a value that an optional parameter will have if the user does not provide one. These are called default parameters. Let's take the previous example and default the last name to "Smith".

在 TypeScript 中，如果用户没有为一个可选参数提供值，可以让这个参数使用事先设置的值，这个参数叫做默认参数。我们可以将上面例子中姓的默认值设置为 "Smath"。

```ts
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

var result1 = buildName("Bob");  // works correctly now, also
var result2 = buildName("Bob", "Adams", "Sr.");  // error, too many parameters
var result3 = buildName("Bob", "Adams");  // ah, just right
```

Just as with optional parameters, default parameters must come after required parameters in the parameter list. 

和可选参数一样，默认参数也必须放在必选参数之后。

Optional parameters and default parameters also share what the type looks like. Both:

可选参数和默认参数也会共享参数的类型：

```ts
function buildName(firstName: string, lastName?: string) {
```

and

和

```ts
function buildName(firstName: string, lastName = "Smith") {
```

share the same type "(firstName: string, lastName?: string)=>string". The default value of the default parameter disappears, leaving only the knowledge that the parameter is optional.

都共享了 "(firstName: string, lastName?: string)=>string" 类型。默认参数的默认值没有指定，表明它是一个可选的参数。

Rest Parameters 其他的参数
----

Required, optional, and default parameters all have one thing in common: they're about talking about one parameter at a time. Sometimes, you want to work with multiple parameters as a group, or you may not know how many parameters a function will ultimately take. In JavaScript, you can work with the arguments direction using the {{arguments}} variable that is visible inside every function body.

必选、可选和默认参数都有一个共同点：它们都只代表一个参数。有时，你需要处理一个参数组，或者你不关心一个函数最终会用到多少个参数。在 JavaScript 中，你可以使用 arguments 变量来处理参数，它在每个函数体中都是可见的。

In TypeScript, you can gather these arguments together into a variable:

在 TypeScript 中，你可以将这些参数放到一个变量里：

```ts
function buildName(firstName: string, ...restOfName: string[]) {
	return firstName + " " + restOfName.join(" ");
}

var employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

Rest parameters are treated as a boundless number of optional parameters. You may leave them off, or have as many as you want. The compiler will build an array of the arguments you pass to the function under the name given after the ellipsis (...), allowing you to use it in your function. 

其他的参数被看作包含了未知个数参数的列表。它可以为空，也可以有任意个参数。编译器会为紧跟在省略号（...）后的参数创建一个数组，你可以在函数中使用它。

The ellipsis is also used in the type of the function with rest parameters:

省略号也可以用在包含其他参数的函数的类型中。

```ts
function buildName(firstName: string, ...restOfName: string[]) {
	return firstName + " " + restOfName.join(" ");
}

var buildNameFun: (fname: string, ...rest: string[])=>string = buildName;
```

Lambdas and using `this` Lambda 表达式和 `this` 的使用
----

How `this` works in JavaScript functions is a common theme in programmers coming to JavaScript. Indeed, learning how to use it is something of a rite of passage as developers become more accustomed to working in JavaScript. Since TypeScript is a superset of JavaScript, TypeScript developers also need to learn how to use `this` and how to spot when it's not being used correctly. A whole article could be written on how to use `this` in JavaScript, and many have. Here, we'll focus on some of the basics. 

`this` 在 JavaScript 函数中是如何工作的，刚接触 JavaScript 的开发者经常会讨论这个话题。的确，学习如何使用它，对 JavaScript 开发者来说是很大的挑战。既然 TypeScript 是 JavaScript 的超集，TypeScript 的开发者也需要学习如何使用 `this` ，并了解如何排除相关的错误。可以写一整篇文章来讨论如何使用 `this` ，也已经有很多这样的文章了。在这里，我们只关注几点基本的用法。

In JavaScript, `this` is a variable that's set when a function is called. This makes it a very powerful and flexible feature, but it comes at the cost of always having to know about the context that a function is executing in. This can be notoriously confusing, when, for example, when a function is used as a callback.

在 JavaScript 中， `this` 是函数被调用时设置的一个变量。这虽然是一个强力而且灵活的功能，但代价是你必须了解函数执行的上下文。这可是出了名的让人迷惑，例如，当函数被用作回调时。

Let's look at an example:

来看看这个例子：

```ts
var deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            var pickedCard = Math.floor(Math.random() * 52);
            var pickedSuit = Math.floor(pickedCard / 13);
			
            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

var cardPicker = deck.createCardPicker();
var pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

If we tried to run the example, we would get an error instead of the expected alert box. This is because the `this` being used in the function created by `createCardPicker` will be set to `window` instead of our `deck` object. This happens as a result of calling 'cardPicker()'. Here, there is no dynamic binding for `this` other than Window. (note: under strict mode, this will be undefined rather than window).

如果我们运行这个例子，会得到一个错误，而不是期望的弹窗。这是因为在 `createCardPicker` 创建的函数中使用的 `this` 是 `window` ，而不是 `deck` 对象。这是因为我们调用了 'cardPicker()' 。这里，这里只有 Window 会被动态绑定到 `this` 。（注意，在严格模式下，this 会是 unefined，而不是 window）

We can fix this by making sure the function is bound to the correct `this` before we return the function to be used later. This way, regardless of how its later used, it will still be able to see the original `deck` object.

我们可以通过确保函数在使用前绑定了正确的 `this` 来解决这个问题。这样，不论函数如何被使用，它仍然可以访问到原始的 `deck` 对象。

To fix this, we switching the function expression to use the lambda syntax ( ()=>{} ) rather than the JavaScript function expression. This will automatically capture the `this` available when the function is created rather than when it is invoked:

为了修复这个问题，我们使用 lambda 语法（ ()=>{} ）来编写函数。这会自动在函数创建时捕获 `this` ，而不是它被调用时。

```ts
var deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // Notice: the line below is now a lambda, allowing us to capture `this` earlier
        return () => {
            var pickedCard = Math.floor(Math.random() * 52);
            var pickedSuit = Math.floor(pickedCard / 13);
			
            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

var cardPicker = deck.createCardPicker();
var pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

For more information on ways to think about `this`, you can read Yahuda Katz's [Understanding JavaScript Function Invocation and “this”](http:// yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/).

可以阅读 Yahuda Katz 的文章 [Understanding JavaScript Function Invocation and “this”](http:// yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/) 来了解更多关于 `this` 的用法。

Overloads 重载
----

JavaScript is inherently a very dynamic language. It's not uncommon for a single JavaScript function to return different types of objects based on the shape of the arguments passed in. 

JavaScript 本身是一门非常动态的语言。一个函数根据传入参数结构的不同返回不同类型的对象也是很常见的。

```ts
var suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        var pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        var pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

var myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
var pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

var pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

Here the `pickCard` function will return two different things based on what the user has passed in. If the users passes in an object that represents the deck, the function will pick the card. If the user picks the card, we tell them which card they've picked. But how do we describe this to the type system.

这里的 `pickCard` 函数会根据用户输入的不同的值返回两种不同的数据。如果用户传入一个代表 deck 的对象，函数会选择一张卡片。如果用户选择一张卡片，我们告诉他们选择的卡片是哪一张。可是我们该如何向类型系统描述这种情况呢。

The answer is to supply multiple function types for the same function as a list of overloads. This list is what the compiler will use to resolve function calls. Let's create a list of overloads that describe what our `pickCard` accepts and what it returns.

答案是使用一系列的重载函数来支持多种函数类型。编译器会用这个列表来处理不同的函数调用。我们来一起创建一个 `pickCard` 的重载函数列表。

```ts
var suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        var pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        var pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

var myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
var pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

var pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

With this change, the overloads now give us type-checked calls to the `pickCard` function. 

通过这种修改，重载函数让我们可以检查被调用的 `pickCard` 函数的类型。

In order for the compiler to pick the correct typecheck, it follows a similar process to the underlying JavaScript. It looks at the overload list, and proceeding with the first overload attempts to call the function with the provided parameters. If it finds a match, it picks this overload as the correct overload. For this reason, its customary to order overloads from most specific to least specific.

为了让编译器选择正确的类型检测，底层的 JavaScript 中也遵循类似的过程。它遍历整个重载列表，并尝试通过提供的参数来执行这个函数。如果找到一个匹配的函数，就将它作为正确的重载。因此，我们习惯将重载函数从详细到模糊排列。

Note that the 'function pickCard(x): any' piece is not part of the overload list, so it only has two overloads: one that takes an object and one that takes a number. Calling `pickCard` with any other parameter types would cause an error.

注意，'function pickCard(x): any' 不是重载函数之一，所以只有两个重载函数：一个接受对象作为参数，另一个接受数字作为参数。使用任何其他参数类型调用 `pickCard` 都会得到错误。
