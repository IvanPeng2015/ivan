### 有哪些核心原则?

- 对值所具有的结构进行类型检查. [鸭式辩型法]

### 基本问题

- 基础类型
  - boolean 字面量 true/false
  - number 字面量数字
  - string 字面量引号
  - Symbol
  - null or undefined 字面量null 和 undefined, 没有声明(主动)场景
  - array 字面量中括号
  - Tuple 元组类型表达了概念 -> 1.特定length的数组 2.每个元素类型独立. 并非特意使用场景, 常见于一般性的类型推导的传导过程.
  - enum 消除一组魔幻变量 1.数字枚举 2.字符串枚举
  - any 放弃TS
  - void 同null和undefined一样没有主动场景, 比较常见的是函数没有显示return, noop: () => void
  - unknown
  - never 表达概念: 永不出现值的类型 1. 函数永远抛异常的返回值类型 2.函数类似while死循环的返回值类型 3.变量类型(声明, 设计如下)
    - never类型是所有类型的子类型, 可以赋值给所有类型, 反之则不成立
    - Variables also acquire the type never when narrowed by any type guards that can never be true. 永不为真的的类型约束可以将变量类型收缩到never类型
    - 典型示例1: type Exclude<T, U> = T extends U ? never : T;
    - 典型示例2: type Extract<T, U> = T extends U ? T : never;
  - Object 非原始类型
  - class
  - interface 鸭式辩型
  - Function


- 高级类型
  - 索引类型的目的是什么? 使用了什么操作符? 索引访问操作符使用条件?
  - 什么是字符串索引签名?
  - 映射类型用途是什么?  
