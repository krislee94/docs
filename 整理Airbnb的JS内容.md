# 整理Airbnb的JS内容
    

[原文链接](https://github.com/airbnb/javascript)

  1. [Types](#types)
  1. [References](#references)
  1. [Objects](#objects)
  1. [Arrays](#arrays)
  1. [Destructuring](#destructuring)
  1. [Strings](#strings)
  1. [Functions](#functions)
  1. [Arrow Functions](#arrow-functions)
  1. [Classes & Constructors](#classes--constructors)
  1. [Modules](#modules)
  1. [Iterators and Generators](#iterators-and-generators)
  1. [Properties](#properties)
  1. [Variables](#variables)
  1. [Hoisting](#hoisting)
  1. [Comparison Operators & Equality](#comparison-operators--equality)
  1. [Blocks](#blocks)
  1. [Control Statements](#control-statements)
  1. [Comments](#comments)
  1. [Whitespace](#whitespace)
  1. [Commas](#commas)
  1. [Semicolons](#semicolons)
  1. [Type Casting & Coercion](#type-casting--coercion)
  1. [Naming Conventions](#naming-conventions)
  1. [Accessors](#accessors)






<h3 name="types">类型</h3>

基本数据类型

    - `string`
    - `number`
    - `boolean`
    - `null`
    - `undefined`
    - `symbol`
    
JavaScript 代码:

```javascript
const foo = 1;
let bar = foo;
    
 bar = 9;
    
console.log(foo, bar); // => 1, 9
```
- Symbols 不能被完全 polyfill, 所以不应该在目标浏览器/环境不支持它们的情况下使用它们。

<h3 name="references">引用 References</h3>

1. 对所有的引用使用 `const` ；不要使用 `var`。eslint: `prefer-const`, `no-const-assign`

```
// bad
var a = 1;
var b = 2;
 
// good
const a = 1;
const b = 2;
```

2. 如果你一定需要可变动的引用，使用 `let` 代替 `var`。eslint: `no-var` jscs: `disallowVar`

> 为什么？因为 let 是块级作用域，而 var 是函数作用域。

```
// bad
var count = 1;
if (true) {
    count += 1;
}
 
// good, 使用 let.
let count = 1;
if (true) {
    count += 1;
}
```

<h3 name="objects">对象 Objects</h3>

1. 使用字面量语法创建对象   eslint:`no-new-object`

```
// bad
const item = new Object();
 
// good
const item = {};
```

2. 使用属性速记语法. eslint: `object-shorthand`

JavaScript 代码:

```
const lukeSkywalker = 'Luke Skywalker';
 
// bad
const obj = {
    lukeSkywalker: lukeSkywalker,
};
 
// good
const obj = {
    lukeSkywalker,
};
```

3. 不要直接调用`Object.prototype`的方法、比如`hasOwnProperty`,`propertyIsEnumerable`,`isPrototypeOf`
    
    >  为什么？这些方法可能会被对象的属性所覆盖 – 比如 { hasOwnProperty: false } – 或者，对象可能是空( null )对象(Object.create(null))。
    
    ```
    // bad
    console.log(object.hasOwnProperty(key));
     
    // good
    console.log(Object.prototype.hasOwnProperty.call(object, key));
     
    // best
    const has = Object.prototype.hasOwnProperty; // 在模块作用域内，缓存查找一次。
    /* or */
    import has from 'has';
    // ...
    console.log(has.call(object, key));
    ```
    
4. 用对象展开操作符浅复制对象，优先于`Object.assign` 。使用对象剩余操作符来获得一个省略某些属性的新对象。
   
   ```
    // very bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign(original, { c: 3 }); //  `original` 是可变的 ?_?
    delete copy.a; // so does this
     
    // bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }
     
    // good
    const original = { a: 1, b: 2 };
    const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }
     
    const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
   ```
   

<h3 name="arrays">数组 Arrays</h3>


1. 使用字面量创建数组。 eslint: no-array-constructor

    ```
    // bad
    const items = new Array();
     
    // good
    const items = [];
    ```

2. 使用数组展开操作符 ... 复制数组。

    ```
    // bad
    const len = items.length;
    const itemsCopy = [];
    let i;
     
    for (i = 0; i < len; i += 1) {
        itemsCopy[i] = items[i];
    }
     
    // good
    const itemsCopy = [...items];
    ```

3. 使用展开操作符 ... 代替 Array.from，来将一个类数组(array-like) 对象转换成数组。

    ```
    const foo = document.querySelectorAll('.foo');
     
    // good
    const nodes = Array.from(foo);
     
    // best
    const nodes = [...foo];
```

<h3 name="destructuring">解构 Destructuring</h3>

1. 当访问和使用对象的多个属性时，请使用对象解构。eslint: `prefer-destructuring` jscs: `requireObjectDestructuring`

> 为什么?解构可以在你建这些属性的临时引用时，为你节省时间。

    ```
    function getFullName(user) {
        const firstName = user.firstName;
        const lastName = user.lastName;
     
        return `${firstName} ${lastName}`;
    }
     
    // good
    function getFullName(user) {
        const { firstName, lastName } = user;
        return `${firstName} ${lastName}`;
    }
     
    // best
    function getFullName({ firstName, lastName }) {
        return `${firstName} ${lastName}`;
    }
    ```

2. 使用数组解构  eslint: `prefer-destructuring` jscs: `requireArrayDestructuring`
    
    ```
    const arr = [1, 2, 3, 4];
     
    // bad
    const first = arr[0];
    const second = arr[1];
     
    // good
    const [first, second] = arr;
    ```

<h3 name="strings">字符串 Strings</h3>

1. 字符串使用单引号 ''。 eslint: `quotes` jscs: `validateQuoteMarks`

    ```
    // bad
    const name = "Capt. Janeway";
     
    // bad - 模板字面量应该包含插值或换行符
    const name = `Capt. Janeway`;
     
    // good
    const name = 'Capt. Janeway';
    ```
    
2. 以编程方式构建字符串时，请使用模板字符串而不是字符串连接。eslint: `prefer-template template-curly-spacing` jscs: `requireTemplateStrings`

   ```
    // bad
    function sayHi(name) {
        return 'How are you, ' + name + '?';
    }
     
    // bad
    function sayHi(name) {
        return ['How are you, ', name, '?'].join();
    }
     
    // bad
    function sayHi(name) {
        return `How are you, ${ name }?`;
    }
     
    // good
    function sayHi(name) {
        return `How are you, ${name}?`;
    }
   ```
   
3. 永远不要在字符串上使用 `eval()` ，它存在太多不确定因素。 `eslint: no-eval`
   
   
   
<h3 name="functions"></h3>

1. 优先使用展开运算符 ... 来调用可变参数函数。 eslint: `prefer-spread`

> 为什么？ 它更清洁，你不需要提供一个上下文，而且你不能轻易地实用 apply 和 new。

    ```
    // bad
    const x = [1, 2, 3, 4, 5];
    console.log.apply(console, x);
     
    // good
    const x = [1, 2, 3, 4, 5];
    console.log(...x);
     
    // bad
    new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));
     
    // good
    new Date(...[2016, 8, 5]);
    ```
    
2. 参数不要重新赋值。 eslint: `no-param-reassign`

    > 为什么？ 重新分配参数可能会导致意外的行为，特别是在访问 arguments 对象时。它也可能导性能化问题，特别是在V8中。
    
        ```
            // bad
        function f1(a) {
            a = 1;
            // ...
        }
         
        function f2(a) {
            if (!a) { a = 1; }
            // ...
        }
         
        // good
        function f3(a) {
            const b = a || 1;
            // ...
        }
         
        function f4(a = 1) {
            // ...
        }
        ```
        
<h3 name="arrow-functions">箭头函数 Arrow Functions</h3>

1. 当您必须使用匿名函数（如在传递一个内联回调时），请使用箭头函数表示法。 eslint: `prefer-arrow-callback`, `arrow-spacing`

>为什么？ 它创建了一个在 this 上下文中执行的函数的版本，这通常是你想要的，而且这样的写法更为简洁。


   ```
      // bad
    [1, 2, 3].map(function (x) {
        const y = x + 1;
        return x * y;
    });
     
    // good
    [1, 2, 3].map((x) => {
        const y = x + 1;
        return x * y;
    });
   ```
   
2. 如果函数体由一个返回无副作用(side effect)的expression(表达式)的单行语句组成，那么可以省略大括号并使用隐式返回。否则，保留大括号并使用 return 语句。`eslint: arrow-parens, arrow-body-style`

> 语法糖，当多个函数链式调用时，可读性更高

    ```
    // bad
    [1, 2, 3].map(number => {
        const nextNumber = number + 1;
        `A string containing the ${nextNumber}.`;
    });
     
    // good
    [1, 2, 3].map(number => `A string containing the ${number}.`);
     
    // good
    [1, 2, 3].map((number) => {
        const nextNumber = number + 1;
        return `A string containing the ${nextNumber}.`;
    });
     
    // good
    [1, 2, 3].map((number, index) => ({
        [index]: number,
    }));
     
    // No implicit return with side effects
    function foo(callback) {
        const val = callback();
        if (val === true) {
        // Do something if callback returns true
        }
    }
     
    let bool = false;
     
    // bad
    foo(() => bool = true);
     
    // good
    foo(() => {
        bool = true;
    });
    ```
    

<h3 name="classes--constructors">类 Classes & 构造函数 Constructors</h3>

1. 总是使用 class。避免直接操作 prototype 。

>为什么? 因为 class 语法更为简洁更易读。

    ```
    // bad
    function Queue(contents = []) {
        this.queue = [...contents];
    }
    Queue.prototype.pop = function () {
        const value = this.queue[0];
        this.queue.splice(0, 1);
        return value;
    };
     
    // good
    class Queue {
        constructor(contents = []) {
        this.queue = [...contents];
        }
        pop() {
        const value = this.queue[0];
        this.queue.splice(0, 1);
        return value;
        }
    }
    ```
    
2. 使用`extends`继承

> 为什么？因为 extends 是一个内置的原型继承方法并且不会破坏 instanceof。

    ```
    // bad
    const inherits = require('inherits');
    function PeekableQueue(contents) {
        Queue.apply(this, contents);
    }
    inherits(PeekableQueue, Queue);
    PeekableQueue.prototype.peek = function () {
        return this.queue[0];
    };
     
    // good
    class PeekableQueue extends Queue {
        peek() {
        return this.queue[0];
        }
    }
    ```
    
3. 方法可以返回 this 来帮助链式调用。

> 增加代码可读性与可拓展性

```
// bad
Jedi.prototype.jump = function () {
    this.jumping = true;
    return true;
};
 
Jedi.prototype.setHeight = function (height) {
    this.height = height;
};
 
const luke = new Jedi();
luke.jump(); // => true
luke.setHeight(20); // => undefined
 
// good
class Jedi {
    jump() {
    this.jumping = true;
    return this;
    }
 
    setHeight(height) {
    this.height = height;
    return this;
    }
}
 
const luke = new Jedi();
 
luke.jump()
    .setHeight(20);
```

4. 避免重复类成员 eslint: `no-dupe-class-members`

> 为什么？ 重复类成员声明将默认使用最后一个 – 重复类成员几乎肯定是一个错误。

```
// bad
class Foo {
    bar() { return 1; }
    bar() { return 2; }
}
 
// good
class Foo {
    bar() { return 1; }
}
 
// good
class Foo {
    bar() { return 2; }
}
```

<h3 name="modules">模块 Modules</h3>

1. 使用模块 (import/export) 而不是其他非标准模块系统。你可以编译为你喜欢的模块系统。

```
// bad
const AirbnbStyleGuide = require('./AirbnbStyleGuide');
module.exports = AirbnbStyleGuide.es6;
 
// ok
import AirbnbStyleGuide from './AirbnbStyleGuide';
export default AirbnbStyleGuide.es6;
 
// best
import { es6 } from './AirbnbStyleGuide';
export default es6;
```

2. 将所有 import 导入放在非导入语句的上面。eslint: `import/first`

>由于 import 被提升，保持他们在顶部，防止意外的行为。

```
// bad
import foo from 'foo';
foo.init();
 
import bar from 'bar';
 
// good
import foo from 'foo';
import bar from 'bar';
 
foo.init();
```

<h3 name="iterators-and-generators">迭代器 Iterators 和 生成器 Generators</h3>

1. 不要使用 iterators（迭代器） 。请使用高阶函数，例如 map() 和 reduce() 等，而不是像 for-in 或 for-of 这样的循环。 eslint: `no-iterator no-restricted-syntax`

>为什么？ 这是强制执行我们不变性的规则。 处理返回值的纯函数比 Side Effects(副作用) 更容易推理。使用 `map() / every() / filter() / find() / findIndex() / reduce() / some()` / … 来迭代数组, 使用 `Object.keys() / Object.values() / Object.entries() `来生成数组，以便可以迭代对象。

```
const numbers = [1, 2, 3, 4, 5];
 
// bad
let sum = 0;
for (let num of numbers) {
    sum += num;
}
sum === 15;
 
// good
let sum = 0;
numbers.forEach((num) => {
    sum += num;
});
sum === 15;
 
// best (use the functional force)
const sum = numbers.reduce((total, num) => total + num, 0);
sum === 15;
 
// bad
const increasedByOne = [];
for (let i = 0; i < numbers.length; i++) { increasedByOne.push(numbers[i] + 1); } // good const increasedByOne = []; numbers.forEach((num) => {
    increasedByOne.push(num + 1);
});
 
// best (keeping it functional)
const increasedByOne = numbers.map(num => num + 1);
```


2. 现在不要使用 generators (生成器)。

>为什么？ 因为目前没有很好地办法将他们转译成 ES5 。


<h3 name="properties">属性 Properties</h3>

1. 使用 点语法(.) 来访问对象的属性。eslint: `dot-notation`

```
const luke = {
    jedi: true,
    age: 28,
};
 
// bad
const isJedi = luke['jedi'];
 
// good
const isJedi = luke.jedi;
```

2. 当通过变量访问属性时使用中括号 []。

```
const luke = {
    jedi: true,
    age: 28,
};
 
function getProp(prop) {
    return luke[prop];
}
 
const isJedi = getProp('jedi');
```

3. 求幂时使用求幂运算符 ** 。eslint: no-restricted-properties.

```
// bad
const binary = Math.pow(2, 10);
 
// good
const binary = 2 ** 10;
```

<h3 name="variables">变量 Variables</h3>

1. 总是使用 `const` 或 `let` 来声明变量。 不这样做会导致产生全局变量。 我们希望避免污染全局命名空间。 eslint: `no-undef` `prefer-const`

```
// bad
superPower = new SuperPower();
 
// good
const superPower = new SuperPower();
```

2. 使用 const 或 let声明每个变量。 eslint: `one-var` jscs: `disallowMultipleVarDecl`
>为什么？ 以这种方式添加新的变量声明更容易，你永远不必担心是否需要将 , 换成 ;，或引入标点符号差异。您也可以在调试器中遍历每个声明，而不是一次跳过所有的变量。

```
// bad
const items = getItems(),
    goSportsTeam = true,
    dragonball = 'z';
 
// bad
// (与上面的比较，并尝试找出错误)
const items = getItems(),
    goSportsTeam = true;
    dragonball = 'z';
 
// good
const items = getItems();
const goSportsTeam = true;
const dragonball = 'z';
```

3. 将所有的 const 和 let 分组 。

>为什么？当你需要把已分配的变量分配给一个变量时非常有用。

```
// bad
let i, len, dragonball,
    items = getItems(),
    goSportsTeam = true;
 
// bad
let i;
const items = getItems();
let dragonball;
const goSportsTeam = true;
let len;
 
// good
const goSportsTeam = true;
const items = getItems();
let dragonball;
let i;
let length;
```

4. 变量不要链式赋值。eslint: `no-multi-assign`

>为什么？ 链接变量赋值会创建隐式全局变量

```
// bad
(function example() {
    // JavaScript 将其解析为
    // let a = ( b = ( c = 1 ) );
    // let关键字只适用于变量a;
    // 变量b和c变成了全局变量。
    let a = b = c = 1;
}());
 
console.log(a); // 抛出 ReferenceError（引用错误）
console.log(b); // 1
console.log(c); // 1
 
// good
(function example() {
    let a = 1;
    let b = a;
    let c = a;
}());
 
console.log(a); // 抛出 ReferenceError（引用错误）
console.log(b); // 抛出 ReferenceError（引用错误）
console.log(c); // 抛出 ReferenceError（引用错误）
 
// 同样适用于 `const`
```

<h3 name="hoisting"> 提升作用域 Hoisting</h3>

`var` 声明会被提升至他们作用域的顶部，但它们赋值不会提升。

JavaScript 代码:

```

// 我们知道这样运行不了
// （假设没有 notDefined 全局变量）
function example() {
    console.log(notDefined); // => 抛出一个 ReferenceError（引用错误）
}
 
// 在引用变量后创建变量声明
// 将因变量提升而起作用。
// 注意：赋值的 `true`没有被提升。
function example() {
    console.log(declaredButNotAssigned); // => undefined
    var declaredButNotAssigned = true;
}
 
// 解析器将变量声明提升到作用域的顶部，
????// 这意味着我们的例子可以被重写为：
function example() {
    let declaredButNotAssigned;
    console.log(declaredButNotAssigned); // => undefined
    declaredButNotAssigned = true;
}
 
// 使用 const 和 let
function example() {
    console.log(declaredButNotAssigned); // => 抛出一个 ReferenceError（引用错误）
    console.log(typeof declaredButNotAssigned); // => 抛出一个 ReferenceError（引用错误）
    const declaredButNotAssigned = true;
}
```

<h3 name="comparison-operators--equality"></h3>

1. 使用 === 和 !== 优先于 == 和 !=。 eslint: `eqeqeq`

2. 诸如 if 语句之类的条件语句使用 ToBoolean 抽象方法来强制求值它们的表达式，并始终遵循以下简单规则：
    
   - Objects 求值为 true
   - Undefined 求值为 false
   - Null 求值为 false
   - Booleans 求值为 布尔值
   - Numbers 如果是 +0、-0、或 NaN 求值为 false ， 否则为 true
   - Strings 如果是空字符串 '' 求值为 false ， 否则为 true

3. 在 `case` 和 `default` 子句中，使用大括号来创建包含词法声明的语句块(例如 `let`, `const`, `function`, 和 `class`). eslint: `no-case-declarations`
    
>为什么？ 词法声明在整个 switch 语句块中都是可见的，但是只有在分配时才被初始化，这只有当它到达 case 时才会发生。这在多个 case 子句试图定义相同的变量时会导致问题。

   ```
    //bad
       switch (foo) {
        case 1:
        let x = 1;
        break;
        case 2:
        const y = 2;
        break;
        case 3:
        function f() {
            // ...
        }
        break;
        default:
        class C {}
    }
 
    // good
    switch (foo) {
        case 1: {
        let x = 1;
        break;
        }
        case 2: {
        const y = 2;
        break;
        }
        case 3: {
        function f() {
            // ...
        }
        break;
        }
        case 4:
        bar();
        break;
        default: {
        class C {}
        }
    }
   ```
   
   
<h3 name="comments">注释 Comments</h3>

1. 多行注释使用 `/** ... */`。

2. 单行注释使用 `// ` 。将单行注释放在需注释的语句上方。在注释之前放置一个空行，除非它位于代码块的第一行。

3. 在代码一开始的地方需要标注

    ```
    / *
    * 文件名：[文件名] / 功能
    * 作者：〈版权〉
    * 描述：〈描述〉
    * 修改人：〈修改人〉
    * 修改时间：YYYY-MM-DD
    * 修改内容：〈修改内容〉
      */
    ```
    
4. 注释的标注  
 
 ```
  -  `// TODO` 用来标识需要实现的问题
  -  `// FIXME` 用来标识需要修正的问题。说明标识处代码需要修正，甚至代码是错误的不能工作
  -  `// XXX` 用来标识下面的代码虽然实现功能，但是方法有待商榷
 ```

<h3  name="naming-conventions">命名规则 Naming Conventions</h3>

1. 避免使用单字母名称
2. 当命名对象，函数和实例时候，使用驼峰式命名。
    
    ```
    // bad
    const OBJEcttsssss = {};
    const this_is_my_object = {};
    function c() {}
     
    // good
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```
    
3. 当命名构造函数或类的时候使用 `PascalCase` 式命名，（注：即单词首字母大写）。 eslint: `new-cap` jscs: requireCapitalizedConstructors

    ```
    // bad
    function user(options) {
        this.name = options.name;
    }
     
    const bad = new user({
        name: 'nope',
    });
     
    // good
    class User {
        constructor(options) {
        this.name = options.name;
        }
    }
     
    const good = new User({
        name: 'yup',
    });
    ```

<h3 name="accessors">存取器 Accessors</h3>

1. 属性的存取器函数不是必须的。
2. 別使用 JavaScript 的 getters/setters，因为它们会导致意想不到的副作用，而且很难测试，维护和理解。相反，如果要使用存取器函数，使用 getVal() 及 setVal(‘hello’)。

    ```
    // bad
    class Dragon {
        get age() {
        // ...
        }
     
        set age(value) {
        // ...
        }
    }
     
    // good
    class Dragon {
        getAge() {
        // ...
        }
     
        setAge(value) {
        // ...
        }
    }
    ```
    
3. 如果属性/方法是一个 boolean, 使用 `isVal()` 或 `hasVal()` 方法。
   
   ```
       // bad
    if (!dragon.age()) {
        return false;
    }
     
    // good
    if (!dragon.hasAge()) {
        return false;
    }
   ```
   
   
   ```
    function deepCopy(obj){
      let result = Array.isArray(obj) ? [] : {};
      const has = Object.prototype.hasOwnProperty;
      for(let key in obj){
        if(has.call(obj,key)){
          if(typeof obj[key] === 'obj'){
            result[key] = deepCopy(obj[key])
          }else{
            result[key] = obj[key]
          }
        }
      }
    
      return result;
    }
   ```
 





