## [React模式](.md)/Transformation

* [输入](#输入)
* [输出](#输出)
* [调用](#调用)

react组件类比于函数

### 输入

*props* 即为组件继承属性，props有类型检查 *PropTypes*, 以及默认参数 *defaultProps*，由于组件代码相较于函数代码更为复杂，因此组件输入理应作好相应检查以及默认设置.
```js
const Title = props => (<h1>{props.text}</h1>);
Title.propTypes = { text: PropTypes.string };
Title.defaultProps = { text: 'Hello world' };

const App = () => (<Title text="Hello React" />);
```
除了直接定义在props上的，特殊之处在于props.children，指定调用时被标签包含的值。

*context*  这是一个高级不稳定API，react组件实现依赖注入的一种形式。

### 输出
返回 *JSX* 对象，类比于return值，JSX本身是React.createELement(component, props, ...children)的语法糖，因此JSX本质上就是函数调用表达式，具有JavaScript值的所有特性。
```js
const Title = props => (
  <h1>
    <a onClick={() => props.onConsoleInfo('hello react')}>
      Hello React
    </a>
  </h1>
);
Title.propTypes = { onConsoleInfo: PropTypes.func.isRequired };

const App = props => (
  <Title onConsoleInfo={console.info} />
);
```
Title组件即输出了JSX对象；
此处组件调用传了一个callback回调函数参数onConsoleInfo，将在被调用的组件里面调用，此举实现了 *由被调用子组件往父组件传递信息*，类比于异步事件模型中的回调函数，并在回调函数中传参数，实现单向数据流的逆向数据交互。

### 调用
*JSX* 调用，调用可传参的形式为Specifying Attributes或者Specifying Children。

其中 *Specifying Attributes* 形式，是以注入在props对象上来表达的：
单个Attributes类比于函数单个 *实参*，支持JavaScripts expression、String Literals，默认的Attributes为布尔值true，作完全的区分的话，可以如下理解：支持大括号／双引号／无值；
多Attributes即Spread对象扩展的形式，类比于函数调用 *对象字面量扩展*，这个API对high-order-component相当重要；
```js
const Title = (props) => {
  return (
    <h1 className="red" {...props} >
      Hello React
    </h1>
    <h2 {...props} className="green">
      hello react
    </h2>
  );
};

const YellowTitle = () => (<Title className="yellow" />);
// 输出
// <h1 className="yellow">Hello React</hi>
// <h2 className="green">hello react</h2>
const DefaultTitle = () => (<Title prefixCls="yellow" />);
// 输出
// <h1 className="red" prefixCls="yellow">Hello React</hi>
// <h2 className="green" prefixCls="yellow">hello react</h2>
```
调用Title组件来引入Attributes要注意Spread Attributes与Single Attributes的顺序区别。

*Specifying Children* 形式除了可以是以上单个Attributes值形式以外，还可以是JSX，然而实际意义上，children也还是作为props.children这个属性传给被调用组件的，而且另外本身也具有Javascript值的特性，特别提到children值可以为函数。
```js
const Title = props => (
  <h1>
    {props.text}
    {props.children}
  </h1>
  <h2>
    {props.one}
  </h2>
);
Title.propTypes = { text: PropTypes.string };
Title.defaultProps = { text: 'Hello world' };

const App = () => {
  const spreadProps = { one: <a>one</a>, anotherProps: ''}
  return (
    <Title text="Hello React" {...spreadProps}>
      <span>community</span>
    </Title>
  );
};
```
