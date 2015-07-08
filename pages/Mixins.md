Mixins 混合
====

Introduction 介绍
----

Along with traditional OO hierarchies, another popular way of building up classes from reusable components is to build them by combining simpler partial classes. You may be familiar with the idea of mixins or traits for languages like Scala, and the pattern has also reached some popularity in the JavaScript community.

根据传统面向对象的层级结构，另一种流行的从可重用组件创建类的方式是，将简单的部分类组合起来。如果你用过 Scala 之类的语言，你应该会对混合和特征的思想比较熟悉了，这种模式也在 JavaScript 社区中也很受欢迎。

Mixin sample 混合的例子
----

In the code below, we show how you can model mixins in TypeScript. After the code, we'll break down how it works.

在下面的例子中，我们向你展示了 TypeScript 中的混合范例。在代码之后，我们会解析它的细节。

```ts
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }
}
 
// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}
 
class SmartObject implements Disposable, Activatable {
    constructor() {
        setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
    }
 
    interact() {
        this.activate();
    }
 
    // Disposable
    isDisposed: boolean = false;
    dispose: () => void;
    // Activatable
    isActive: boolean = false;
    activate: () => void;
    deactivate: () => void;
}
applyMixins(SmartObject, [Disposable, Activatable])
 
var smartObj = new SmartObject();
setTimeout(() => smartObj.interact(), 1000);
 
////////////////////////////////////////
// In your runtime library somewhere
////////////////////////////////////////

function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        })
    }); 
}
```

Understanding the sample 理解这个例子
----

The code sample starts with the two classes that will act is our mixins. You can see each one is focused on a particular activity or capability. We'll later mix these together to form a new class from both capabilities.

这个代码片段中的两个类将作为混合。你会发现每个类都有特定的行为和功能。稍后我们将会混合它们来组成一个包含两种功能的类。

```ts
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }
}
 
// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}
```

Next, we'll create the class that will handle the combination of the two mixins. Let's look at this in more detail to see how it does this:

然后，我们要创建一个类来管理两个混合。我们来详细分析一下它的工作细节：

```ts
class SmartObject implements Disposable, Activatable {
```

The first thing you may notice in the above is that instead of using `extends`, we use `implements`. This treats the classes as interfaces, and only uses the types behind Disposable and Activatable rather than the implementation. This means that we'll have to provide the implementation in class. Except, that's exactly what we want to avoid by using mixins.

你会注意到的第一点是我们用了 `implements` 而不是 `extends`。这里将类当做接口，而且只是用 Disposable 和 Activatable 中的类型。这意味着我们必须在类中提供实现。当然，这正是我们使用混合的时候需要避免的。

To satisfy this requirement, we create stand-in properties and their types for the members that will come from our mixins. This satisfies the compiler that these members will be available at runtime. This lets us still get the benefit of the mixins, albeit with a some bookkeeping overhead.

为了满足这个需求，我们为来自混合的成员创建了替代属性及类型，使这些成员在执行时可用。尽管有些复杂，但我们仍然能够得到混合的益处。

```ts
// Disposable
isDisposed: boolean = false;
dispose: () => void;
// Activatable
isActive: boolean = false;
activate: () => void;
deactivate: () => void;
```

Finally, we mix our mixins into the class, creating the full implementation.

最后，我们将混合添加到类中，创建了一个完整的实现。

```ts
applyMixins(SmartObject, [Disposable, Activatable])
```

Lastly, we create a helper function that will do the mixing for us. This will run through the properties of each of the mixins and copy them over to the target of the mixins, filling out the stand-in properties with their implementations.

最终，我们创建了一个工具函数来帮助我们执行混合操作。它会遍历每个混合的属性，并将其复制到目标对象中，并填充实现的替代属性。
 
```ts
function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        })
    }); 
}
```
