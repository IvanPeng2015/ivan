### 为什么要用TS, 凭什么要用TS

- 1. 引入静态类型, 编译期类型推断, 提前提示.
- 2. 可读性, 可维护, IDE支持.

缺陷
- 1. 丧失灵活性
- 2. 报错

### 有哪些核心原则?

- 对值所具有的结构进行类型检查. [鸭式辩型法]

### 基本问题

- 引入
  - 三斜线
  - @types 仓库
  - 命名空间
  - decalre 声明

- 基础类型
  - boolean
  - number
  - string
  - litera types 字面量类型
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
  - Function 重载

- 高级类型
  - 索引类型 典型场景表达http params类型
    - 什么是字符串索引签名?
    - 索引类型的目的是什么?
    - 使用了什么操作符? keyof T 索引查询操作符
    - 索引访问操作符使用条件? 1. 泛型 2. 使用T[K]得确保K extends keyof T
  - 联合类型
    - 联合类型的推断
  - 交叉类型
  - 泛型
  - 映射类型
    - 映射类型用途是什么?
  - type alias, 即type声明语句, 类型别名, interface的所有功能都能用type声明来等价.
    - 工具类
  - infer 表示在 extends 条件语句中待推断的类型变量。
    - type ParamType<T> = T extends (param: infer P) => any ? P : T;


- 类型设计
  - 类型断言 as操作符, 覆盖类型推断.
  - 类型保护
    - typeof操作符
    - instanceof操作符
    - in操作符
    - Discriminating联合类型: 根据其字面量类型属性来区分联合类型内部的不同类型. 典型场景, 区分reducer接收到的action, 依赖type字面量
    - 自定义类型保护函数: https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards
    - nullable types的类型保护和类型推断, 纯靠if/else语句
  - 类型兼容性
  - 逆变/协变/双向协变/不变

- Effective TS
  - item 9 类型声明(:操作符)优于类型断言(as操作符)
  - item 10 string和String, number和Number, boolean和Boolean是不同的类型. 避免使用object wrapper类型
  - item 11  

- TS编译原理
  - Scanner 扫描器
  - Praser 解析器
  - Binder 绑定器
  - Checker 检查器
  - Emitter 发射器

  源码 ~ 扫描器 ~> token流 ~~ 解析器 ~~> AST抽象语法树 ~~ 绑定器 ~~> 符号

  扫描出token并解析到ast语法, 纯编译原理
  - AST
    -  遍历抽象语法树
