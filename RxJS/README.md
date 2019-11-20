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

### rxjs定义
**stream流管理** RxJS就是解决流与流的管理，能够让我们使用语义化的操作符操作一个时间维度的流。能够表达出：这是一个1秒一次的流，这是一个只返回最新状态的流，这是两个流组合而成 这样的语义。可以说能够让我们使用非常简洁的语言就能表达出复杂逻辑执行过程的抽象执行概念封装。

### rxjs主要解决代码上的什么问题?
rx主要解决一下两种类型的问题
- "缩减乃至消除时间相关的状态量, 控制复杂度."
- "**简化** 各种复杂场景需要的实现", 譬如官网例子typeahead/指数退避策略, 譬如组织高级缓存策略, 譬如nginx httpclient实现, 譬如状态回退功能.
对代码有明显侵入改观:
- 显式逻辑化时间处理
- 数据stream化

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
- 缓存功能表达
- 更新功能表达
- 回退功能表达
- 重试功能表达

### 冷的/热的Observable

### 命名原则
1. observable命名使用名词+后缀
2. 多个值使用复数名词
3. 单个值使用单数名词
4. 可能空值使用maybe前缀
