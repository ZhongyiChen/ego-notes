## 对象字面量


### 属性值简写
可以省略ES5及之前所必须的属性值和冒号。

```javascript
var listeners = [];
function listen() {}

// ES5
var events = {
  listeners: listeners,
  listen: listen,
}

// ES6
var events = {
  listeners,
  listen,
}
```


### 可计算属性名
使得对象字面量不再局限于使用静态名称声明属性。该特性不能和属性值简写共用。

```javascript
var grocery = {
  id: 'bananas',
  price: 10,
}

var groceries = {
  [grocery.id]: grocery,          // 如上所述，这里不能出现例如 `[grocery],` 的用法
}
```

```javascript
// ES5
function getEnvelope(type, description) {
  var envelope = {
    data: {}
  }
  envelope[type] = description;
  return envelope;
}

// ES6
function getEnvelope(type, description) {
  return {
    data: {},
    [type]: description,
  }
}
```


### 方法定义
省略了传统的方法定义需要带冒号和关键字 function 的情况。

```javascript
// ES5
var emitter = {
  events: {},
  on: function(type, fn) {
    if (this.events[type] === undefined) {
      this.events[type] = [];
    }
    this.event[type].push(fn);
  },
  emit: function(type, event) {
    if (this.events[type] === undefined) {
      return;
    }
    this.events[type].forEach(function(fn) {
      fn(event);
    });
  },
}

// ES6
var emitter = {
  events: {},
  on(type, fn) {
    if (this.events[type] === undefined) {
      this.events[type] = [];
    }
    this.event[type].push(fn);
  },
  emit(type, event) {
    if (this.events[type] === undefined) {
      return;
    }
    this.events[type].forEach(function(fn) {
      fn(event);
    });
  },
}
```


## 箭头函数
箭头函数看起来和匿名函数很类似。但它绝对不仅仅是省略了函数名称那么简单：箭头函数不能用作构造函数，也没有 prototype 属性(这意味着不能对它们使用 new 关键字)。另外，箭头函数会绑定到所在的**词法作用域**中，因此它们不会改变 this 的指向。


### 词法作用域
在箭头函数的函数体内，this、arguments、super 均属于所在的父级作用域。

```javascript
// ES5
function puzzle() {
  return function() {
    console.log(arguments);
  }
}
puzzle('a', 'b', 'c')(1, 2, 3);           // 1 2 3

// ES6
function puzzle() {
  return () => console.log(arguments);
}
puzzle('a', 'b', 'c')(1, 2, 3);           // "a" "b" "c"
```


### 箭头函数的写法
箭头函数有很多种写法。

```javascript
// 参数带括号，拥有函数体
var example = (parameters) => {
  return parameters;
}

// 只有一个参数时可省略圆括号
var example = value => {
  return value * 2;
}

// 只有一个参数并且只有单一语句时，可同时省略参数括号和函数体
var example = value => value * 2

// 但是，如果返回的是对象字面量，则必须在字面量外面加括号以免被当做函数体
var example = value => ({ num: value * 2 })
```


## 解构


### 对象的解构
有时候，我们会希望语法变得更加清晰。解构能很好的帮助我们实现这一点。而且，ES6支持对象深层解构的解构。

```javascript
var character = {
  name: 'Ego',
  pseudonym: 'Echo',
}

// 现在假设接下来的代码需要大量的用到 character.name 和 character.pseudonym

// ES5
var realname = character.name;            // 声明为真名，以区别于假名
var pseudonym = character.pseudonym;

// ES6
var { name: realname, pseudonym } = character;
```

在解构中，我们能够为值为 undefined 的属性解构提供默认值。当默认值与别名结合使用时，应该将别名放在前面，默认值放在后面。

```javascript
// 变量 boots 的默认值为 { size: 10 }
var { boots = { size: 10 } } = character;

// 变量 boots (别名 footwear )的默认值为 { size: 10 }
var { boots: footwear = { size: 10 } } = character
```


### 数组的解构
解构数组时可以跳过不感兴趣或不需要引用的值。

```javascript
var names = ['Echo', 'M.', 'Ego'];
var [firstname,,lastname] = names;
```

数组解构当然也可以设置默认值。
```javascript
var names = ['Echo', 'M.'];
var [firstname = 'Who',,lastname = 'Ego'] = names;
```


### 函数参数的默认值
与某些编程语言不同，我们可以为函数的任何一个参数设置默认值，而不是只能给最后一个参数设置。

```javascript
function sumOf(a = 1, b = 2, c) {
  return a + b + c;
}
console.log(sumOf(undefined, undefined, 4));    // -> 1 + 2 + 4 = 7
```


### 函数参数的解构
与只提供一个默认值相比，更好的方法是解构整个 options，并在解构模式中为每个属性指定默认值。

```javascript
function random({ min = 1, max = 10 } = {}) {
  return Math.floor(Math.random() * (max - min)) + min;
}

console.log(random());                // -> 7 (这是可能的结果)

console.log(random({ max: 24 }));     // -> 18 (这是可能的结果)
```


### 解构的用例
解构非常适用于处理返回值，包括 ajax 的返回结果以及正则表达式的匹配结果。

```javascript
// 处理函数返回结果
function getCoordinates() {
  return { x: 10, y: 22, z: -1, type: '3d' };
}
var { x, y } = getCoordinates();        // 我们只需用到 x 和 y

// 处理正则表达式
function splitDate(date) {
  var rdate = /(\d+).(\d+).(\d+)/;
  return rdate.exec(date)
}
var matches = splitDate('2019-10-24');
if (matches === null) {       // 匹配不成功时，matches 为空
  return;
}
var [, year, month, day] = matches;
```


## 剩余参数
剩余参数的引入使我们摆脱了处理任意数量的函数参数时必须用到 arguments 的尴尬。同时也摆脱了对 Array#slice.call 的依赖。

```javascript
// ES5
function join() {
  var list = Array.prototype.slice.call(arguments);
  var separator = list.shift();
  return list.join(separator);
}
join('; ', 1, 2, 3);       // -> 1; 2; 3

// ES6
function join(separator, ...list) {
  return list.join(separator);
}
join('; ', 1, 2, 3);       // -> 1; 2; 3
```

如果在箭头函数中包含剩余参数，哪怕只有一个参数，也必须放置在圆括号内。

```javascript
var sumAll = (...numbers) => numbers.reduce((total, next) => total + next)
sumAll(1, 2, 5);      // -> 8
```


## 扩展运算符
扩展运算符可以将可遍历对象转换为数组，能够在数组或函数调用中轻松展开。

```javascript
function cast() {
  return [0, ...arguments, 4];
}
cast(1, 2, 3);    // -> [0, 1, 2, 3, 4]
```

扩展运算符也可将一个字符串分割成字符数组。

```javascript
[...'Ego Echo'];    // -> ['E', 'g', 'o', ' ', 'E', 'c', 'h', 'o']
```


扩展运算符有着大量用例。
```javascript
/* 数组的连接 */

// ES5
[1, 2].concat(more);

// ES6
[1, 2, ...more];

/* 将一个数组放入列表 */

// ES5
list.push.apply(list, items);

// ES6
list.push(...items);

/* 解构 */

// ES5
a = list[0];
other = list.slice(1);

// ES6
[a, ...other] = list;

/* new 关键字和 apply 方法 */

// ES5
new (Date.bind.apply(Date,[null,2019,24,10]));

// ES6
new Date(...[2019,24,10])
```


## 模板字面量


### 标签模板
通常，Javascript中的 \ 具有特殊含义，代表转义。比如，\n 代表换行。String.raw 标签模板可以使得转义字符不进行转义。

实际上，标签模板会被解释为某个函数调用。
```javascript
// 解释前
tag`Hello, ${ name }. I am ${ emotion } to meet you!`

// 解释后
tag(
  ['Hello, ', '. I am ', ' to meet you!'],
  'Echo',
  'happy'
);
```

而 tag 的实现方法很可能如下

```javascript
function tag(parts, ...values) {
  return parts.reduce(
    (all, part, index) => all + values[index - 1] + part
  )
}
```

标签模板还有一个更有用的用法：用它来自动确保模板中插入表达式的安全性。这可以防止 XSS 攻击。

```javascript
function sanitize(str) {
  // 这里过滤掉 HTML 标签以及类似的危害
}
function sanitized(parts, ...values) {
  return parts.reduce((all, part, index) =>
    all + sanitize(values[index - 1]) + part
  )
}
var comment = 'Evil comment<iframe scr="http://evil.corp"></iframe>';
var html = sanitized`<div>${ comment }</div>`;

console.log(html);    // -> "<div>Evil comment</div>"
```


## let 声明
除了会形成块级作用域和暂时性死区，如果在 for 循环中使用 let ，则变量的作用域会封闭在循环体内。

```javascript
for (let i = 0; i < 2; i++) {
  console.log(i);     // -> 0
                      // -> 1
}
console.log(i)        // Uncaught ReferenceError: i is not defined
```


## const 声明
使用 const 的主要目的是为了放置引用改变导致程序出现问题。毕竟，这种问题非常难排查。
使用 const 声明的变量并不是不可改变的。如果想要确保值不变，可以使用 Object.freeze 这样的函数。使用 Object.freeze 可以禁止扩展传入的对象。

```javascript
// people 所指向的引用不可变，people 本身的内容是可变的
const people = ['Echo', 'Mike'];
people.push('John');
console.log(people);    // -> ["Echo", "Mike", "John"] 

// frozenPeople 的引用和内容均不可变
const frozenPeople = Object.freeze(
  ['Echo', 'Mike']
);
frozenPeople.push('John');
// -> Uncaught TypeError: Cannot add property 2, object is not extensible
```


### 使用 Object.is 进行对象比较
Object.is 方法与严格相等比较运算符 === 在大部分情况下等价。但又略有不同。

```javascript
NaN === NaN           // -> false
Object.is(NaN, NaN)   // -> true

-0 === +0             // -> true
Object.is(-0, +0)     // -> false
```


### Object.setPrototypeOf
该静态方法用于设置一个对象的原型引用为另一个对象。

```javascript
// ES5
var base = { name: 'Echo' };
var obj = Object.create(base);    // 在这里 obj 的 __proto__ 指针将指向 base
                                  // 但这个用法只适合于创建新的对象
const base = { type: 'fruit' };
const obj = { name: 'Banana' };
Object.setPrototypeOf(obj, base); // 在这里 obj 的 __proto__ 指针将指向 base
                                  // 很明显该静态方法适用于早已存在的对象
                                  // 但是，该方法非常的消耗性能。慎用！
```


##  Promise
Promise#finally的完整替代脚本如下

```javascript
Promise.prototype.finally = Promise.prototype.finally || function finally(fn) {
  const P = this.constructor;
  return this.then(
    result => P.resolve(fn()).then(() => result),
    err => P.resolve(fn()).then(() => P.reject(err))
  );
};
```


### 迭代器的原理
要转变为迭代器对象，普通对象只要遵守这么一个协议：给这个对象的 Symbol.iterator 属性赋值一个函数。

```javascript
const example = {
  [Symbol.iterator]() {
    // 这里可能有一些操作
    return {
      // 这里可能有一些操作
      next() {
        // 这里是获取 value 和 done 的各种骚操作
        return {
          value: '...',      // 序列中的当前值
          done: false,       // 或者 true 表示迭代结束
        }
      }
    }
  }
}
```


### 无穷序列
迭代器本质上是懒惰的。迭代器中的元素每次只能取出一个，即使序列是有限的。这样，我们就有办法实现无穷序列。

```javascript
// 下面实现一个遍历随机数组成的无穷序列
const infinity_random = {
  [Symbol.iterator]: () => ({
    next: () => ({ value: Math.random() })
  })
}

// 务必注意的是，对这个 infinity_random 对象
// 不能使用以下方式转换成数组
Array.from(infinity_random);       // 呀~~浏览器奔溃了 -_-
[...infinity_random];              // 呀~~浏览器又奔溃了 -_-

/**
 * 如果我们早已确定了元素的个数
 * 则可以通过解构的方式获取
 */
const [first, second] = infinity_random;

/**
 * 如果不确定元素个数，我们也可以使用 for...of 的方式迭代
 * 只是，这种方式很不直观，会导致业务代码难以理解
 */
const arr = [];
for (const value of infinity_random) {
  if (value > .8) {
    break;
  }
  arr.push(value)
}

/**
 * 为了解决上面的 for...of 迭代所带来的问题
 * 我们应当把获取元素的限制条件抽象出来作为一个独立的方法
 * 这也叫做迭代器的组合
 */
function takeIfLessThen(sequence, bound) {
  return {
    [Symbol.iterator]() {
      const s_iterator = sequence[Symbol.iterator]();
      return {
        next() {
          const item = s_iterator.next();
          if (item.value > bound) {
            return { done: true }
          }
          return item;
        }
      }
    }
  }
}

/**
 * 这样，我们就只需要在业务代码上编写直观的代码了
 */
const arr = [...takeIfLessThen(infinity_random, .8)];
```

此外，对普通对象，不应当直接向其字面量加上 Symbol.iterator 属性，而是通过接受函数处理(在函数内添加该属性)而返回一个可迭代对象。这样有利于提高代码的可读性，可迭代函数通用亦能被重复使用。


# 生成器
生成器的定义方式是先创建一个函数，调用这个函数再返回生成器对象 g 。这个 g 是一个可迭代对象，我们可以通过 `Array.from(g)`、`[...g]` 或 `for...of` 循环来使用它。生成器对象同时遵守可迭代协议和迭代器协议。

* 生成器对象 g 是通过生成器函数创建的。
* 对象 g 是一个可迭代对象，因为它有一个 Symbol.iterator 方法。
* 对象 g 也是一个迭代器，因为它有一个 .next 方法。
* g 的迭代器就是它自己。

```javascript
// 我们定义一个生成无穷斐波那契序列的生成器
function* fibonacci() {
  let previous = 0;
  let current = 1;
  while (true) {
    yield current;
    [previous, current] = [current, previous + current];
  }
}

// 可迭代对象的实现与前面生成器的功能大致相同
const fibonacci = {
  [Symbol.iterator]() {
    let previous = 0;
    let current = 1;
    return {
      next() {
        const value = current;
        [previous, current] = [current, previous + current];
        return { value, done: false }
      }
    }
  }
}

// 再次强调，可迭代对象会返回一个有 next 方法的对象，
// 生成器也会这么作。
// 上面的例子也可以像下面这样子写。效果一样的。
const fibonacci = {
  *[Symbol.iterator]() {
    let previous = 0;
    let current = 1;
    while (true) {
      yield current;
      [previous, current] = [current, previous + current];
    }
  }
}
```


# 使用生成器遍历树
基于树形数据结构的算法并不好理解。但我们可以利用表达力极强的生成器来按照自己的想法进行树节点迭代。甚至，我们可以添加节流迭代来提升我们程序的性能。

```javascript
// 定义一个 Node 类
class Node {
  constructor(value, ...children) {
    this.value = value;
    this.children = children;
  }
}

// 然后定义一个树形结构
const tree = new Node(1,
  new Node(2),
  new Node(3,
    new Node(4,
      new Node(5,
        new Node(6)
      ),
      new Node(7)
    )
  ),
  new Node(8,
    new Node(9),
    new Node(10)
  )
);

// 然后定义一个生成器函数深度优先遍历它
function* depthFirst(node) {
  yield node.value;
  for (const child of node.children) {
    yield* depthFirst(child);
  }
}

// 尝试遍历
console.log([...depthFirst(tree)]);

// 也可以通过另一种方式
// 即使用上面的 depthFirst 生成器作为迭代器
// 将 Node 转换成可迭代对象(类)
class Node {
  constructor(value, ...children) {
    this.value = value;
    this.children = children;
  }
  *[Symbol.iterator]() {
    yield this.value;
    for (const child of children) {
      yield* child;
    }
  }
}

// 按照同样的方式声明了 tree 后，遍历它
console.log([...tree]);

// 当然，也可使用宽度优先的算法
class Node {
  constructor(value, ...children) {
    this.value = value;
    this.children = children;
  }
  *[Symbol.iterator]() {
    const queue = [this];
    while (queue.length) {
      const node = queue.shift();
      yield node.value;
      queue.push(...node.children);
    }
  }
}

// 这次遍历的结果不同啦
console.log([...tree]);
```


### 使用 async/await
注意 await 关键字只能用于标记为 async 的异步函数内部。async 函数的原理与生成器类似，只不过是在局部上下文中挂起，直至 Promise 返回。如果以 await 修饰的表达式不是 Promise，那它会被转换为 Promise。

```javascript
// 首先声明一个返回 Promise 的函数
function getRandomArticle() {
  return fetch('/articles/random')
    .then(res => res.json())
}

// 然后实现我们的异步函数 read 
// 请注意：renderView(model) 也是一个异步函数
async function read() {
  try {
    const model = await getRandomArticle();
    const html = await renderView(model);
    return html;
  } catch (err) {
    console.log(err);
  }
}

// 我们可以像使用 Promise 那样使用 read()
read().then(html => console.log(html));

// 也可以用 await 实现对 read() 的连缀调用
async function write() {
  const html = await read();
  console.log(html);
}
```


### 深入理解异步函数
异步函数内部同时利用了生成器和 Promise。在生成器函数中，我们可以假设 yield 在语法上等价于 await。

```javascript
/**
 * 假设有如下异步函数
 */
async function example(a, b, c) {
  // 函数体省略
}

/**
 * 将 example 函数转换为一个简单函数
 * 该函数返回以一个生成器为参数的辅助函数
 */
function example(a, b, c) {
  return spawn(function* () {
    // 函数体省略
  });
}

/**
 * 接下来实现该辅助函数
 */
function spwan(generator) {
  // 将所有代码包装在一个 Promise 中
  return new Promise((resolve, reject) => {
    const g = generator();

    // 运行第一步
    step(() => g.next());

    function step(nextFn) {
      const next = runNext(nextFn);
      if (next.done) {
        // 成功结束，解决当前 Promise
        resolve(next.value);
        return;
      }
      // 未结束，连缀返回的 Promise 并运行下一步
      Promise
        .resolve(next.value)
        .then(
          value => step(() => g.next(value)),
          err => step(() => g.throw(err))
        );
    }

    function runNext(nextFn) {
      try{
        // 继续执行生成器代码
        return nextFn();
      } catch (err) {
        // 以失败告终，拒绝当前 Promise
        reject(err);
      }
    }
  });
}

/**
 * 接下来，我们分别看异步函数和我们实现的 spawn 函数
 * 的示例
 * 先看异步函数的示例
 */

async function exercise() {
  const r1 = await new Promise(
    resolve => setTimeout(resolve, 500, 'Echo')
  );
  const r2 = await new Promise(
    resolve => setTimeout(resolve, 200, 'Ego')
  );
  return [r1, r2];
}

exercise().then(result => console.log(result));
// -> ['Echo', 'Ego']

/**
 * 然后再把 exercise 函数转换为基于前面 spawn 的逻辑
 */
function exercise() {
  return spawn(function* () {
    const r1 = yield new Promise(
      resolve => setTimeout(resolve, 500, 'Echo')
    );
    const r2 = yield new Promise(
      resolve => setTimeout(resolve, 200, 'Ego')
    );
    return [r1, r2];
  });
}

exercise().then(result => console.log(result));
// -> ['Echo', 'Ego']
```

除了通过 yield 到 await 的转换来看待异步函数，我们还可以通过 Promise 的角度来看异步函数。

```javascript
// 有这样一个异步函数
async function getUserProfiles() {
  const users = await findAllUsers();
  const models = await Promise.all(users.map(topUserModel));
  const profiles = models.map(model => model.profile);
  return profiles;
}

// 以下代码与 getUserProfiles 异步函数大致等价
function getUserProfiles() {
  const userPromise = findAllUsers();
  const modelPromise = userPromise.then(users =>
    Promise.all(users.map(toUserModel))
  );
  const profilePromise = modelPromise.then(models =>
    models.map(model => model.profile)
  );
  return profilePromise;
}
```


### 异步迭代器
ECMAScrpit尚未完全加入该功能(处于阶段3)。异步迭代器中的协议和迭代器的稍有不同：next 返回一个 Promise，这个 Promise 解决后会返回包含 value 和 done 属性的对象。Promise 支持执行序列中的异步任务，即下一步会等上一步解决之后再执行。为避免重用 Symbol.iterator 可能造成的认知困惑，ES6 引入了 Symbol.asyncIterator，专门用于声明异步迭代器。

```javascript
// 异步迭代器的声明如下
const sequence = {
  [Symbol.asyncIterator]() {
    const items = ['E', 'c', 'h', 'o'];
    return {
      next: () => Promise.resolve(
        done: items.length === 0,
        value: items.shift()
      )
    }
  }
}

// 举个例子：创建一个
// 每隔一段时间就增加一次自己值的无穷序列
const interval = duration => ({
  [Symbol.asyncIterator]: () => ({
    i: 0,
    next() {
      return new Promise(resolve => 
        setTimeout(() => resolve({
          value: this.i++,
          done: false
        }), duration);
      );
    }
  })
});

// 为了使用这个异步迭代器，我们可以使用随同
// 异步迭代器一起引入的 for await...of 语法
async function print() {
  for await (const i of interval(1000)) {
    console.log(`${i} seconds elapsed.`)
  }
}

print();    // 每隔一段时间打印一句
```


### 异步生成器
与普通迭代器一样，异步迭代器也有对应的异步生成器。异步生成器函数与生成器函数类似，区别在于前者支持 `await` 和 `for await...of` 声明。

```javascript
// 定义一个按固定间隔周期性获取资源的生成器
async function* fectchInterval(duration, ...params) {
  for await (const i of interval(duration)) {
    yield await fetch(...params);
  }
}
```

每一步执行完后，异步生成器会返回一个签名为 { next, return, throw } 的对象，而这个对象的方法返回的 Promise 会解决为 { value, done } 形式的对象。

```javascript
// 下面给出了异步生成器的应用方法
async function process() {
  for await (const response of fetchInterval(
    1000,
    '/api/status'
  )) {
    const data = await response.json();
    // 使用更新后的数据
  }
}
```


## 集合
Javascript 数据结构很灵活。
任何对象都可以转换为散列映射(hash-map)，其中键由字符串构成，可以映射到任意值。但是，这样的数据使用方式会造成几个问题：
* 用户使用 __proto__ 、toString 或 Object.prototype 中的任意属性作为键值会产生安全问题，并会对使用这个对象造成麻烦。
* 使用 `for...in` 进行迭代需要依赖 Object#hasOwnProperty 来确保属性不是由继承而来的。
* 使用 `Object.keys(obj).forEach` 对列表进行迭代过于烦琐。
* 键仅限于字符串，而不能是 DOM 元素或其他非字符串。

第一个问题，可以通过使用前缀、使用 `Object.create(null)` 而非空对象字面量来解决。

对于迭代，我们可以在使用 `Object.create(null)` 的基础上，创建一个返回 键/值对 元组的 list 函数来解决。当然，在 ES6 中，我们还可以在散列映射上实现迭代器协议(这样我们就能通过扩展符等更简便的方式访问列表)。

ES6 集合没有键名问题，它允许使用任意值作为键。

最重要的是，我们应当使用 ES6 新引入的 Map 等内置集合，它们已经内置了上述的 ES5 构建自定义散列映射的实现细节。


### 使用 Map
必须强调的是，Map 中的键不会转换为字符串。毕竟这是 Map 最有价值的特性之一。

ES6 map 使用 SameValueZero 算法对键进行比较，其中 NaN 等于 NaN，-0 等于 +0。

```javascript
console.log(NaN === NaN);     // -> false
console.log(-0 === +0);       // -> true 
// 如果使用 Object.is 进行比较，它们都输出 false

const map = new Map();
map.set(NaN, 'one');
map.set(NaN, 'two');
map.set(-0, 'three');
map.set(+0, 'four');
console.log([...map]);
// -> [[NaN, 'two'], [0, 'four']]
```

在 ES5 中，如果我们要将给定的 DOM 元素与一个含有若干方法的 API 对象相关联，就不得不用一种冗余又低效的方式来实现。

```javascript
const map = [];

function customThing(el) {
  const mapped = findByElement(el);
  if (mapped) {
    return mapped;
  }
  const api = {
    // 自定义 API 方法
  }
  const entry = storeInMap(el, api);
  api.destory = destory.bind(null, entry);
  return api;
}

function storeInMap(el, api) {
  const entry = { el, api };
  map.push(entry);
  return entry;
}

function findByElement(query) {
  for (const { el, api } of map) {
    if (el === query) {
      return api;
    }
  }
}

function destory(entry) {
  const index = map.indexOf(entry);
  map.splice(index, 1);
}
```

但如果使用 ES6 的 Map 来实现，由于代码简化，我们不必抽象出一大堆函数：

```javascript
const map = new Map();
function customThing(el) {
  const mapped = map.get(el);
  if (mapped) {
    return mapped;
  }
  const api = {
    // 自定义 API 方法
    destory: () => map.delete(el)
  }
  map.set(el, api);
  return api;
}
```

看，多简单！


### 理解和使用 WeakMap
大部分情况下，你可以视 WeakMap 为 Map 的子集。WeakMap 中没有迭代器协议，因此 WeakMap 创建的集合不能像 Map 那样可迭代，也意味着没有 WeakMap#entries、WeakMap#keys、WeakMap#values、WeakMap#forEach 及 WeakMap#clear 等方法。
另一个区别是，Map 中的 key 可以是对象引用或其他，但 WeakMap 中的每个 key 都必须是一个对象。记住，Symbol 是一种值类型，因此也不能作为 WeakMap 的 key。
WeakMap 中的 "weak" 表示它对其键的是弱引用的。如果该对象除了用作 WeakMap 键之外而没有被其他东西引用，它就会被垃圾回收清除。Map 则相反，Map 中的键对象是强引用的，可以阻止 Map 键和值被垃圾回收清除。

```javascript
// 下面简单演示 WeakMap 的用法
const date = new Date();
const map = new WeakMap([[date, 'foo'], [() => 'bar', 'baz']]);
map.has(date);    // -> true
map.get(date);    // -> 'foo'
map.delete(date);
map.has(date);    // -> false
```

比如，如果想要保存 DOM 元素的数据并在不需要时从内存中释放，我们可以使用 WeakMap。


### 使用 Set
从某些方面来说，Set 与 Map 类似。
* Set 也是可迭代的。
* Set 构造函数也接受一个可迭代对象。
* Set 也有一个 .size 属性。(该属性是只读的)
* 与 Map 中的键一样，Set 中的值可以是任意值或对象引用。
* 与 Map 中的键一样，Set 中的值必须是唯一的。
* NaN 在 Set 中也等于 NaN。
* Set 同样拥有 .keys、.values、.entries、.forEach、.has、.delete 和 .clear 方法。

然而，
* Set 没有 键/值对 ，它只有一个维度。(可将 Set 视为元素彼此不同的数组)
* Set 中没有 .get 方法。同样，set.set(value) 方法也没有意义。
* 要将值添加到 Set 中，须用 set.add 方法。
* Set 是可迭代的。它只能迭代值。
* Set 默认的迭代器是 Set#value，而 Map 默认的迭代器为 Map#entries。
* Set#keys 方法也返回 value 的迭代器，实际上它是对 Set#values 迭代器的引用。

```javascript
// 可以看到，Set 不包含重复成员
const set = new Set(['E', 'c', 'h', 'o', ' ', 'E', 'g', 'o']);
console.log([...set]);
// -> ['E', 'c', 'h', 'o', ' ', 'g']

// Set#entries 方法为每个元素返回 [value, value] 的迭代器
const set = new Set(['a', 'b', 'c']);
console.log([...set.entries]);
// -> [['a', 'a'], ['b', 'b'], ['c', 'c']]

// Set#keys 方法与 Set#values 方法引用一致
const set = new Set(['a', 'b', 'c']);
console.log(set.keys === set.values);   // -> true
```


### ES6 WeakSet
就像 Map 和 WeakMap 的关系一样，WeakSet 是 Set 的弱版本，它无法迭代。WeakSet 中的值必须是唯一的对象引用(即不能添加字符串或符号这样的基本值)。如果 WeakSet 中的值没有其他引用，那么它将被垃圾回收。
WeakSet 只有 .add、.delete 以及检查其中是否有给定值的 .has 方法。

```javascript
const set = new WeakSet();
set.add('a');       // -> TypeError
set.add(Symbol());  // -> TypeError
```

虽然 WeakSet 本身不可迭代，但可以将迭代器传递给构造函数。什么意思呢？就是说，我们在构造 WeakSet 集合时，WeakSet 构造函数会对可迭代对象进行迭代，并将可迭代序列中的每个成员添加到该集合中。

```javascript
const set = new WeakSet([
  new Date(),
  {},
  () => {},
  [1]
]); // 把可迭代对象放入 WeakSet 构造函数中
// 每个元素最终会被添加到集合中
```

WeakSet 有着非常多的用例。

```javascript
// 确保 Car 类中的方法只能被 Car 类的实例调用
const cars = new WeakSet();
class Car {
  constructor() {
    cars.add(this);
  }
  fuelUp() {
    if (!cars.has(this)) {
      throw new TypeError('Car#fuelUp called on a non-Car!');
    }
  }
}

/**
 * 实现一个 API，打印传入对象的包括子属性在内的所有属性。
 * 该 API 同时能处理循环引用哦。
 */
// 先实现工具函数 isObject
function isObject(value) {
  return Object.prototype.toString.call(value) ===
    '[object Object]';
}
// 然后实现主体函数
function listOwnProperties(input) {
  recurse(input);
  function recurse(source, lastPrefix, seen = new WeakSet()) {
    Object.key(source).forEach(printOrRecurse);
    function printOrRecurse(key) {
      const value = source[key];
      const prefix = lastPrefix ?
        `${lastPrefix}.${key}` : key;
      const shouldRecur = (
        isObject(value) || 
        Array.isArray(value)
      );
      if (shouldRecur) {
        if (!seen.has(value)) {
          seen.add(value);
          recurse(value, prefix, seen);
        } else {  // 检测到循环引用
          console.log(`${prefix}: [circular]`);
        }
      } else {  // 检测到值是既不是对象，也不是数组
        console.log(`${prefix}: ${value}`);
      }
    }
  }
}

/**
 * 下面我们验证一下该函数
 */
const circle = { cx: 20, cy: 5, r: 15 };
circle.self = circle;
listOwnProperties({
  circle,
  numbers: [1, 5, 7],
  sum: (a, b) => a + b
});
// -> circle.cx: 20
// -> circle.cy: 5
// -> circle.r: 15
// -> circle.self: [circular]
// -> numbers.0: 1
// -> numbers.1: 5
// -> numbers.2: 7
// -> sum: (a, b) => a + b

/**
 * 其实，更常见的用法是保存 DOM 元素。
 * 当 DOM 元素被挂载到 document 中时，
 * WeakSet 可以保留着 DOM 元素的弱引用，
 * 使其能被代码感知存在。当 DOM 元素被从
 * document 树中移除时，其内存能被顺利回收。
 */
const elements = new WeakSet();
function dommy(target) {
  if (elements.has(target)) {
    return;
  }
  elements.add(target);
  // 执行工作
}
```

至于开发者想使用什么方案，可以考虑以下因素：
* 如果不需要改变已存在的数据，WeakSet 是一条可行的路。
* 如果需要保留与对象相关的数据，那么可以考虑使用 WeakMap。
* 如果只关心某个元素是否存在，那么可能需要使用 Set。
* 如果想要创建一个缓存，建议使用 Map。


## 了解代理 Proxy
Proxy 在 API 使用者和对象之间扮演了一个中间人的角色。默认情况下，代理不会做什么，事实上它什么也不做。

```javascript
const target = {};
const handler = {};
const proxy = new Proxy(target, handler);
proxy.exposed = true;
console.log(target.exposed);  // -> true
console.log(proxy.sth);       // -> undefined
```

通过添加捕获器，我们可以从不同角度拦截对 target 对象的操作。前提是这些操作必须通过 proxy 进行。我们可以通过 handler 对象的 get 捕获器来打印每次从 target 对象取得的值，或者通过 set 捕获器阻止对某些属性的写操作。


### 捕获 get 访问
我们定义一个 handler.get 捕获器来对 target 对象的每次访问进行捕获。

```javascript
const handler = {
  get(target, key) {
    console.log(`Get on property "${target}"`);
    return target[key];
  }
};
const target = {};
const proxy = new Proxy(target, handler);
proxy.numbers = [1, 1, 2, 3, 5, 8, 13];
proxy.numbers;
// 'Get on property "numbers"'
// -> [1, 1, 2, 3, 5, 8, 13]
proxy.sth;
// 'Get on property "sth"'
// -> undefined
```

作为代理的补充，ES6引入了一个内置的 Reflect 对象。ES6 代理的捕获器会一对一地映射到 Reflect 对象的 API。具体来说，每个捕获器在 Reflect 对象上都有一个对应的反射方法。
get 捕获器不一定总是返回 target[key] 的原始值。比如，借助代理并定义明确的规则来禁止访问 target 对象的某些属性，对外只暴露代理而不暴露 target 对象。这应该是代理最主要的用途。

```javascript
/**
 * 以下代码确保加了前缀 '_' 的属性都不能访问
 */
const handler = {
  get(target, key) {
    if (key.startsWith('_')) {
      throw new Error(`Property "${key}" is inaccessible`);
    }
    return Reflect.get(target, key);
  }
}
const target = {};
const proxy = new Proxy(target, handler);
proxy._secret;
// -> Uncaught Error: Property "_secret" is inaccessible
```


### 捕获 set 访问
与 get 捕获器相对，set 捕获器可以拦截属性赋值。要注意其中的 set 捕获器应该在什么情况下返回 true。返回 true 意味着给属性 key 设置 value 应该成功了。如果 set 捕获器的返回值是 false，则设置相应属性的值在严格模式下会导致抛出 TypeError，否则会静默失败。但如果使用并返回 `Reflect.set(target, key, value)`，我们就不需要自己考虑这些实现细节了。

```javascript
/**
 * 以下代码实现了"禁止访问以下划线开头的属性"
 * 前提是对 target 的访问完全由 proxy 及其捕获器接管
 */
const handler = {
  get(target, key) {
    invariant(key, 'get');
    return Reflect.get(target, key);
  }
  set(target, key, value) {
    invariant(key, 'set');
    return Reflect.set(target, key, value);
  }
}
function invariant(key, action) {
  if (key.startsWith('_')) {
    throw new Error(
      `Can't ${action} private "${key}" property`
    );
  }
}
const target = {};
const proxy = new Proxy(target, handler);
```

与 ES5 的 Object.freeze 等方法不同的是，Proxy 可以实现动态的属性私有化，即 target 所在作用域仍然可以对其私有属性进行修改(包括增删查改)。开发者或许可以给每个属性都定义 getter 和 setter 访问器，但依然不能阻止对所有属性的访问，而只能阻止那些明确配置了 getter 和 setter 的属性。


### 通过代理实现模式验证
代理可以帮助我们将验证从 target 对象中分离处理，也可以确保底层对象始终有效，不被污染。

```javascript
/**
 * 实现验证部分
 */
const validations = new Map();
const validator = {
  set(target, key, value) {
    if (validations.has(key)) {
      return validations.get(key)(value);
      // 事实上，上面的返回 true，并不会导致 target 对象被赋值成功
      // 因为这里的流程根本没有对 target 进行赋值操作
    }
    return Reflect.set(target, key, value);
  }
}
validations.set('age', validateAge);

function validateAge(value) {
  if (typeof value !== 'number' || Number.isNaN(value)) {
    throw new TypeError('Age must be a number');
  }
  if (value <= 0) {
    throw new TypeError('Age must be a positive number');
  }
  return true;
}

/**
 * 实现业务逻辑部分
 */
const person = {};
const proxy = new Proxy(person, validator);
proxy.age = 'twenty three';
// -> TypeError: Age must be a number
proxy.age = NaN;
// -> TypeError: Age must be a number
proxy.age = 0;
// -> TypeError: Age must be a positive number
proxy.age = 28;
console.log(person.age);  // -> undefined
// 为啥没能打印出来我们希望的结果？
// 请参考 validator.set 的实现
```


## 可撤销代理
相比于 Proxy 对象，可撤销代理提供了更加细粒度的控制。可撤销代理不使用 new 关键字，并且返回的是一个 { proxy, revoke } 对象(而不仅仅是一个 proxy 对象)。调用 revoke() 后，任何操作都会导致 proxy 抛出错误。这很有用，例如：要是使用者企图多次访问某个私有属性，则立即撤销其对 proxy 的访问权。

```javascript
/**
 * 下面的代码演示了如何彻底收回使用者访问代理的权限
 */
const target = {};
const handler = {};
const { proxy, revoke } = Proxy.revocable(target, handler);
proxy.isUsable = true;
console.log(proxy.isUsable);  // -> true
revoke();
revoke();
revoke();   // 看！无论怎么调用它，都没有反应
console.log(proxy.isUsable);
// -> TypeError: illegal operation attempted on a revoked proxy
```


## 代理捕获器
代理最强大的地方主要体现在其各种捕获器上。


### has 捕获器
我们可以使用 handler.has 对 in 操作符隐藏任意属性。以下是三种应对属性窥探者的策略：
* 什么也不做，此时 `key in proxy` 会转换为 `Reflect.has(target, key)`，等价于 `key in target`。
* 无论 key 是否存在于 target 中，都返回 true 或 false 值。
* 抛出错误以表明此时 in 操作符是非法的。(此地无银。极不推荐)

```javascript
/**
 * 最好的做法是通过返回 false 来告诉不速之客，
 * 他们访问的属性并不在(in)对象中。
 */
const target = {
  _secret: 'Echo',
  wellKnow: 'Publicly-known'
};
const handler = {
  has(target, key) {
    if (key.startsWith('_')) {
      return false;
    }
    return Reflect.has(target, key);
  }
};
const proxy = new Proxy(target, handler);
console.log('wellKnow' in proxy);
// -> true
console.log('_secret' in proxy);
// -> false
console.log('_secret' in target);
// -> true (这里绕过了代理)
```

注意，要想阻止 Object#hasOwnProperty('_secret')，has 捕获器可帮不上忙。


### deleteProperty 捕获器
将某个属性设置为 undefined 会清除它的值，但属性仍在。但像 `delete cat.furBall` 这样使用 delete 操作符则会将属性从 cat 对象上删除。
经过前面的学习，我们不能修改 _secret 属性的值，甚至无法通过 in 操作符获悉它是否存在，但还是可以通过 proxy 对象用 delete 操作符删除该属性！
我们可以使用 handler.deleteProperty 来删除操作。

```javascript
// 编写一个阻止使用者删除私有属性的 handler
const handler = {
  deleteProperty(target, key) {
    if (key.startsWith('_')) {
      throw new Error(
        `Can't delete private ${key}`
      );
    }
    return Reflect.deleteProperty(target, key);
  }
}
```


### defineProperty 捕获器
ES5 中的 Object.defineProperty 用于给对象添加属性。`Object.defineProperty(target, key, descriptor)` 通常用于以下两种情况：
* 希望 getter 和 setter 得到跨浏览器的支持。
* 希望实现自定义的属性访问器。

手工添加的属性是可读的、可写的、可删的、可枚举的。而通过 Object.defineProperty 添加的属性默认是只读的、不可删除的、不可枚举的。(这点跟 const 声明很类似：都是只读的，但并非不可变)

使用 Object.defineProperty 定义属性时，可以通过属性描述符自定义以下方面：
* configurable = false 禁用对属性描述符的大多数改变，同时使属性不可删除。
* enumerable = false 对 for...in 循环和 Object.keys 隐藏当前属性。
* writable = false 让属性只读
* value = undefined 是当前属性的初始值
* get = undefined 是作为属性 getter 的方法
* set = undefined 是作为属性 setter 的方法，它接受一个新 value，然后更新属性的 value。
此外，开发者必须选择是配置 value 和 writable (配置一个数据描述符)，还是配置 get 和 set (配置一个访问器描述符)。因此，像 `obj.a = 1` 这样的操作，也会得到一个数据描述符哦(该描述符的 writable 属性为 true)。

```javascript
/**
 * 我们看一下
 * 一般赋值后的属性描述符
 * 和使用 Object.defineProperty
 * 定义属性的属性描述符
 */
const obj = {};
obj.a = 1;
Object.defineProperty(obj, 'b', {
  value: 2
});
console.log(
  Object.getOwnPropertyDescriptor(obj, 'a')
);
// {
//   value: 1,
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
console.log(
  Object.getOwnPropertyDescriptor(obj, 'b')
);
// {
//   value: 2,
//   writable: false,
//   enumerable: false,
//   configurable: false
// }
```

handler.defineProperty 捕获器用于拦截要定义的属性。它既可拦截声明式定义 `obj.a = 1`，也可拦截 API 调用 `Object.defineProperty(obj, 'b', { value: 2 })`，跟 Object.defineProperty 一样，该捕获器的第一个参数是 target 对象，第二个参数是属性 key，第三个参数是描述符 descriptor。

```javascript
/**
 * 声明捕获器：不允许代理对象定义
 * 以下划线开头的属性
 */
const handler = {
  defineProperty(target, key, descriptor) {
    if (key.startsWith('_')) {
      return false;
    }
    return Reflect.defineProperty(
      target, key, descriptor
    );
  }
}
const target = {};
const proxy = new Proxy(target, handler);
proxy.a = 1;
proxy._b = 2;  // 在非严格模式下静默失败
// 在严格模式下，抛出以下错误
// -> TypeError: 'defineProperty' 
// on proxy: trap returned false
```


### ownKeys 捕获器
handler.ownKeys 方法可以返回一个属性数组(这数组也是 Reflect.ownKeys() 的结果)。数组中应该包含 target 对象的所有属性：可枚举的、不可枚举的，以及符号属性。ownKeys 捕获器与其他属性遍历器的区别如下：
* `Reflect.ownKeys()` 返回对象上所有自己的键。
* `Object.getOwnPropertyNames()` 只返回非符号属性。
* `Object.getOwnPropertySymbols()` 只返回符号属性。
* `Object.keys()` 只返回非符号可枚举属性。
* `for...in` 只返回非符号可枚举属性。
要想阻止访问带有前缀的属性时，我们可以先取得 `Reflect.ownKeys(target)` 的输出，然后从中过滤掉带有前缀的属性即可。

```javascript
const handler = {
  ownKeys(target) {
    return Reflect.ownKeys(target).filter(key => {
      const isStrKey = typeof key === 'string';
      if (isStrKey) {
        // 过滤掉带有 '_' 前缀的键
        return !key.startsWith('_');
      }
      return true;  // 如果键不是字符串，默认通过
    });
  }
}
const target = {
  [Symbol('id')]: 'Echo',
  _secret: 'Ego',
  fruits: ['apple', 'banana'],
};
const proxy = new Proxy(target, handler);
for (const key of Object.keys(proxy)) {
  console.log(key);
}
// -> 'fruits'
// 这里之所以没有打印 -> Symbol(id)
// 是因为 Object.keys 在返回结果前过滤了 Symbol 属性
// 而不是被我们 ownKeys 捕获器过滤的
```


## 高级代理捕获器
前面介绍的捕获器，都是与属性的访问和操作相关。后面介绍的捕获器，主要是与要代理的的对象本身相关。


### getOwnPropertyDescriptor 捕获器
为了配合前面隐藏 target 对象的私有属性的做法，我们最好在使用者访问私有属性的描述符时，返回 undefined。

```javascript
const handler = {
  getOwnPropertyDescriptor(target, key) {
    if (key.startsWith('_')) {
      return;
    }
    return Reflect.getOwnPropertyDescriptor(
      target, key
    );
  }
}
const target = {
  _a: 'Echo',
  b: 'Ego',
}
const proxy = new Proxy(target, handler);
console.log(
  Object.getOwnPropertyDescriptor(proxy, 'c');
);
// -> undefined
console.log(
  Object.getOwnPropertyDescriptor(proxy, '_a');
);
// -> undefined
console.log(
  Object.getOwnPropertyDescriptor(proxy, 'b');
);
// {
//   value: 'Ego',
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```

此外，getOwnPropertyDescriptor 捕获器可以拦截 Object#hasOwnProperty 的实现，后者依赖属性描述符来确认属性是否存在。

```javascript
// 接上个例子的代码：
console.log(
  proxy.hasOwnProperty('b');
);    // -> true
console.log(
  proxy.hasOwnProperty('_a');
);    // -> false
```


### apply 捕获器
apply 捕获器有意思的地方在于，它是专门为函数量身定制的。当代理的 target 函数被调用时，apply 捕获器就会触发。
apply 捕获器接受三个参数：
* target 是被代理的函数
* ctx 是函数被调用时作为 target 中 this 值的上下文对象。
* args 是一个函数被调用时传给 target 的数组。

```javascript
const twice = {
  apply(target, ctx, args) {
    // 每当函数 target 调用后，将其结果 * 2 再
    // 返回给表达式
    return Reflect.apply(target, ctx, args) * 2;
  }
}
function sum(a, b) {
  return a + b;
}
const proxy = new Proxy(sum, twice);
console.log(proxy(1, 2)); // -> 6
```

设想这么一种情况：我们希望某个对象 obj 内的方法被调用时，其 this 永远指向该对象 obj，怎么办呢？
在 ES5 中，我们可以利用 `obj.fn = obj.fn.bind(obj)` 来实现我们的设想。但问题在于，如果该 obj 内有很多函数都有这个要求，那我们就得一一为它们添加以上代码。这很不利于维护。
参考我们前面学到的 handler.get ，我们可以自定义 get 捕获器，在捕获器内部，如果发现要获取的 value 是函数类型，就执行 `return value.bind(target)`，同样可以达到我们的目的。唯一的问题在于，经过这么处理后，每次返回的都是一个新的函数。即代码可能出现如下情况：

```javascript
console.log(
  proxy.fn !=== proxy.fn
);    // -> true
```

当然，这个引用不一致的问题，我们可以通过添加缓存例如 WeakMap 来解决。
最后，别指望用 apply 捕获器来解决我们现在的问题，因为它同样需要缓存来配合，使得 apply 本来的语义变得更为抽象。


### construct 捕获器
construct 捕获器拦截的是 new 操作符的调用。construct 捕获器最常见的用例是重组构造器的参数或做一些本应由构造器来做的事，比如记录日志或记录创建的对象实例。

```javascript
// 两个效果一致的捕获器
const handler = {
  construct(Target, args) {
    return new Target(...args);
  }
}

const handler = {
  construct(Target, args) {
    return Reflect.construct(Target, args);
  }
}
```

有这么一种情况：由于种种原因，我们无法直接修改某个类，比如代码不是我们编写的，或者已经有别的代码依赖于当前的对象结构。但我们确实有业务需要为该类增加一个新属性。这时，我们就应当使用 construct 捕获器。

```javascript
const handler = {
  construct(Target, args) {
    const [name] = args;
    const target = Reflect.construct(Target, args);
    target.name = name;
    return target;
  }
}
class Target {
  hello() {
    console.log(`Hello, ${this.name}!`);
  }
}

// 旧的业务代码
const target = new Target();
target.name = 'Echo';
target.hello(); // -> 'Hello, Echo!'

// 新的业务代码
const ProxiedTarget = new Proxy(Target, handler);
const proxy = new ProxiedTarget('Echo');
proxy.hello(); // -> 'Hello, Echo!'
```


### getPrototypeOf 捕获器
我们可以使用 handler.getPrototypeOf 方法作为捕获器来拦截以下所有操作。
* 访问 Object#__proto__ 属性。
* 调用 Object#isPrototypeOf 方法。
* 调用 Object#getPrototypeOf 方法。
* 调用 Reflect.getPrototypeOf 方法。
* 使用 instanceof 操作符。
强大吧？我们可以通过它动态地指定要报告的底层对象的原型。比如，我们可以使用这个捕获器让某个对象在通过代理访问时被当成 Array。

```javascript
const handler = {
  getPrototypeOf: target => Array.prototype
}
const target = {};
const proxy = new Proxy(target, handler);
console.log(proxy instanceof Array);
// -> true

/**
 * 但 proxy 并不能算是数组
 * 因为 proxy 的原型指针并没有
 * 关联 Array.prototype
 */
console.log(proxy.push);
// -> undefined
```

但我们可以继续用 get 捕获器来"弥补"这个不足呀。

```javascript
const handler = {
  getPrototypeOf: target => Array.prototype,
  get(target, key) {
    return (
      Reflect.get(target, key) ||
      Reflect.get(Array.prototype, key)
    );
  }
}
const target = {};
const proxy = new Proxy(target, handler);
console.log(proxy.push);
// -> function push() {[native code]}
proxy.push('first', 'second');
console.log(proxy);
// -> { 0: 'first', 1: 'second', length: 2 }
```


### setPrototypeOf 捕获器
该捕获器与 getPrototypeOf 捕获器相对。ES6 有一个 Object.setPrototypeOf 方法，用于将一个对象的原型设置成另一个对象(ES6 之前，在浏览器中是以对象的特殊属性 __proto__ 为设置入口哦，已废弃)。

```javascript
const handler = {
  setPrototypeOf(target, proto) {
    Reflect.setPrototypeOf(target, proto);
    // 等价于 Object.setPrototypeOf(target, proto)
  }
}
const base = {};
function Target() {}
const proxy = new Proxy(Target, handler);
proxy.setPrototypeOf(proxy, base);
console.log(proxy.prototype === base);
// -> true
```

setPrototypeOf 捕获器比较典型的使用场景是：我们需要一个空方法体，然后通过捕获器"蒸发"对 Object.setPrototypeOf 的调用，也就是什么都不做。又或者开发者想阻止用户修改被代理对象的原型，然后告知用户操作失败。


### preventExtensions 捕获器
我们可以用 handler.preventExtensions 来捕获 ES5 新增的 Object.preventExtensions 方法。被阻止扩展后，对象就不能新增属性了，即不允许扩展对象。
例如，我们希望可以选择性地阻止一些对象的扩展，而不是全部阻止。此时可以用 WeakSet 来保存可扩展对象。如果对象不在这个集合中，那么 preventExtensions 可以捕获相应的请求，然后就地丢弃它们。

```javascript
const canExtend = new WeakSet();
const handler = {
  preventExtensions(target) {
    const canPrevent = !canExtend.has(target);
    if (canPrevent) {
      Object.preventExtensions(target);
    }
    return Reflect.preventExtensions(target);
  }
}
const target = {};
const proxy = new Proxy(target, handler);

/**
 * 先尝试一下把 target 放入 canExtend 中
 */
canExtend.add(target);
Object.preventExtensions(proxy);
// trap returned falsish ???
// 不知为啥，根本无效

/**
 * 然后再把 target 从 canExtend 中移除
 */
canExtend.delete(target);
Object.preventExtensions(proxy);
// Proxy {}
console.log(Object.isExtensible(proxy));
// -> false
```


### isExtensible 捕获器
可扩展对象就是可以为其添加属性的对象。
handler.isExtensible 方法可用于记录或监督对 Object.isExtensible 的调用，但不能左右对象是否可扩展的事实。这个捕获器被施加了严格的控制：如果 `Object.isExtensible(proxy) !== Object.isExtensible(target)`，则抛出 TypeError。
这个捕获器最大的作用，可能就是不想让用户知道底层对象是否可扩展了。


## Number


### 二进制和八进制字面量
在 ES5 及以前，要涉及非二进制时，最好的做法是将其传入 parseInt 中：`parseInt('101', 2) === 5`。另外，在 ES3 中，parseInt 将以 0 开头的数字字符串视为一个八进制数值(因此，官方在 ES5 中将 parseInt 的默认基数指定为十，并且考虑到向后兼容，建议开发者最好明确指定 parseInt 的基数)。
现在，有了 ES6，我们可以像 Java 那样用前缀表示二进制和八进制的数字了：
* 0b010 二进制 === 2
* 0B010 二进制 === 2
* 0o010 八进制 === 8
* 0O010 八进制 === 8
* 0x010 十六进制 === 16
* 0X010 十六进制 === 16


### Number.isNaN
与全局的 isNaN 几乎相同。不同之处在于，全局的 isNaN 会对参数进行类型转换。因此，对于 Number.isNaN 而言，只有传进去的是 NaN，才会返回 true。
但上面两个方法都没有回答某个值是否是数字这个问题。为了解答这个问题：

```javascript
// 这是判断一个值是否为数字的完整方法
function isNumber(value) {
  return typeof value === 'number' &&
    !Number.isNaN(value);
}
```


### Number.isFinite
和前面的情况相同，全局函数 isFinite 会先对参数进行类型转换。因此，使用 Number.isFinite 更安全。如果希望强制转换参数类型，你应当主动这么做：`Number.isFinite(Number(value))`。

```javascript
Number.isFinite(NaN);         // -> false
Number.isFinite(Infinity);    // -> false
Number.isFinite(-Infinity);   // -> false
Number.isFinite(null);        // -> false
Number.isFinite(-13);         // -> true
Number.isFinite('10');        // -> false
```


### Number.parseInt
其工作原理与 parseInt 相同。事实上，它们就是相同的。对于非十进制数的字符串，ES6 引入了一种新的不一致性：parseInt 可以解析 0x，但不能解析 0b 和 0o。

```javascript
parseInt('0xf00');      // -> 3840
parseInt('0xf00', 16);  // -> 3840
parseInt('0xf00', 10);  // -> 0
parseInt('5xf00', 10);  // -> 5
parseInt('0b011');      // -> 0
parseInt('0b011', 2);   // -> 0
parseInt('0o100');      // -> 0
parseInt('0o100', 8);   // -> 0
```

相反，Number 函数完全可以将这些字符串转换为正确的数字。

```javascript
Number('0b011');        // -> 3
Number('0o110');        // -> 72
```


### Number.parseFloat
与 parseInt 一样，parseFloat 没有做任何修改就添加到 Number 中了。比较幸运的是，parseFloat 不会对十六进制字符串进行任何特殊转换。

```javascript
parseFloat('010');    // 10
parseFloat('0x10');   // 0
parseFloat('0b10');   // 0
parseFloat('0o10');   // 0
```


### Number.isInteger
这是 ES6 的一种新方法(即 ES5 及之前是不存在 isInteger 这个全局函数的)。如果提供的 value 是没有小数部分的有限数字(不能是数字字符串)，则 Number.isInteger 方法返回 true。


### Number.EPSILON
EPSILON 属性是 Number 内置中新添加的常数值。可以用 Number.EPSILON 来判断这个误差是否小到可以忽略不计。Number.EPSILON 表示浮点运算舍入操作的安全误差范围。

```javascript
/**
 * 我们构造一个判断浮点运算
 * 是否在预期的误差范围内
 * 的函数
 */
function withinMarginOfError(left, right) {
  // 这里用了 Math.abs，这样我们就不必担心
  // left 和 right 的位置问题了
  return Math.abs(
    left - right
  ) < Number.EPSILON;
}

withinMarginOfError(0.1 + 0.2, 0.3);
// -> true
```


### Number.MAX_SAFE_INTEGER 和 Number.MIN_SAFE_INTER
Number.MAX_SAFE_INTEGER 是 Javascript 中可以安全且精确表示的最大整数，或者是 IEEE754 标准规定的能用浮点表示整数的任何语言可以表示的最大整数。Number.MIN_SAFE_INTER 当然就是相对应的最小负值啦。
由于这两者是整数的边界，因此不建议利用此两者进行算术运算。

```javascript
1 === 2;
// -> false

var max_num = Number.MAX_SAFE_INTEGER;
max_num + 1 === max_num + 2;
// -> true

var min_num = Number.MIN_SAFE_INTEGER;
min_num - 1 === min_num - 2;
// -> true
```


### Number.isSafeInteger
对于 [MIN_SAFE_INTER, MAX_SAFE_INTEGER] 范围内的任何整数，此方法都会返回 true。与 ES6 引入的其他 Number 方法一样，该方法不涉及任何类型的强制转换。
要想验证一个操作的结果是否在界限内，我们不仅要验证结果，还要验证这两个操作数。

```javascript
/**
 * 我们可以看到，以下的减法操作会产生一个
 * 范围内的结果，但由于某个操作数并不在
 * 范围内。因此，其结果是不准确的。
 */
Number.isSafeInteger(9007199254740003 - 990);
// -> true

Number.isSafeInteger(990);
// -> true

Number.isSafeInteger(9007199254740003);
// -> false
```

除了上面这种情况，还包括甚至两个操作数都不在界限内，但其产生的结果在范围内(这个结果当然也是不准确的)的情况。对此，我们可以声明一个函数来确保所有操作数(包括 result)都在安全范围内。

```javascript
function safeOp(result, ...operands) {
  const values = [result, ...operands];
  if (
    !values.every(Number.isSafeInteger)
  ) {
    throw new RangeError(
      'Operation cannot be trusted!'
    );
  }
  return result;
}

safeOp(1 + 2, 1, 2);
// -> 3

safeOp(
  9007199254740003 - 990,
  9007199254740003,
  990
);
// -> RangeError: 
// -> Operation cannot be trusted!
```


## Math
ES6 为 Math 内置引入了很多新的静态方法。其中一些方法是专门为使 C语言 更容易编译成 Javascript 而设计的(你很少需要将它们用于日常的 Javascript 应用开发)。虽然引入 Number 内置的方法不会通过 Number(value) 进行类型转换，但添加到 Math 中的大多数方法是具有这个特性的哦。


### Math.sign
返回一个矢量(-1、0 或 1)来表示所提供的输入的符号。跟其他语言不同的是，Javascript 还可能返回 -0 和 NaN。

```javascript
Math.sign(1);     // -> 1
Math.sign(0);     // -> 0
Math.sign(-0);    // -> -0
Math.sign(-30);   // -> -1
Math.sign(NaN);   // -> NaN
Math.sign('one'); // -> NaN
Math.sign('-0');  // -0
Math.sign('7');   // -> 1
```


### Math.trunc
Math.trunc 是作为对 Math.floor 和 Math.ceil 方法的替代方案。不同的是，该方法会舍弃小数部分但不进行舍入。

```javascript
Math.trunc(12.3456);  // -> 12
Math.trunc(-13.58);   // -> -13
Math.trunc(-0.123);   // -> -0
Math.trunc(NaN);      // -> NaN
Math.trunc('one');    // -> NaN
Math.trunc('123.456');// -> 123
```


### Math.cbrt
即立方根(类似于 Math.sqrt 是"平方根"的缩写)。

```javascript
Math.cbrt(-1);    // -> -1
Math.cbrt(27);    // -> 3
Math.cbrt('one'); // -> NaN
Math.cbrt('8');   // -> 2
```


### Math.expm1
约相当于 e 的 value 次幂的结果减 1。但是，Math.expm1 比单纯的 Math.exp(value) - 1 计算具有更高的精确度。Math.expm1 的逆函数是 Math.log1p。

```javascript
function exmp1(value) {
  return Math.exp(value) - 1;
}

expm1(1e-20);
// -> 0
Math.expm1(1e-20);
// -> 1e-20

expm1(1e-10);
// -> 1.000000082740371e-10
Math.expm1(1e-10);
// -> 1.00000000005e-10
```


### Math.log1p
这是 value + 1 的自然对数————ln(value + 1)————也是 Math.expm1 的逆函数。在 Javascript 中，一个数字的自然对数可以用 Math.log 表示。与 Math.expm1 一样，Math.log1p 方法比手动执行 Math.log(value + 1) 操作更精确。

```javascript
function log1p(value) {
  return Math.log(value + 1);
}

log1p(1.00000000005e-10);
// -> 1.000000082690371e-10

Math.log1p(1.00000000005e-10);
// -> 1e-10
// 这就是 Math.expm1(1e-10)
// 的逆运算
```


### Math.log10
以 10 为底的 log10(value) 的对数。

```javascript
Math.log10(1000); // -> 3
```


### Math.log2
以 2 为底的 log2(value) 的对数。

```javascript
Math.log2(1024); // -> 10
```


### 三角函数
ES6 的 Math 对象添加了新的三角函数：
* `Math.sinh(value)` 返回 value 的双曲正弦值；
* `Math.cosh(value)` 返回 value 的双曲余弦值；
* `Math.tanh(value)` 返回 value 的双曲正切值；
* `Math.asinh(value)` 返回 value 的反曲正弦值；
* `Math.acosh(value)` 返回 value 的反曲余弦值；
* `Math.atanh(value)` 返回 value 的反曲正切值。


### Math.hypot
它返回每个参数的平方和的平方根。

```javascript
Math.hypot(1, 2, 3);
// -> 3.741657386773941
// 即 (1*1 + 2*2 + 3*3) 的平方根
```


### 按位计算助手
以下的 Math 的新方法是专门为将 C语言 更轻松地编译为 Javascript 而设计的。它们用于处理 32 位数字：
* Math.clz32 计算二进制表示的 32 位数字中的前导零位。
* Math.imul 返回 类C语言 中的 32 位乘法的结果。
* Math.fround 将 value 舍入到最接近的 32 位浮点型表示的数字。

```javascript
Math.clz32(0);      // -> 32
Math.clz32(1);      // -> 31
Math.clz32(1 << 1); // -> 30
Math.clz32(1 << 2); // -> 29
Math.clz32(1 << 29);// -> 2
Math.clz32(1 << 31);// -> 0
```


## 字符串 和 Unicode


### String#startsWith
该方法用于避免 String#indexOf 给业务代码带来不必要的复杂性。

```javascript
// ES5
'Echo Ego'.indexOf('Echo') === 0;
// -> true
'Echo Ego'.indexOf('Ego', 5) === 5;
// -> true

// ES6
'Echo Ego'.startsWith('Echo');
// -> true
'Echo Ego'.startsWith('Ego', 5);
// -> true
```


### String#endsWith
这个方法是 String#startsWith 的镜像，正如 String#lastIndexOf 是 String#indexOf 的镜像。与 String#startsWith 相对，本方法也有一个索引值，用于指示查找 value 应该结束的位置。

```javascript
'Echo Ego'.endsWith('Ego');
// -> true
'Echo Ego'.endsWith('Echo');
// -> false
'Echo Ego'.endsWith('Echo', 4);
// -> true
```


### String#includes
该方法用于确定一个字符串是否包含另一个字符串。你还可以为该方法提供索引值，搜索将从该索引值开始。

```javascript
// ES5
'Echo Ego'.indexOf('cho') !== -1;
// -> true

// ES6
'Echo Ego'.includes('cho');
// -> true

'Echo Ego'.includes('cho', 4);
// -> false
```


### String#repeat
这种方法允许你将一个字符串重复 count 次。注意，提供的 count 应该是一个非负的有限数字，如果其范围在 [-1, -Infinity)，或者是 Infinity，则会抛出错误；如果 count 是小数值，则向下取整到最近的整数( (-1, 0) 范围内的值会四舍五入到 -0，即 0)；使用 NaN 时，count 会被解析为 0；非数字值则会被强制转换为数字(如果被转换为 NaN，则再进一步转换为 0)。

```javascript
'ha'.repeat(1);
// -> "ha"
'ha'.repeat(2);
// -> "haha"
'ha'.repeat(0);
// -> ""
```


### 字符串填充
这就是 ES6 新增的 String#padStart 和 String#padEnd。开发者需要为这两个函数指定完成填充后的字符串总长度，默认的填充字符是空格符 ' '。如果原始字符串长于或等长于指定长度，则这两个方法什么也不做并返回原始字符串。

```javascript
'01.23'.padStart(5);
// -> "01.23"
'1.23'.padStart(5);
// -> " 1.23"
'1.23'.padStart(5, '0');
// -> "01.23"
'1.23'.padStart(7, '0');
// -> "0001.23"
'1.23'.padStart(7, 'abcdef');
// -> "abc1.23"

'01.23'.padEnd(5);
// -> "01.23"
'1.23'.padEnd(5);
// -> "1.23 "
'1.23'.padEnd(5, '0');
// -> "1.230"
'1.23'.padEnd(7, '0');
// -> "1.23000"
'1.23'.padEnd(7, 'abcdef');
// -> "1.23abc"
```


### 去空白
这是两个未完全纳入标准的方法：String#trimStart 和 String#trimEnd。前者用于删除字符串开头的任何空格，后于用于删除字符串末尾的任何空格。

```javascript
'  Echo Ego  '.trimStart();
// -> "Echo Ego  "
'  Echo Ego  '.trimEnd();
// -> "  Echo Ego"
```


## Unicode
Javascript 字符串可以用 UTF-16 代码单元表示。每个代码单元用于表示 [U+0000, U+FFFF] 范围中的代码点。对于超出 U+FFFF 的代码点，你可以用代理对表示它们，也就是两个连续的代码单元。例如，马表情(🐎)的代码点可以用 '\ud83d\udc0e' 的连续代码单元来表示。在 ES6 中，你也可以用 '\u{1f40e}' 符号表示代码点————也是马表情符号。

```javascript
'\ud83d\udc0e';   // -> "🐎"
'\ud83d\udc71';   // -> "👱"
'\u2764';         // -> "❤"
'🐎👱❤'.length; // -> 5
```

这就非常尴尬了，我们肯定不能用普通的 for 循环来遍历这些标签符号。好在 ES6 中的字符串遵循迭代协议，我们可以用字符串迭代器来遍历代码点，即使这些代码点是由代理对构成的。

```javascript
for (const codePoint of '🐎👱❤') {
  console.log(codePoint);
}
// -> 🐎
// -> 👱
// -> ❤

[...'🐎👱❤'].length;  // -> 3
```

但是，想要对字符串长度进行 100% 的精确计算，将字符串拆分为代码点是不够的。例如，'\u0305' 代表一个上划线字符，当它与 Unicode 代码单元进行组合，就会合成一个带有上划线的 Unicode 字符。

```javascript
'\u0305';        // -> "̅"

function overlined(ch) {
  return `${ch}\u0305`;
}

overlined('o');  // -> "o̅"

'o̅'.length;      // -> 2
[...'o̅'].length; // -> 2
```

由于这个世界上有着各种各样的字符群集，Javascript 字符串迭代器尚未都支持。因此，我们运气不好的时候，甚至需要使用正则表达式或实用工具库来计算正确的字符串长度。


### String#codePointAt
该方法用来获取字符串中给定位置的代码点的数字表示。

```javascript
const txt = '\ud83d\udc0e\ud83d\udc71\u2764';

txt.codePointAt(0);   // -> 0x1f40e
txt.codePointAt(2);   // -> 0x1f471
txt.codePointAt(4);   // -> 0x2764

/**
 * 在正式的代码中，我们当然不可能
 * 知道每个代码单元的其实索引，因此
 * 我们应当用一个字符串迭代器来识别
 * 它们。
 */
for (const codePoint of txt) {
  console.log(codePoint.codePointAt(0));
}
// -> 0x1f40e
// -> 0x1f471
// -> 0x2764

/**
 * 我们还可以用扩展运算符和
 * Array#map 的组合简化实现。
 */
[...txt].map(c => c.codePointAt(0));
// -> [0x1f40e, 0x1f471, 0x2764]
```

然后我们可以将这些 base-16 的值包含在 '\u{codePoint}' 中，并再次获得表情符号。


### String.fromCodePoint
该方法接收一个数字并返回一个代码点。

```javascript
/**
 * 基于 base-16 代码点
 */
String.fromCodePoint(0x1f40e);
// -> "🐎"
String.fromCodePoint(0x1f471);
// -> "👱"
String.fromCodePoint(0x2764);
// -> "❤"

/**
 * 基于 base-10 字面量
 */
String.fromCodePoint(128014);
// -> "🐎"
String.fromCodePoint(128113);
// -> "👱"
String.fromCodePoint(10084);
// -> "❤"

/**
 * 甚至可以打印一串表情符号
 */
String.fromCodePoint(0x1f40e, 0x1f471, 0x2764);
// -> "🐎👱❤"
```


### Unicode-Aware 字符串反转
跟前面的 Unicode 小节一样，我们需要先借迭代器协议将字符串按代码点拆分，然后将其颠倒，才能正确地反转字符串。

```javascript
const txt = '\ud83d\udc0e\ud83d\udc71\u2764';

txt.split('')
  .map(c => c.codePointAt(0));
// -> [55357, 56334, 55357, 56433, 10084]
txt.split('')
  .reverse()
  .map(c => c.codePointAt(0));
// -> [10084, 56433, 55357, 56334, 55357]
// 这样的结果，将我们的代码点拆散了，明显不正确

[...txt].reverse().join('');
// -> "❤👱🐎"
// 这才是正确的结果
```

同样的，这样的代码也是不适用于所有字形集群的。


### String#normalize
有些字符串的代码点不同，可看上去却是完全一样的。原因是有一些字符即可以由单个代码单元表示，又可以由其他字符与合成字符组合而成。

```javascript
'\u00f1';       // -> "ñ"
'\u006e\u0303'; // -> "ñ"
'ñ' === 'ñ';    // -> false
'ñ'.length;     // -> 1
'ñ'.length;     // -> 2
```

可以看到，同样是 "ñ" 的样子，既可以来自 "ñ" 这单个字符单元形成的代码点，又可以来自 "n" 和 "̃ " 组合而成的代码点。
如果用 String#normalize 对第二个版本进行标准化，那么将换回与第一个版本中相同的代码点。

```javascript
var a = 'ñ';
var b = 'ñ';

a === b;
// -> false

a.normalize() === b.normalize();
// -> true
```


## 正则表达式


### 粘连修饰符 /y
ES6 引入了它。该修饰符与全局修饰符 g 相似。相同在于，它们都通常用于多次匹配，直到输入字符串耗尽为止。但是，粘连正则表达式会将 lastIndex 移到最后一次匹配的位置(即粘连正则表达式必须确保匹配从剩余的第一个位置开始，而全局正则表达式只要确保剩余位置存在匹配即可)。

```javascript
function matcher(regex, input) {
  return () => {
    const match = regex.exec(input);
    const lastIndex = regex.lastIndex;
    return { lastIndex, match };
  }
}
const input = 'haha haha haha';

const nextGlobal = matcher(/ha/g, input);
console.log(nextGlobal());
// -> { lastIndex: 2, match: ['ha'] }
console.log(nextGlobal());
// -> { lastIndex: 4, match: ['ha'] }
console.log(nextGlobal());
// -> { lastIndex: 7, match: ['ha'] }

const nextSticky = matcher(/ha/y, input);
console.log(nextSticky());
// -> { lastIndex: 2, match: ['ha'] }
console.log(nextSticky());
// -> { lastIndex: 4, match: ['ha'] }
console.log(nextSticky());
// -> { lastIndex: 0, match: null }
```

第三次调用 nextSticky() 之所以匹配失败了，是因为这次剩余的字符为 " haha haha"，而粘连正则表达式的特性导致了剩余字符串的第一个空格字符 ' ' 不可能被匹配成功。
其实，粘连匹配被添加到 Javascript 的原因是主要为了提高编译器中的词法分析器性能哦。


### Unicode 修饰符 /u
这个修饰符可以视为更严格版的正则表达式。

```javascript
/\a/.test('ab');  // -> true
// 这里的 a 明明已经被转义了，
// 但程序仍然判定匹配成功

/\a/u.test('ab');
// -> SyntaxError:
// Invalid escape: /\a/

/**
 * 需要匹配 ES6 新引入的
 * Unicode 字符表达法表示的
 * 字符串时，须加上 u 修饰符
 */
/\u{1f40e}/.test('🐎');
// -> false
/\u{1f40e}/u.test('🐎');
// -> true
```

在不使用 u 修饰符时，. 模式可以匹配除终止符以外的任何 BMP符号。但无法匹配不在 BMP 中的 Unicode 符号。使用 u 修饰符后，则可以整个匹配 Unicode 代码点，而不是仅匹配其中第一个代码单元。

```javascript
// 不使用 u 修饰符
const rdot = /^.$/;
rdot.test('a');         // -> true
rdot.test('\n');        // -> false
rdot.test('\u{1d11e}'); // -> fasle

// 使用 u 修饰符
const rdot = /^.$/u;
rdot.test('a');         // -> true
rdot.test('\n');        // -> false
rdot.test('\u{1d11e}'); // -> true
```


### 具名捕获组
如今，Javascript 正则表达式可以在编号捕获组和非捕获组中对匹配进行分组。要注意的是，在编写笔记的此刻，具名捕获组提案还处于阶段 3。因此，它尚不适用于使用广泛的产品上。

```javascript
// ES5
function parseKeyValue(input) {
  const r = /([a-z]+)=([a-z]+)/;
  const [, key, value] = r.exec(input);
  return { key, value };
}
parseKeyValue('echo=ego');
// -> { key: 'echo', value: 'ego' }

// ES6
function parseKeyValue(input) {
  const r = (
    /(?<key>[a-z]+)(?:=|\sis\s)(?<value>[a-z]+)/
  );
  const { groups } = r.exec(input);
  return groups;
}
parseKeyValue('echo=ego');
// -> { key: 'echo', value: 'ego' }
parseKeyValue('k is v');
// -> { key: 'k', value: 'v' }
```

我们知道，Javascript 正则表达式支持反向引用，其中捕获组可以复用于查找重复项。具名捕获组提案增加了对具名反向引用的支持，以反向引用具名捕获组。

```javascript
// ES5 支持的反向引用
function isSame(input) {
  const r = /([^:]+):\1/;
  return r.exec(input) !== null;
}
isSame('root:root');   // -> true;
isSame('ro:root');     // -> true;
isSame('roots:root');  // -> false;

// ES6 支持的具名反向引用
function hasSame(input) {
  const r = /(?<user>[^:]+):\k<user>/u;
  return r.exec(input) !== null;
}
hasSame('root:root');   // -> true;
hasSame('ro:root');     // -> true;
hasSame('roots:root');  // -> false;
```

`\k<groupName>` 引用可以与编号引用串联使用。但是，使用具名引用时最好避免使用编号引用。
最后，具名组可以传递给 String#replace 的替换中直接被引用。

```javascript
// 将美国日期格式替换为匈牙利格式
function changeFormat(input) {
  const r_usa = (
    /(?<month>\d{2})\/(?<day>\d{2})\/(?<year>\d{4})/
  );
  const hungarian = input.replace(
    r_usa,
    '$<year>-$<month>-$<day>'
  );
  return hungarian;
}
changeFormat('10/30/2019');
// -> "2019-10-30"
```


### Unicode 属性转义
Unicode 属性转义描述的是一种新的转义序列，可用于标有 u 修饰符的正则表达式。该提案截至目前处于阶段3。Unicode 标准为每个符号都定义了属性。有了这些属性，我们就可以对 Unicode 字符进行高级查询了。

```javascript
// 一个匹配希腊文 Unicode 符号的函数
function isGreekSymbol(input) {
  const r = /^\p{Script=Greek}$/u;
  return r.test(input);
}

// 一个匹配非希腊文 Unicode 符号的函数
function isNonGreekSymbol(input) {
  const r = /^\P{Script=Greek}$/u;
  return r.test(input);
}

isGreekSymbol('π');
// -> true

isNonGreekSymbol('π');
// -> false
```

p 是 pattern 的缩写。大写的 P 是 p 的反模式，即不匹配大括号内的属性的值时命中。


### 后行断言
JavaScript 一直支持先行断言。该功能允许我们匹配一个表达式，前提是后面跟着另一个表达式。这里的另一个表达式的模式为 (?=...)。无论另一个表达式(先行断言)是否匹配，另一个表达式匹配的结果都将被丢弃，并且不会消耗输入字符串的字符(在多次匹配或全局匹配的情况下)。

```javascript
/**
 * 使用先行断言的正则表达式
 * (?=...)
 */
function getJsFilename(input) {
  // 表达式还使用了具名捕获组哦
  const r = 
    /^(?<filename>[a-z]+)(?=\.js)\.[a-z]+$/u;
  const match = r.exec(input);
  if (match === null) {
    return null;
  }
  return match.groups.filename;
}
getJsFilename('index.js');  // -> index
getJsFilename('index.php'); // -> null

/**
 * 使用先行否定断言的正则表达式
 * (?!...)
 */
function getNonJsFilename(input) {
  // 表达式还使用了具名捕获组哦
  const r = 
    /^(?<filename>[a-z]+)(?!\.js)\.[a-z]+$/u;
  const match = r.exec(input);
  if (match === null) {
    return null;
  }
  return match.groups.filename;
}
getNonJsFilename('index.js');  // -> null
getNonJsFilename('index.php'); // -> index
```

后行断言提案(阶段3)同时引入了肯定和否定的后行断言，分别用 (?<=...) 和 (?<!...) 表示。这些断言可以用于确认我们想要的匹配模式是否在另一个给定模式之后。如果要测试该内容，请注意大部分浏览器尚不支持后行断言正则表达式。

```javascript
/**
 * 使用后行断言的正则表达式
 * (?=...)
 */
function getDollarAmount(input) {
  // 表达式还使用了具名捕获组哦
  const r = 
    /^(?<=\$)(?<amount>\d+(?:\.\d+)?)$/u;
  const match = r.exec(input);
  if (match === null) {
    return null;
  }
  return match.groups.amount;
}
getDollarAmount('$12.34'); // -> 12.34
getDollarAmount('€12.34'); // -> null

/**
 * 使用后行否定断言的正则表达式
 * (?=...)
 */
function getNonDollarAmount(input) {
  // 表达式还使用了具名捕获组哦
  const r = 
    /^(?<!\$)(?<amount>\d+(?:\.\d+)?)$/u;
  const match = r.exec(input);
  if (match === null) {
    return null;
  }
  return match.groups.amount;
}
getNonDollarAmount('$12.34'); // -> null
getNonDollarAmount('€12.34'); // -> 12.34
```


### 新的 /s (dotAll) 修饰符
在使用 . 时，我们通常希望匹配每个独立字符。然而，JavaScript 中的 . 表达式不匹配星状字符(这点可以通过添加 u 修饰符来解决)或行终止符。dotAll 提案(阶段3)添加了 s 修饰符，该修饰符使得 Javascript 正则表达式中的 . 可以匹配任何独立字符。

```javascript
const r = /^.$/;
r.test('a');    // -> true
r.test('\t');   // -> true
r.test('\n');   // -> false

/**
 * 在 ES5 中，开发者会使用这样
 * 的变通方案
 */
const r = /^[\s\S]$/;
r.test('a');    // -> true
r.test('\t');   // -> true
r.test('\n');   // -> true

/**
 * 在 dotAll 中，只要在表达式中
 * 添加 s 修饰符，即可用 . 匹配
 * 任何字符
 */
const r = /^.$/s;
r.test('a');    // -> true
r.test('\t');   // -> true
r.test('\n');   // -> true
```


### String#matchAll
通常来说，当使用全局或粘连修饰符的正则表达式时，我们希望遍历每个匹配捕获组的集合。但目前为止，仍需要我们自己实现一个迭代器来实现：在循环中用 String#match 或 RegExp#exec 来收集捕获组，直到正则表达式与 lastIndex 不匹配为止。

```javascript
/**
 * 该函数定义 lastIndex 的目的，是为了
 * 确保再次调用生成器函数不会导致表达式
 * 的 lastIndex 没有指向字符串的开头。
 */
function* parseAttributes(input) {
  let lastIndex = 0;
  const r = /(\w+)="([^"]+)"\s/ig;
  while (true) {
    r.lastIndex = lastIndex;
    const match = r.exec(input);
    if (match === null) {
      break;
    }
    lastIndex = r.lastIndex;
    r.lastIndex = 0;
    const [, key, value] = match;
    yield [key, value];
  }
}
const html = '<input t="y" p="h" />';
console.log(...parseAttributes(html));
// ['t', 'y']
// ['p', 'h']
```

String#matchAll 提议(阶段1)为字符串原型引入了一种新的方法，除了返回的迭代器是一系列 match 对象而不只是编号捕获。这意味着我们可以通过 match.groups 访问命名捕获序列中的每个 match。

```javascript
const r = 
  /(?<key>\w+)="(?<value>[^"]+)"\s/igu;
const html = '<input t="y" p="h" />';
for (const match of html.matchAll(r)) {
  const {
    groups: {
      key,
      value
    }
  } = match;
  console.log(`${key}: ${value}`);
}
// t: y
// p: h
```


## Array
多年以来，在谈及数组时，Underscore 和 Lodash 这样的库都高调抱怨其缺失的特性(一方面也是为了营销自己嘛)。为此，ES5 为数组添加了很多功能方法：Array#filter、Array#map、Array#reduce、Array#reduceRight、Array#forEach、Array#some 和 Array#every。
现在 ES6 又新增了一些有助于操作、填充和过滤数组的方法。

### Array.from
通过前面的学习，我们已经明白，我们可以利用扩展运算符来将定义了迭代器协议的对象转换为数组。另外，新的 DOM 标准将 NodeList 升级为可迭代的，从而使得开发者能通过扩展运算符来扩展 NodeList DOM 元素集合。在新版的 jQuery 中，jQuery 对象以及实现了迭代器协议，因此开发者也可以扩展 jQuery 对象(旧版的 jQuery 无效，会报告异常)。

```javascript
[...document.querySelectorAll('div')];
// -> [<div>, <div>, <div>, ...]

/**
 * 以下操作只在新版的 jQuery 中支持
 */
[...$('div')];
// -> [<div>, <div>, <div>, ...]
```

新的 Array.from 方法有些不同。它不会依赖迭代器协议从对象中提取值，与扩展运算符不同的是，它支持任何类数组对象。

```javascript
/**
 * 以下操作在任何版本的 jQuery 中都支持
 */
Array.from($('div'));
// -> [<div>, <div>, <div>, ...]
```

Array.from 有 3 个参数，但只有 input 这个参数是必需的：
* input，你想要扩展的类数组对象或可迭代对象；
* map，应用于 input 的每个元素的映射函数；
* context，调用 map 时的 this 绑定。

```javascript
function typesOf() {
  return Array.from(
    arguments,
    value => typeof value
  );
}
typesOf(null, [], NaN);
// -> ['object', 'object', 'number']

/**
 * 前面只是为了展示 Array.from 的用法，
 * 为了使代码更清晰，我们应当把业务代码
 * 整理如下。
 */
function typesOf(...all) {
  return all.map(v => typeof v);
}
typesOf(null, [], NaN);
// -> ['object', 'object', 'number']
```


### Array.of
Array.of 的 ponyfill 如下：

```javascript
function arrayOf(...items) {
  return items;
}
```

Array.of 的出现，目的就是为了解决 Array 构造函数存在的问题。使用 new 操作符调用 Array 构造函数时，如果参数只有一个而且是整数，就会出现一些很奇怪的现象：如果这唯一的参数是正整数 N，则会生成一个含有 N 个 undefined 的数组；如果这唯一的参数是负整数，则直接报错。

```javascript
/**
 * Array 构造函数
 */
new Array();
// -> []
new Array(undefined);
// -> [undefined]
new Array(1);
// -> [undefined x 1]
new Array(3);
// -> [undefined x 3]
new Array('3');
// -> ['3']
new Array(1, 2);
// -> [1, 2]
new Array(-1, -2);
// -> [-1, -2]
new Array(-1);
// -> RangeError: 
//    Invalid array length

/**
 * Array.of 方法
 */
Array.of();
// -> []
Array.of(undefined);
// -> [undefined]
Array.of(1);
// -> [1]
Array.of(3);
// -> [3]
Array.of('3');
// -> ['3']
Array.of(1, 2);
// -> [1, 2]
Array.of(-1, -2);
// -> [-1, -2]
Array.of(-1);
// -> [-1]
```


### Array#copyWithin
Array#copyWithin 方法将数组中的若干成员复制到该数组的 target 位置，被复制的成员是该数组中 [start, end) 这一区间内的元素。Array#copyWithin 方法返回数组本身。

```javascript
// 原型方法
Array.prototpe.copyWithin(
  target,
  start = 0,
  end = this.length
);
```

```javascript
// 用例
const items = [1, 2, 3, , , , , , , , ];
items.copyWithin(6, 1, 3);
// -> [1, 2, 3, , , , 2, 3, , , ];
```


### Array#fill
这种方便且实用的方法可以用提供的 value (可以是任何类型的值)替换数组中的所有成员。
但必须注意，在稀疏数组中，这个 value 将填充空位并替换现有成员。
另外，别指望该方法会执行作为其参数的函数，它只会安静地将 value 塞到数组的各个位置而已

```javascript
['a', 'b', 'c'].fill('x');
// -> ['x', 'x', 'x']

new Array(3).fill('x');
// -> ['x', 'x', 'x']

/**
 * 你也可以指定其实索引和结束
 * 索引(不会填充结束索引的位置)
 */
['a', 'b', 'c', , ,].fill('x', 2);
// -> ['a', 'b', 'x', 'x', 'x']

new Array(5).fill('x', 0, 1);
// -> ['x', undefined x 4]
```


### Array#find 和 Array#findIndex
Array#find 方法为数组中的每个成员执行 callback 函数，直到有成员返回 true 为止，然后返回该成员。该方法遵循的签名是 (callback(item, i, array), context)。你可以认为 Array#find 是一个返回匹配元素(而不是 true)的 Array#some。

```javascript
['a', 'b', 'c', 'd'].find(
  item => item === 'c'
);
// -> "c"

['a', 'b', 'c', 'd'].find(
  item => item === 'z'
);
// -> undefined
```

Array#findIndex 使用的签名与 Array#find 相同。不同的是，Array#findIndex 返回匹配元素的索引，而不是一个布尔值或元素本身。当没有匹配成功时，它的返回值为 -1。

```javascript
['a', 'b', 'c', 'd'].findIndex(
  item => item === 'c'
);
// -> 2

['a', 'b', 'c', 'd'].findIndex(
  item => item === 'z'
);
// -> -1
```


### Array#keys
Array#keys 返回一个迭代器，该迭代器产生一个保存数组键的序列。返回值是一个迭代器，这意味着你可以使用 for...of、扩展运算符或手动调用 .next() 来迭代它。

```javascript
['a', 'b', 'c'].keys();
// -> ArrayIterator {}
```

与 Object.keys 以及大多数迭代数组的方法不同，该序列不会忽略数组中的空位。

```javascript
Object.keys(new Array(4));
// -> []

[...new Array(4).keys()]
// -> [0, 1, 2, 3]
```


### Array#values
Array#values 和 Array#keys 相同，但返回的迭代器是一系列值而不是键。


### Array#entries
Array#entries 与前面两个方法类似，但其返回值是含有一系列 键/值对 的迭代器。

```javascript
[...['a', 'b', 'c'].entries()];
// -> [[0, 'a'], [1, 'b'], [2, 'c']]
```


### Array.prototype[Symbol.iterator]
这个方法与 Array#values 完全相同。


## 模块
略


## 风格建议
略