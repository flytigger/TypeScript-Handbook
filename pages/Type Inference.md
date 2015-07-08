Type Inference 类型推断
====

Introduction 介绍
----

In this section, we will cover type inference in TypeScript. Namely, we'll discuss where and how types are inferred.

在这一节，我们要学习 TypeScript 中的类型推断。也就是类型在那里、如何被推断。

Basics 基础
----

In TypeScript, there are several places where type inference is used to provide type information when there is no explicit type annotation. For example, in this code:

在 TypeScript 中，如果没有明确的类型声明，类型推断被用来提供类型信息。例如，在这个代码中：

```ts
var x = 3;
```

The type of the `x` variable is inferred to be `number`. This kind of inference takes place when initializing variables and members, setting parameter default values, and determining function return types.

`x` 的类型被推断为 `number`。当初始化变量或成员，设置参数默认值，或确认函数返回值时，这类推断就会发生，

In most cases, type inference is straightforward. In the following sections, we'll explore some of the nuance in how types are inferred.

大多数情况下，类型推断是很简单的。在下面的小节中，我们会探索类型推断中的细微差别。

Best common type 最优类型
----

When a type inference is made from several expressions, the types of those expressions are used to calculate a "best common type". For example:

当类型推断以多个表达式作为依据时，这些表达式的类型将被用来计算一个 "最优类型"。例如：

```ts
var x = [0, 1, null];
```

To infer the type of `x` in the example above, we must consider the type of each array element. Here we are given two choices for the type of the array: `number` and `null`. The best common type algorithm considers each candidate type, and picks the type that is compatible with all the other candidates.

为了推断 `x` 的类型，我们要考虑数组的每一个成员的类型。这里我们有两种选择：`number` 和 `null`。最优类型算法会考虑每一个候选类型，并选择一个兼容所有候选类型的类型。

Because the best common type has to be chosen from the provided candidate types, there are some cases where types share a common structure, but no one type is the super type of all candidate types. For example:

由于最优类型需要从提供的候选类型中选择，有时候多个类型都有类似的结构，但并没有一个所有候选类型的父类。如：

```ts
var zoo = [new Rhino(), new Elephant(), new Snake()];
```

Ideally, we may want `zoo` to be inferred as an `Animal[]`, but because there is no object that is strictly of type `Animal` in the array, we make no inference about the array element type. To correct this, instead explicitly provide the type when no one type is a super type of all other candidates:

通常，我们想让 `zoo` 推断为 `Animal[]`，不过由于数组中并没有一个对象指明 `Animal` 类型，我们没办法推断出数组的类型。为了纠正这个问题，我们在无法确定父类型的时候指明一个类型：

```ts
var zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];
```

When no best common type is found, the resulting inference is the empty object type, {}. Because this type has no members, attempting to use any properties of it will cause an error. This result allows you to still use the object in a type-agnostic manner, while providing type safety in cases where the type of the object can't be implicitly determined.

当没有最优类型的时候，会推断出一个空对象（{}）。由于这个类型没有任何成员，当试图访问它的成员时会引起错误。{无法翻译}

Contextual Type 上下文类型
----

Type inference also works in "the other direction" in some cases in TypeScript. This is known as "contextual typing". Contextual typing occurs when the type of an expression is implied by its location. For example: 

```ts
window.onmousedown = function(mouseEvent) { 
    console.log(mouseEvent.buton);  //<- Error
};
```

For the code above to give the type error, the TypeScript type checker used the type of the Window.onmousedown function to infer the type of the function expression on the right hand side of the assignment. When it did so, it was able to infer the type of the `mouseeVent` parameter. If this function expression were not in a contextually typed position, the `mouseeVent` parameter would have type `any`, and no error would have been issued.

If the contextually typed expression contains explicit type information, the contextual type is ignored. Had we written the above example:

```ts
window.onmousedown = function(mouseEvent: any) { 
    console.log(mouseEvent.buton);  //<- Now, no error is given
};
```

The function expression with an explicit type annotation on the parameter will override the contextual type. Once it does so, no error is given as no contextual type applies.

Contextual typing applies in many cases. Common cases include arguments to function calls, right hand sides of assignments, type assertions, members of object and array literals, and return statements. The contextual type also acts as a candidate type in best common type. For example:

```ts
function createZoo(): Animal[] {
    return [new Rhino(), new Elephant(), new Snake()];
}
```

In this example, best common type has a set of four candidates: Animal, Rhino, Elephant, and Snake. Of these, Animal can be chosen by the best common type algorithm.
