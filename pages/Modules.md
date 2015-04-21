Modules 模块
====

Introduction 介绍
----

This post outlines the various ways to organize your code using modules in TypeScript. We'll be covering internal and external modules and we'll discuss when each is appropriate and how to use them. We'll also go over some advanced topics of how to use external modules, and address some common pitfalls when using modules in TypeScript.

这篇文章列出了在 TYpeScript 中使用模块来管理代码的各种方式。这些内容会覆盖内部和外部的模块，并讨论如何恰当地以及正确地使用它们。我们也会了解一些如何使用外部模块，以及在 TypeScript 中使用模块的一些常见陷阱等高级内容。

###First steps 第一步

Let's start with the program we'll be using as our example throughout this page. We've written a small set of simplistic string validators, like you might use when checking a user's input on a form in a webpage or checking the format of an externally-provided data file.

让我们先来写一个整篇文章都会用到的示例程序。我们来写一个简单的字符串校验程序，就像你用来检查用户在网页的表单中的输入，或者用来检查外部提供的数据文件那样。

####Validators in a single file 单文件的校验程序

```ts
interface StringValidator {
    isAcceptable(s: string): boolean;
}

var lettersRegexp = /^[A-Za-z]+$/;
var numberRegexp = /^[0-9]+$/;

class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}

class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: StringValidator; } = {};
validators['ZIP code'] = new ZipCodeValidator();
validators['Letters only'] = new LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

###Adding Modularity 使用模块

As we add more validators, we're going to want to have some kind of organization scheme so that we can keep track of our types and not worry about name collisions with other objects. Instead of putting lots of different names into the global namespace, let's wrap up our objects into a module.

当我们添加更多的校验器时，我们会需要一个管理方案，来跟踪我们的类型，并防止与其他对象的命名冲突。我们将所有对象包装到一个模块中，而不是向全局命名空间中添加过多的名称。

In this example, we've moved all the Validator-related types into a module called _Validation_. Because we want the interfaces and classes here to be visible outside the module, we preface them with _export_. Conversely, the variables _lettersRegexp_ and _numberRegexp_ are implementation details, so they are left unexported and will not be visible to code outside the module. In the test code at the bottom of the file, we now need to qualify the names of the types when used outside the module, e.g. _Validation.LettersOnlyValidator_.

在这个例子中，我们将校验器相关的类型都移动到 _Validation_ 模块中。因为我们需要让接口和类在模块外部可见，我们给他们加上 _export_ 前缀。相反的， _lettersRegexp_ 和 _numberRegexp_ 都是实现细节，它们将不被导出，因此在模块外部不可见。在文件底部的测试代码中，当在模块外使用这些类型时，我们需要使用模块名来修饰这些类型，如 _Validation.LettersOnlyValidator_ 。

####Modularized Validators 模块化的校验程序

```ts
module Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    var lettersRegexp = /^[A-Za-z]+$/;
    var numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

Splitting Across Files 分割成多个文件
----

As our application grows, we'll want to split the code across multiple files to make it easier to maintain.

当应用规模不断增长时，我们需要将代码分离到多个文件中，使其方便管理。

Here, we've split our Validation module across many files. Even though the files are separate, they can each contribute to the same module and can be consumed as if they were all defined in one place. Because there are dependencies between files, we've added reference tags to tell the compiler about the relationships between the files. Our test code is otherwise unchanged.

这里，我们将校验模块分离成了多个文件。虽然被分成多个文件，但它们仍然属于同一个模块，并像在同一处定义的一样使用。由于它们的文件相互依赖，我们在文件中添加了引用标签，告诉编译器它们之间的关系。我们的测试代码没有变化。

###Multi-file internal modules 多文件内部模块

####Validation.ts

```ts
module Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

####LettersOnlyValidator.ts

```ts
/// <reference path="Validation.ts" />
module Validation {
    var lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

####ZipCodeValidator.ts

```ts
/// <reference path="Validation.ts" />
module Validation {
    var numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
```

####Test.ts

```ts
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

Once there are multiple files involved, we'll need to make sure all of the compiled code gets loaded. There are two ways of doing this.

一旦有多个相关文件，我们需要保证所有需要被便衣的代码都被载入了。有两种方式来实现。

First, we can use concatenated output using the _--out_ flag to compile all of the input files into a single JavaScript output file:

首先，我们可以使用 _--out_ 参数使所有文件被编译到同一个 JavaScript 文件中：

```
tsc --out sample.js Test.ts
```

The compiler will automatically order the output file based on the reference tags present in the files. You can also specify each file individually:

编译器会根据文件中的引用标签自动排列输出文件。你也可以单独指定每一个文件：

```
tsc --out sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
```

Alternatively, we can use per-file compilation (the default) to emit one JavaScript file for each input file. If multiple JS files get produced, we'll need to use _<script>_ tags on our webpage to load each emitted file in the appropriate order, for example:

另外，我们也可以将每个输入的文件单独编译（默认行为）。如果产生了多个 JS 文件，我们需要在网页中使用 _<script>_ 标签通过特定的顺序载入每个文件，例如：

####MyTestPage.html (excerpt)

```ts
    <script src="Validation.js" type="text/javascript" />
    <script src="LettersOnlyValidator.js" type="text/javascript" />
    <script src="ZipCodeValidator.js" type="text/javascript" />
    <script src="Test.js" type="text/javascript" />
```

Going External 使用外部模块
----

TypeScript also has the concept of an external module. External modules are used in two cases: node.js and require.js. Applications not using node.js or require.js do not need to use external modules and can best be organized using the internal module concept outlined above.

TypeScript 中也有外部模块的概念。外部模块有两个应用场景：node.js 和 require.js。不使用 node.js 和 require.js 的应用不需要外部模块，是用上面讲到的内部模块概念来管理就可以了。

In external modules, relationships between files are specified in terms of imports and exports at the file level. In TypeScript, any file containing a top-level _import_ or _export_ is considered an external module.

外部模块中，文件间的关系通过文件中的导入和导出规则制定。在 TypeScript 中，任何包含顶级 _import_ 或 _export_ 的文件都被看做一个外部模块。

Below, we have converted the previous example to use external modules. Notice that we no longer use the module keyword – the files themselves constitute a module and are identified by their filenames.

下面，我们将之前的示例改写成使用外部模块的方式。注意，我们不再使用 module 关键词——文件本身就构成了一个通过其文件名区分的模块。

The reference tags have been replaced with _import_ statements that specify the dependencies between modules. The _import_ statement has two parts: the name that the module will be known by in this file, and the require keyword that specifies the path to the required module:

我们使用 _import_ 声明代替引用标签来指定模块间的依赖关系。_import_ 声明包含两部分：模块在当前文件中的名称，以及制定了外部模块路径的 require 关键词：

```ts
import someMod = require('someModule');
```

We specify which objects are visible outside the module by using the _export_ keyword on a top-level declaration, similarly to how _export_ defined the public surface area of an internal module.

我们在顶级声明中使用 _export_ 关键词指定那些对象在模块外可见，就像使用 _export_ 定义内部模块的公共部分一样。

To compile, we must specify a module target on the command line. For node.js, use _--module commonjs_; for require.js, use _--module amd_. For example:

我们必须在命令行中指定模块编译的目标。 _--module commonjs_ 编译成 node.js 模块，  _--module amd_ 编译成 require.js 模块。例如：

```
tsc --module commonjs Test.ts
```

When compiled, each external module will become a separate .js file. Similar to reference tags, the compiler will follow _import_ statements to compile dependent files.

编译时，每个外部模块都会成为一个独立的 .js 文件。类似引用标签，编译器会根据 _import_ 声明来编译依赖文件。

####Validation.ts

```ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

####LettersOnlyValidator.ts

```ts
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
export class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
```

####ZipCodeValidator.ts

```ts
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
export class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

####Test.ts

```ts
import validation = require('./Validation');
import zip = require('./ZipCodeValidator');
import letters = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zip.ZipCodeValidator();
validators['Letters only'] = new letters.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

###Code Generation for External Modules 外部模块的代码生成

Depending on the module target specified during compilation, the compiler will generate appropriate code for either node.js (commonjs) or require.js (AMD) module-loading systems. For more information on what the _define_ and _require_ calls in the generated code do, consult the documentation for each module loader.

编译器会根据编译时指定的目标模块来生成 node.js (commonjs) 或 require.js (AMD) 对应的代码。想要了解更多关于 _define_ 和 _require_ 在生成的代码中如何工作的信息，可以参考对应模块加载器的文档。

This simple example shows how the names used during importing and exporting get translated into the module loading code.

下面的例子展示了模块名在导入和导出时是如何被翻译的。

####SimpleModule.ts

```ts
import m = require('mod');
export var t = m.something + 1;
```

####AMD / RequireJS SimpleModule.js:

```ts
define(["require", "exports", 'mod'], function(require, exports, m) {
    exports.t = m.something + 1;
});
```

####CommonJS / Node SimpleModule.js:

```ts
var m = require('mod');
exports.t = m.something + 1;
```

Export =
----

In the previous example, when we consumed each validator, each module only exported one value. In cases like this, it's cumbersome to work with these symbols through their qualified name when a single identifier would do just as well.

在上面的例子中，我们编写的每个校验器模块只导出了一个值。在这种情况下，处理这么多模块名是很麻烦的，只要一个识别符就可以了。

The export = syntax specifies a single object that is exported from the module. This can be a class, interface, module, function, or enum. When imported, the exported symbol is consumed directly and is not qualified by any name.

`export =` 语法指定了从模块中导出一个对象。这个对象可以是类、接口、模块、函数或枚举值。当引入该值时，导出的符号可以直接使用，不需要通过任何名称来指定。

Below, we've simplified the Validator implementations to only export a single object from each module using the export = syntax. This simplifies the consumption code – instead of referring to 'zip.ZipCodeValidator', we can simply refer to 'zipValidator'.

下面，我们使用 `export =` 语法从每个模块中导出一个对象，来简化校验器的实现。这简化了使用代码——使用 'zipValidator' 而不是 'zip.ZipCodeValidator'。

####Validation.ts

```ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

####LettersOnlyValidator.ts

```ts
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
export = LettersOnlyValidator;
```

####ZipCodeValidator.ts

```ts
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

####Test.ts

```ts
import validation = require('./Validation');
import zipValidator = require('./ZipCodeValidator');
import lettersValidator = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zipValidator();
validators['Letters only'] = new lettersValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

Alias 别名
----

Another way that you can simplify working with either kind of module is to use _import q = x.y.z_ to create shorter names for commonly-used objects. Not to be confused with the _import x = require('name')_ syntax used to load external modules, this syntax simply creates an alias for the specified symbol. You can use these sorts of imports (commonly referred to as aliases) for any kind of identifier, including objects created from external module imports.

另一种简化使用多种模块的方式，是使用 _import q = x.y.z_ 来创建经常使用的对象的短名称。不要与使用 _import x = require('name')_ 导入外部模块的语法弄混，这个语法只是为指定的符号创建一个别名。你可以为任何类型的识别符使用这种导入方式（通常称作别名），包括通过导入外部模块创建的对象。

####Basic Aliasing 基本的别名

```ts
module Shapes {
    export module Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
var sq = new polygons.Square(); // Same as 'new Shapes.Polygons.Square()'
```

Notice that we don't use the _require_ keyword; instead we assign directly from the qualified name of the symbol we're importing. This is similar to using _var_, but also works on the type and namespace meanings of the imported symbol. Importantly, for values, _import_ is a distinct reference from the original symbol, so changes to an aliased _var_ will not be reflected in the original variable.

注意，我们没有使用 _require_ 关键词；相反地，我们直接直接将制定的名称赋值给导入的符号。这与使用 _var_ 类似，不过对导入符号的类型和命名空间也有效。重要的一点，对于值来说， _import_ 是与原始的符号不同的引用，所以修改别名变量不会影响到原始变量。

Optional Module Loading and Other Advanced Loading Scenarios 可选的模块加载及高级模块加载方案
----

In some cases, you may want to only load a module under some conditions. In TypeScript, we can use the pattern shown below to implement this and other advanced loading scenarios to directly invoke the module loaders without losing type safety.

有时候，你只需要在特定的情况下加载一个模块。在 TypeScript 中，我们可以用下面的方式来实现这个功能，还有其他在不丢失类型安全的前提下调用模块加载器的高级加载方式。

The compiler detects whether each module is used in the emitted JavaScript. For modules that are only used as part of the type system, no require calls are emitted. This culling of unused references is a good performance optimization, and also allows for optional loading of those modules.

编译器会检查启动的 JavaScript 中每一个模块是否被使用。对于值作为类型系统一部分的模块来说，导入调用不会被执行。这种消除未使用的引用是一种很好的性能优化方式，也使模块拥有了动态加载的特性。

The core idea of the pattern is that the _import id = require('...')_ statement gives us access to the types exposed by the external module. The module loader is invoked (through require) dynamically, as shown in the if blocks below. This leverages the reference-culling optimization so that the module is only loaded when needed. For this pattern to work, it's important that the symbol defined via import is only used in type positions (i.e. never in a position that would be emitted into the JavaScript).

To maintain type safety, we can use the _typeof_ keyword. The _typeof_ keyword, when used in a type position, produces the type of a value, in this case the type of the external module.

####Dynamic Module Loading in node.js

```ts
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    var x: typeof Zip = require('./ZipCodeValidator');
    if (x.isAcceptable('.....')) { /* ... */ }
}
```

####Sample: Dynamic Module Loading in require.js

```ts
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    require(['./ZipCodeValidator'], (x: typeof Zip) => {
        if (x.isAcceptable('...')) { /* ... */ }
    });
}
```

Working with Other JavaScript Libraries 使用其他 JavaScript 库
----

To describe the shape of libraries not written in TypeScript, we need to declare the API that the library exposes. Because most JavaScript libraries expose only a few top-level objects, modules are a good way to represent them. We call declarations that don't define an implementation "ambient". Typically these are defined in .d.ts files. If you're familiar with C/C++, you can think of these as .h files or 'extern'. Let's look at a few examples with both internal and external examples.

###Ambient Internal Modules 包装内部模块

The popular library D3 defines its functionality in a global object called 'D3'. Because this library is loaded through a _script_ tag (instead of a module loader), its declaration uses internal modules to define its shape. For the TypeScript compiler to see this shape, we use an ambient internal module declaration. For example:

####D3.d.ts (simplified excerpt)

```ts
declare module D3 {
    export interface Selectors {
        select: {
            (selector: string): Selection;
            (element: EventTarget): Selection;
        };
    }

    export interface Event {
        x: number;
        y: number;
    }

    export interface Base extends Selectors {
        event: Event;
    }
}

declare var d3: D3.Base;
```

###Ambient External Modules 包装外部模块

In node.js, most tasks are accomplished by loading one or more modules. We could define each module in its own .d.ts file with top-level export declarations, but it's more convenient to write them as one larger .d.ts file. To do so, we use the quoted name of the module, which will be available to a later import.  For example:

####node.d.ts (simplified excerpt)

```ts
declare module "url" {
    export interface Url {
        protocol?: string;
        hostname?: string;
        pathname?: string;
    }

    export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export var sep: string;
}
```

Now we can _/// <reference>_ node.d.ts and then load the modules using e.g. _import url = require('url');_.

```ts
///<reference path="node.d.ts"/>
import url = require("url");
var myUrl = url.parse("http://www.typescriptlang.org");
```


Pitfalls of Modules 模块的陷阱
----

In this section we'll describe various common pitfalls in using internal and external modules, and how to avoid them.

###<reference> to an external module <reference> 引用外部模块

A common mistake is to try to use the /// <reference> syntax to refer to an external module file, rather than using import. To understand the distinction, we first need to understand the three ways that the compiler can locate the type information for an external module.

The first is by finding a .ts file named by an _import x = require(...);_ declaration. That file should be an implementation file with top-level import or export declarations.

The second is by finding a .d.ts file, similar to above, except that instead of being an implementation file, it's a declaration file (also with top-level import or export declarations).

The final way is by seeing an "ambient external module declaration", where we 'declare' a module with a matching quoted name.

####myModules.d.ts

```ts
// In a .d.ts file or .ts file that is not an external module:
declare module "SomeModule" {
    export function fn(): string;
}
```

####myOtherModule.ts

```ts
/// <reference path="myModules.d.ts" />
import m = require("SomeModule");
```

The reference tag here allows us to locate the declaration file that contains the declaration for the ambient external module. This is how the node.d.ts file that several of the TypeScript samples use is consumed, for example.

###Needless Namespacing 多余的命名空间

If you're converting a program from internal modules to external modules, it can be easy to end up with a file that looks like this:

####shapes.ts

```ts
export module Shapes {
    export class Triangle { /* ... */ }
    export class Square { /* ... */ }
}
```

The top-level module here _Shapes_ wraps up _Triangle_ and _Square_ for no reason. This is confusing and annoying for consumers of your module:

####shapeConsumer.ts

```ts
import shapes = require('./shapes');
var t = new shapes.Shapes.Triangle(); // shapes.Shapes?
```

A key feature of external modules in TypeScript is that two different external modules will never contribute names to the same scope. Because the consumer of an external module decides what name to assign it, there's no need to proactively wrap up the exported symbols in a namespace.

To reiterate why you shouldn't try to namespace your external module contents, the general idea of namespacing is to provide logical grouping of constructs and to prevent name collisions. Because the external module file itself is already a logical grouping, and its top-level name is defined by the code that imports it, it's unnecessary to use an additional module layer for exported objects.
 
Revised Example:

####shapes.ts

```ts
export class Triangle { /* ... */ }
export class Square { /* ... */ }
```

####shapeConsumer.ts

```ts
import shapes = require('./shapes');
var t = new shapes.Triangle(); 
```

###Trade-offs for External Modules

Just as there is a one-to-one correspondence between JS files and modules, TypeScript has a one-to-one correspondence between external module source files and their emitted JS files. One effect of this is that it's not possible to use the _--out_ compiler switch to concatenate multiple external module source files into a single JavaScript file.
