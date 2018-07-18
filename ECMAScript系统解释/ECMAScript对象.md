# ECMAScript对象

勿要理解为数据结构, JS中处理逻辑较大的mistake即是将以下对象方方面面完全当做数据类型来处理.

### ECMAScript 2015-

- Object 原始对象
  - 概念:
  - 实现/优势/创建: 字面量{}

- Array
  - 优势/创建: 字面量形式[]
  - 优势: 遵守 Iterate 可迭代协议, 即提供Symbol.iterator属性
  -

### ECMAScript 2015+

- Promise
  - 定义/创建:
  ```js
  new Promise( function(resolve, reject) {...} /* executor */  );
  ```
  - [实现/细节](Promise实现.md)

- Generator

- Map/WeakMap

- Set/WeakSet

- Proxy
  - 定义/创建:
  - 拦截

- Iterable 可迭代协议
  - 概念: 允许 JavaScript 对象去定义或定制它们的迭代行为
  - 实现: Symbol.iterator属性方法
  -

- Iterator 迭代器协议
  - 概念: 定义了一种标准的方式来产生一个有限或无限序列的值
  - 实现: next方法, 用来返回序列中的下一项, 包含done属性与value属性
  - 定义/创建:
  ```js
  function makeIterator(array){
    var nextIndex = 0;
    return {
       next: function(){
           return nextIndex < array.length ?
               { value: array[nextIndex++], done: false } :
               { done: true };
       }
    };
  }
  // 一旦初始化，next()方法可以用来依次访问对象中的键值：
  var it = makeIterator(['yo', 'ya']);
  console.log(it.next().value); // 'yo'
  console.log(it.next().value); // 'ya'
  console.log(it.next().done);  // true
  ```

### 第三方辅助

- Immutable
  - 概念: 对象不可变
  - 依赖: immutable.js库

- Observable
  - 概念:
  - 依赖: rxjs库
  - Q: 刨去细节, 基本实现原理是咋样的?

### 代数结构

- Setoid

- Ord

- Semigroupoid

- Category

- Semigroup

- Monoid

- Filterable

- Functor

- Contravariant

- Apply

- Applicative

- Alt

- Plus

- Alternative

- Foldable

- Traversable

- Chain

- ChainRec

- Monad

- Extend

- Comonad

- Bifunctor

- Profunctor
