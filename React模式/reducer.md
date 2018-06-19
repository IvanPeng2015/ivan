Redux核心设计模式：所有逻辑都集中在一个单独的reducer函数中，并且执行这些逻辑的唯一方式就是传给 *Redux* 一个能够描述当时情景的普通对象（plain object）。Redux store 调用这些逻辑函数，并传入当前的 state tree 以及这些描述对象，返回新的 state tree，接着 Redux store 便开始通知这些订阅者（subscriber）state tree 已经改变。

数据store代表整体结构的状态集，依赖于此状态集的 **发布订阅** 模式用于解耦actions和executes，将 **动作** 和 **执行** 分开处理。描述actions用的是返回普通对象的函数，不同actions之间本就是相互独立解耦的。而描述所有executes用的是一整个函数，单个action触发的对应execute在里面被限制性的封装成为函数，称为reducer，然后传给Array.prototype.reduce(reducer[, initialValue])，限定为签名（preState, action) => newState 的纯函数结构，每个reducer函数处理状态集store的一部分，reducer函数的组织严格依赖于数据存储。

这里需要明确一点，初期特别倾向于将组件模块的组织与reducer函数组织结构完全进行类似的等同，实质上，这两者之间没有任何直接关联。第一，组件模块的结构组织可以异常灵活，自由组合嵌套，组件是数据消费者，只需使用中间件订阅所需数据。第二，reducer的组织总结出以下模式，reducer是数据的生产者，每个reducer函数职责是更新整体数据的一部分。

* [Reducer和State的基本结构](#Reducer和State的基本结构)

* [拆分Reducer逻辑](#拆分Reducer逻辑)

### Reducer和State的基本结构

整个应用只有一个单一reducer。 无脑组织整个reducer的所有逻辑在单一函数的话，大概是如下典型结构的样子：
```js
// state默认值初始化initialValue
function reducer(state = initialValue, action) {
  switch (action.type) {
    case 'INCREMENT':
      return newState(state, action); // 一段逻辑处理函数
    case 'DECREMENT':
      return anotherState(state, action); //  另一段逻辑处理函数
    default:
      return state;
  }
}
```

整个应用只有也一个单一store。store数据state中的顶层状态树通常是一个Plain Object即普通对象，就顶层设计来说，组织顶层对象数据常用方式是将数据划分为子树，每个顶层key对应着和 *特定域* 或者 *切片* 相关联的数据。无脑组织整个store的所有数据在单一对象上的话，就是每一层都用划分子树的这种方式，典型结构就是JSON格式。

大多数应用会处理多种数据类型，通常可以分为以下三类（我们以redux典型example TODOs为例）：

  * 域数据（Domain data）: 应用需要展示、使用或者修改的数据（比如 从服务器检索到的列表todos）
  * 应用状态（App state）: 特定于应用某个行为的数据（比如 “Todo 5 是现在选择的状态”，或者 “正在进行一个获取 Todos 的请求”）
  * UI 状态（UI state）: 控制 UI 如何展示的数据（比如 “编写 TODO 模型的弹窗现在是展开的”）

Store 代表着应用核心，因此应该用域数据（Domain data）和应用状态数据（App state）定义 State，而不是用 UI 状态（UI state）。举个例子，state.leftPane.todoList.todos 这样的结构就是一个坏主意，因为整个应用的核心是 “todos” 而不仅仅是 UI 的一个模块。 todos 这个切片才应该是 state 结构的顶层。

UI 树和状态树之间很少有 1 对 1 的关系。除非你想明确的跟踪你的 Redux Store 中存储的 UI 数据的各个方面，但即使是这样，UI 数据的结构和域数据的结构也是不一样的。

👤认为一般的，不用全局追踪的UI状态即不存在需要组件间通信的状态数据，最好放于local state（定义数据），置于redux外部，在组件内部用setState进行更新（管理状态），由组件内部获得更好的封装。

一个典型的应用`state`大致会长这样：

```js
{
    domainData1 : {},
    domainData2 : {},
    appState1 : {},
    appState2 : {},
    ui : {
        uiState1 : {},
        uiState2 : {},
    }
}
```

### 拆分Reducer逻辑

如上所述的典型reducer函数结构，函数复杂度于`action.type`种类成正相关，由应用复杂度决定的。拆分的过程叫 *[函数分解（functional decomposition）](http://stackoverflow.com/questions/947874/what-is-functional-decomposition)* 。拆分出的短小的新的函数通常分为三类：

  * **util function** 一些小的工具函数，包含一些可重用的逻辑片段。
  * **root reducer**: 通常作为`createStore`第一个参数的函数。

  * **slice reducer**: 负责处理状态树中一块切片数据的函数，函数签名通常为`(state, action) -> newState`，通常会作为`higher-order reducer`函数的参数，单个slice reducer函数是经过higher-order reducer处理好数据切分的小任务函数。

  * **case function**: 一个负责处理特定`action`的更新逻辑的函数。可能就是一个`reducer`函数，也可能需要其他参数才能正常工作。这个函数其实很直观，理由就是一个切片函数处理的action case也会越来越多，将其中复杂的case处理简单抽出来成为单个函数；或者单个case过于复杂，按照其他参数再度进行划分。
  ```js
  // case函数查找函数，减少switch case样板代码
  function createReducer(initialState, handlers) {
    return function reducer(state = initialState, action) {
      if (handlers.hasOwnProperty(action.type)) {
        return handlers[action.type](state, action);
      } else {
        return state;
      }
    }
  }
  // Slice reducer通常如下面这样拆分为action对应的case函数，以切片state初始值和查找表为参数
  const todosReducer = createReducer([], {
    'ADD_TODO' : addTodo,
    'TOGGLE_TODO' : toggleTodo,
    'EDIT_TODO' : editTodo
  });
  ```

  * **higher-order reducer**: 一个以`reducer`函数作为参数，且/或返回一个新的`reducer`函数的函数（比如：`combineReducers`, `redux-undo`）。负责分解state的结构给各个切片，但并非每一层的状态树结构都是普通对象，当然通常我们构建的store顶层是普通对象，`combineReducers`这个函数在顶层就通常得到使用，但若每层完全按照顶层设计使用普通对象，典型应用reducer的组织结构大概是这样的：
  ```js
  const rootReducer = combineReducers({
    login,
    // anti-patterns 域数据都是数据结构的顶层
    account: combineReducers({
      schoolInfo,
      careerInfo: combineReducers({
        companyList,
        jobList,
      }),
      familyInfo,
    }),
    // 这一层有相应的切片处理
    register: (state = {}, action) => {
      switch(action.type){
        case 1: {
          return state;
        }
        // 这一层的切片case本身处理完毕，仍然要对这一层state进行普通对象的切片分解
        default: combineReducers({
          accountInfo,
          timeInfo,
        })(state, action)
      }
    },
    // 路由
    routing:routerReducer,
  });
  ```
  其实按照store数据结构给出的结论，上例的数据结构应该是扁平的，即state.account.careerInfo.companyList这样的设计没有任何意义。我们的目的设计不同的higher-order reducer函数来针对不一样的数据结构进行切片。

redux库工具函数`combineReducers`加上 **slice reducer** 配合状态树key-value结构是较为常用普遍的，更新逻辑被委托在基于普通对象结构的state切片的各个**slice reducer** 函数中。

不要将每一层的数据结构都臆想成普通对象，全局只出现了 **slice reducer** 函数以及官方提供的工具函数combineReducer。事实上，应充分利用 **higher-order reducer** **slice reducer** **case function** 三种方式拆分逻辑，才可以将混杂在各种数据结构中的rootReducer函数分解为耦合度低的各类函数。

### Highter-order reducer进阶

* redux库combineReducers函数

  应用场景：把不同片段的`state`的更新工作委托给一个特定的`reducer`，以此更新由普通的`JavaScript`对象构成的`state`树。

* [redux-immutalbe](https://github.com/gajus/redux-immutable)库combineReducers函数

  应用场景：处理Immutable.js Map对象结构的state树。

* 不同slice reducers之间共享数据

  把所需数据当额外参数的形式传递给自定义函数，这里面方法太多，较为直接、简洁的通常做法是 **通过thunk函数getState方法在触发action的前一步立即获取其其它切片reducer上的数据**。

  ❌不要通过connect获取再传参给action【导致每次此数据更新即找寻相关connect了的组件进行更新检测渲染】，除非需要特定渲染的数据。

  ❌更不要还往前一步在container方法上用props获取然后传下来。不使用combineReducers，而使用原始reducer写法解决共享数据片段更新直接打乱了整个reducer的拆分。使用combineReducers方法包裹函数解决交叉场景问题的方式，同样是打乱了切片拆分reducer的统一方式。
