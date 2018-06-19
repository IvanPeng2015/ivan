# [React模式](.md)/Presentational and container components

将组件构成拆分为presentational和container两个不同的level负责不同层次的职责，并不是技术维度有区别，而是目的维度有区别，所以他们遵从的规则并不是确定死的。这种拆分能解决如下问题：

- 组件状态定义在组件内部，由组件方法进行改变，系统中其它部分的依赖此数据的组件将如何共享它？

presentational组件遵从以下规则：

- 只关心UI怎么展示
- 可能在内部包含presentational或者container，而且通常有一些DOM元素和样式
- 常允许包含有`props.children`
- 不依赖于应用上其他的状态，比如actions或者store
- 不指定数据如何加载或者改变
- 仅仅通过props接收数据和回调函数
- 极少情况会有自己的state，有也仅仅是UI状态，而不是data，指的是UI存在与业务无关交互
- 除非组件内包含state，生命周期，或者性能优化，否则写成functional组件
- 示例: Page, Sidebar, Story, UserInfo, List.

container组件遵从以下规则：

- 只关系things如何work
- 可能会内部包含presentational或者container，通常不会有DOM元素和样式，顶多有个包裹的div
- 向其他presentational或者container组件提供数据和方法；
- 调用connect HOC组件提供的method，并以回调函数的形式向presentational组件提供
- 常为有状态的，倾向于提供数据源
- 常用HOC生成，比如react-redux库的connect函数
- 示例: UserPage, FollowersSidebar, StoryContainer, FollowedUserList.

益处：

- 更好的职责划分，应用业务和UI更清晰
- 复用性上，用不同的数据源的传给同一presentational组件，这样就可以在不同的container得到复用
- presentational组件完全于应用业务逻辑隔离，整体上用于测试，UI检查
- 强制抽象出布局组件，使用`props.children`代替重复在多个container中都使用相同的markup和布局

更多对立描述：

- stateful & stateless
- classes & functions

以上组件层次的区分限制在视图框架库内部，延伸到状态管理，实现上比如redux库，rxjs库，mobx库，要决定组件状态管理权放在哪里？状态数据放置于状态库管理的规则：

- 凡是由服务器数据决定的UI显示的状态，涉及到使用服务器数据来判定的UI状态；
