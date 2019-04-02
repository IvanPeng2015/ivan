### rxjs基础概念
- 哪些东西是需要 **异步** 处理的?
  - 定义: 一个 JavaScript 运行时包含了一个待处理的消息队列. 每一个消息都关联着一个用以处理这个消息的函数。凡是丢到消息队列的的都是异步处理的内容.
  - 回调API(NodeJS)调用消息: 回调函数
  - Promise API调用消息: then等处理函数
  - UI交互消息: 事件监听函数
  - 定时器消息: 回调函数
  - 内置对象事件监听消息 (image.onload, websocket.connect, xmlhttprequest): 监听函数
  - 手动添加消息(addEventListenner/EventEmitter): 事件函数
- 哪些东西是适合/要用observable处理的?
- 为什么需要 pipeable 操作符?
- observable与promise
  - 创建与订阅, promise new出来即执行, , 而observable new出来是不执行的, subscribe时才执行.
  - promise是描述将来/某个时刻的值原语, Observable是
- 操作符的用法从哪里体现/查看?
  - typescript 单元测试文件
- 在外层Observable内打平一个Observable, mergeMap, concatMap, switchMap, 这些, 与在一个Observable内订阅其余的Observable, 有什么区别? 作用是什么?
- 设计
  - 1. iterable pattern可遍历模式的API设计，比如observer.next(value)
  - 2. observer pattern观察者模式的API设计，比如subscribe。
  - 3. functional programming比如内部数据处理的copy API，pure function入参减少side effects.还有个复杂点在于operator。其实也就是个结合`chaining`而形成的function而已，比如简单的`map operator`就是`observer.next(map(value))`。但是在rxjs体系下，开发者看不到这一层而已
- 缓存功能表达
- 更新功能表达
- 回退功能表达
- 重试功能表达

### rxjs主要解决代码上的什么问题?
rx主要解决一下两种类型的问题
- "缩减乃至消除时间相关的状态量, 控制复杂度."
- "**简化** 各种复杂场景需要的实现", 譬如官网例子typeahead/指数退避策略, 譬如组织高级缓存策略, 譬如nginx httpclient实现, 譬如状态回退功能.
对代码有明显侵入改观:
- 显式逻辑化时间处理
- 数据stream化

**stream流管理** RxJS就是解决流与流的管理，能够让我们使用语义化的操作符操作一个时间维度的流。能够表达出：这是一个1秒一次的流，这是一个只返回最新状态的流，这是两个流组合而成 这样的语义。可以说能够让我们使用非常简洁的语言就能表达出复杂逻辑执行过程的抽象执行概念封装。

### rxjs学习
- 前期: 文档
- 中前期: rxjs源码, Github源码路线图?
- 中期: 编写弹珠测试, 哪儿入手如何编写弹珠测试?
- 后期: 参与rxjs项目, 开发提供复杂工具.

### rxjs设计数据层主要面临什么问题?
- 数据来源: Restful API请求得到的promise数据, websocket等长链接得到的事件数据, 各种storage本地存储数据, 用户交互数据.
  - 每种来源都对应有处理复杂数据逻辑. 无法简单的做到数据隔离, 以及数据的存储更新与获取的隔离;
  - 数据依赖关系复杂. 同步与异步交叉的依赖关系.
  - 数据缓存怎么做? 数据实时性问题怎么解决, 数据一致性怎么保证;
- 怎么解决状态依赖问题?
  - 这个状态流依赖的层级追溯中, 不仅包含有源头的click/tap/touch等事件流, 同时肯定包含某些操作或者请求的状态
- 怎么解决stream分支的问题? 如何判定解决分支问题不影响其它订阅?

### 操作符
- **合并行为** mergeMap, concatMap, merge, mergeAll, concat, concatAll, exhaustMap, mergeMapTo, switchMap讲述了什么区别?
  - 基本的合并函数, 参数为Observable
    - merge: 无序
    - concat: 按序
    - zip: 批次收集Observable值
    - combineLatest: 每个值都寻找最近值组合成数组
  - 高阶合并操作符, 在基本的合并操作符后加All处理, `将高阶Observable的所有内部Observable都组合起来`
    - mergeAll: 对产出的下游Observable作为参数进行merge操作
    - concatAll: 对产出的下游Observable作为参数进行concat操作
    - zipAll: 上游Observable complete才能确定下游Observable的个数, 此时才可以对N个Observable做zip操作.
    - combineAll: 同需要上游Observable complete. 最终Observable 的value值数组的元素个数依赖于上游Observable的总共产出值个数.
  - 高阶合并映射操作符, 组合基本的函数与高阶合并操作符步骤, 操作符是纯值的关系处理与传递.
    - mergeMap = map + mergeAll
    - concatMap = map + concatAll
  - 进化的高阶处理处理: concatAll操作符, 上游高阶Observable每产生一个数据都会`生出`一个下游Observable, 当上游产出速度过快, 超过下游Observable complete的速度, 就会产生`背压`.
    - switch: 总是切换到最新的内部Observable对象获取数据. `每个switch的上游高阶Observable对象产生一个内部Observable对象，switch都会立刻订阅最新的内部Observable，如果已经订阅了之前的内部Observable对象，就会退订那个过时的内部Observable对象。`
    - exhaust: 在耗尽当前内部Observable的数据之前不会切换到下一个内部Observable对象. `exhaust的策略和switch相反，当内部Observable对象在时间上发生重叠时，情景就是前一个内部Observable还没有完结，而新的Observable又已经产生，到底应该选择哪个作为数据源？switch选择新产生的内部Observable对象，exhaust则选择前一个内部Observable对象`
  - 其余的基本合并函数
    - race: 映射第一个发出值的Observable
    - forkJoin: 收集每个observable都完成组成的值
    - pairs: 收集对象的[key, value]对
  - 其余的基本合并操作符
    - startWith: 往前插入
    - endWith: complete后插入
    - withLatestFrom: 还提供另一个 observable 的最新值
    - timeoutWith: 超时
    - pairwise: 收集Observable前后两个值组成数组

- timeout机理? 如何实现超时重复请求?

### 怎么去获取Subject或者Observable的最新单个值?
If you're using getValue() you're doing something imperative in declarative paradigm. It's there as an escape hatch, but 99.9% of the time you should NOT use getValue(). There are a few interesting things that getValue() will do: It will throw an error if the subject has been unsubscribed, it will prevent you from getting a value if the subject is dead because it's errored, etc. But, again, it's there as an escape hatch for rare circumstances.

There are several ways of getting the latest value from a Subject or Observable in a "Rx-y" way:

1. Using BehaviorSubject: But actually subscribing to it. When you first subscribe to BehaviorSubject it will synchronously send the previous value it received or was initialized with.
2. Using a ReplaySubject(N): This will cache N values and replay them to new subscribers.
3. A.withLatestFrom(B): Use this operator to get the most recent value from observable B when observable A emits. Will give you both values in an array [a, b].
4. A.combineLatest(B): Use this operator to get the most recent values from A and B every time either A or B emits. Will give you both values in an array.
5. shareReplay(): Makes an Observable multicast through a ReplaySubject, but allows you to retry the observable on error. (Basically it gives you that promise-y caching behavior).
6. publishReplay(), publishBehavior(initialValue), multicast(subject: BehaviorSubject | ReplaySubject), etc: Other operators that leverage BehaviorSubject and ReplaySubject. Different flavors of the same thing, they basically multicast the source observable by funneling all notifications through a subject. You need to call connect() to subscribe to the source with the subject.

### 实际使用需要解决什么问题
- NodeJS一类的单次调用异步API, callback API, 用Observable转换后, 怎么进行优雅的处理链式调用?

### 冷的/热的Observable
