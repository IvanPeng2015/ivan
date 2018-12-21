# React 模式

📖React开发过程中用到的设计模式／技巧

## react库本身遵从一些思路

- react basic theoretical concepts

  基本的库的设计遵从一系列准则。💾GitHub🔗：<https://github.com/reactjs/react-basic。>

- [Virtual DOM](虚拟DOM.md)

- setState function

- [React设计](React设计.md)

## react应用组件模式

- JavaScript data to JSX

- 生命周期

- controlled component vs uncontrolled component

## react拆分组件模式

react的核心实质是UIs即data到另一结构data的映射，在Javascript中去理解，就是固定输入得到固定输出的的纯函数。整个页面实质上是一个单一复杂的UIs对应的纯函数。因此如何拆解整个函数，划分模块、实现功能的独立开发就是一项主要的工作。

要明白的很重要的一点是，并不是定好了每个函数然后进行输出DOM node对应的结构而是先已有DOM node的完整结构，这是设计人员交互人员交付的，然后依据此设计每个函数的拆分。

💾GitHub: 参考🔗[react-in-patterns](https://github.com/krasimir/react-in-patterns)

- [Transformation](transformation.md)模式

  宏观上看整个网站页面由HTML、CSS、JavaScript组成，进行划分的话，首先最直观的划分方式是自顶向下组件树。如果独立观察每部分组件内容，即有数据输入得出输出相应的DOM并附带CSS及JavaScript，我们把这样拆分出来的函数称作 **组件**。

- [Composition](composition.md)模式

  DOM树纷繁复杂，仅仅把每个部分节点树自顶向下来划分的话，非常容易陷入一种思路，以为生搬硬套把一块儿的DOM node划分为单个组件，不加组合的把上下嵌套起来就完事。为了让DOM node交叉组合，以划分出高度可组合的组件为目的，内容分发为体现，React提供解释react node，供花样繁多的组合组件，即DOM node自由组合划分角度来独立创建组件。

- [Presentational and Container Components](presentational-container.md)模式

  以上划分组件的仅仅是DOM树结构这一个维度，从WEB应用开发角度来看，一方面可以将开发分为UI和数据两个部分，从样式和业务的区分角度来划分独立创建组件。

- []()

  就单个组件UI样式本身也是花样繁多，React提供了 **TODO** 模式，从相同的HTML元素不同样式来划分独立创建组件，从提取共同样式来划分独立创建组件。就整体UI组件而言，用来布局的和一般性样式组件也有着明显区分，这又是另外一个划分创建组件的维度，**布局**。

- [High-Order Component](high-order-component.md)模式

  数据部分即业务逻辑、数据逻辑，数据业务逻辑有共用部分，提供了抽象公用逻辑这个维度独立创建组件。

- 单个组件数据业务逻辑过大过多，React提供了[Enhance Component]()模式，同时，这个模式还适用于原本单个组件功能拆分，在简单组件上叠加逻辑组织为复杂的组件。

## 社区开源组件库

## 数据层管理模式

React组织好了组件，对于组件系统又会遇到以下几个问题：数据放在哪里？怎么处理通信？怎么管理状态？这均要通过数据层的统一API的库来管理，即redux。

- [Reducer 组织](reducer.md)

数据的组织问题，使用redux的store数据结构组织，reducer就是一个单一函数，因此也就是reducer函数的拆分组织。

- 组件的通信问题，使用dispatch action，简化的同步数据通信不存在什么模式，主要的问题在于异步通信。这里数据层通信方式的所有问题都可以通过redux中间件去解决，redux中间件是可以自定制的，目前的实践中常见的http请求、websocket接收都通过自定义方式。第三方处理比较深刻的是redux-thunk，以及redux-soga两个模块。

## 实现
- fiber架构为什么能起到作用? 如何实现的fiber架构?
- reconciliation 调度算法是如何借助fiber数据结构完成的?
- react hooks如何实现?
