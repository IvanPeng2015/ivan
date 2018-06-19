# [React模式](Readme.md)/Composition

为了让react组件高度可组合，提供了react node分发的能力，react提供了API`props.children`、提供了在props上直接传JSX值的能力。（Specifying Children等效于在 _调用_ 中另外传一个Specifying Attributes，语义相同，语法不同）。

**Remember, components don't have to emit DOM. They only need to provide composition boundaries between UI concerns.**

- [样式JSX](#样式JSX)
- [plain对象JSX](#plain对象JSX)
- [函数JSX](#函数JSX)
- [属性: react元素](#属性: react元素)

## 样式JSX

写一个组件，看这个组件输出什么JSX，其中带有props Attributes、props.children甚至context，这样创建组件是普遍性的，是我们封装一个组件的目的。

```javascript
const Header = props => (
  <h1 className='lots-of-styles-here'>
    <strong>
      <i className='something-else'>
        { props.children }
      </i>
    </strong>
  </h1>
);

<Header>Hello world</Header>
```

上面props.children值为最简化的HtmlElement Node，同样可以为复杂的JSX。

```javascript
const Header = props => (<header>{props.children}</header>);
const Navigation = props => (<nav>Navigation</nav>);
const SearchBar = props => (<nav>Search Bar</nav>);
<Header><Navigation /></Header>
<Header><SearchBar /></Header>
```

Header组件并不关心他的content出现什么元素，什么JSX，这就让Header部分，只定义它自己那部分输出的JSX的样式以及交互，和通过props输出完全的解耦。用来构建UI时可以将应用拆分成独立自主完成功能且便于测试的小组件。

此处的JSX嵌套调用可以类比与函数的组合，但因为通过利用`props.children`组件调用嵌套起来比较清晰，所以就未存在lodash/fp/compose方法。

React这个地方内容分发的能力与Vue中的 _[单个slot]（<http://cn.vuejs.org/v2/guide/components.html#单个-Slot）>_ 单个插槽相似。

## Plain对象JSX

多个属性分配多个插槽，下面是props.children对象的示例（Specifying Attributes同理）：

```javascript
const Header = props => (
  <header>
    {props.children.navigation}
    <br />
    <div>
      {props.children.searchbar}
    </div>
  </header>
);
<Header>{{
  navigation: (<nav>Navigation</nav>),
  searchBar: (<nav>Search Bar</nav>),
}}</Header>
```

这个拆分就不那么显得直观了，主要的作用在于分割，且可以随意分割。这就相当于提供了多插槽，与 _[具名slot](http://cn.vuejs.org/v2/guide/components.html#具名-Slot)_ 类似。

但是，对于除JSX对象外的一般性的Attributes值，用props.children没有太大意义，可以认定为anti-patterns，徒增代码理解难度。

## 函数JSX

将JSX写在对象属性上通过props.children传给调用组件，只具备了属性索引JSX，一般的，可以设置复杂的函数，调用props.children()得到相应的JSX，目前看来作用不是很大，且理解起来往往费力，例子暂不放。

## 属性: react元素

我们首先来讨论我们想生成的一般性组件树🌲，嵌套的APP、Header、Navigation最终生成的标记:

```javascript
<App>
  <Header>
    <Navigation> ... <Navigation/>
  </Header>
</App>
```

最🥚疼的也是常看到的用来组合这三个组件的方法，是哪里需要就哪里调用的方式，如下anti-patterns代码实例:

```javascript
// app.js
const App = () => (<Header />);

// header.js
const Header = () => (
  <header>
    <h1>Hello React</h1>
    <hr />
    <Navigation />
  </header>);
export default Header;

// navigation.js
const Navigation = () => (<nav>...</nav>);
```

遵从这个模式引入了以下问题：

- 我们擅长的就是以App组件为入口，对Header组件进行调整在的Header组件上添加些logo元素，又以Header为入口对navigation进行调整Navitigation上添加一些菜单栏。但是，Header和Navigation组件被绑定在了一起，耦合度太高，当我们想要没有Navigation的Header时，就比较麻烦。
- 单纯测试Header组件逻辑比较麻烦，对于其中引入的组件我们都需要创建实例来引入测试，高耦合情况下，Navigation的测试错误与Header难以区分。

调用组件时将嵌套的元素使用props传递给被调用组件，方式一：props.children，方式二：其他任何props:

```javascript
// app.js
const App = () => {
  const title = (<h1>Hello React</h1>);
  return (
    <Header title={title}>
      <Navigation />
    </Header>
  );
};

// header.js
const Header = props => (
  <header>
    {props.title}
    <hr />
    {props.children}
  </header>
);

// navigation.js
const Navigation = () => (<nav>...</nav>);
```
