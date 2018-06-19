## [React模式](Readme.md)/High-Order component

官方定义：高阶组件HOC即输入一个组件输出另一个组件的函数，HOC本质上仍然是一个无副作用的纯函数，是React中主要的组件复用技术。普通组件将props转化为UI，而HOC将一组件转化为另一组件。

首先需要关注的是官网[HOC](https://facebook.github.io/react/docs/higher-order-components.html)详尽介绍，下列英文列出来的5点再强调一遍。

* [Use HOCs For Cross-Cutting Concerns](#Use HOCs For Cross-Cutting Concerns)
* [Don't Mutate the Original Component. Use Composition.](#Don't Mutate the Original Component. Use Composition.)
* [Convention: Pass Unrelated Props Through to the Wrapped Component](#Convention: Pass Unrelated Props Through to the Wrapped Component)
* [Convention: Maximizing Composability](#Convention: Maximizing Composability)
* [Convention: Wrap the Display Name for Easy Debugging](#Convention: Wrap the Display Name for Easy Debugging)
* [注意事项⚠️](#注意事项⚠️)

### Use HOCs For Cross-Cutting Concerns

HOC抽象出来单个组件，做跨组件共享的逻辑。类似于抽象出函数，一般而言，第一第二个有相同逻辑的组件可不必抽象，但是到了第三个仍有部分逻辑重复组件创建时，请务必花费时间将此部分逻辑抽象成单个HOC组件。

* HOC应该定义组件定义这个层次：即使HOC本身只是一个函数，

HOC之所以可以做这个抽象，实质上的理由是：HOC完全控制定义了📦组件。

我们在开源库ant-design中大量使用的模式是，通过HOC的wrapper技术，将组件的逻辑隔离出来，独立起来，输出组件为import引入组件调用结果，与HOC差别，这个react-patterns叫container components。CC模式的目的是处于分离组件职责，high-level的和low-level的。

这里给出官网典型的提取复用逻辑HOC对比例子：
```js
// commentList.js 独立组件
class CommentList extends React.Component {
  constructor() {
    super();
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      // "DataSource" is some global data source
      comments: DataSource.getComments()
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      comments: DataSource.getComments()
    });
  }

  render() {
    return (
      <div>
        {this.state.comments.map((comment) => (
          <Comment comment={comment} key={comment.id} />
        ))}
      </div>
    );
  }
}

// blogPost.js 独立组件
class BlogPost extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.state = {
      blogPost: DataSource.getBlogPost(props.id)
    };
  }

  componentDidMount() {
    DataSource.addChangeListener(this.handleChange);
  }

  componentWillUnmount() {
    DataSource.removeChangeListener(this.handleChange);
  }

  handleChange() {
    this.setState({
      blogPost: DataSource.getBlogPost(this.props.id)
    });
  }

  render() {
    return <TextBlock text={this.state.blogPost} />;
  }
}

// 对比HOC模式
function withSubscription(WrappedComponent, selectData) {
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      };
    }

    componentDidMount() {
      DataSource.addChangeListener(this.handleChange);
    }

    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }

    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      });
    }

    render() {
      // ... and renders the wrapped component with the fresh data!
      // Notice that we pass through any additional props
      return <WrappedComponent data={this.state.data} {...this.props} />;
    }
  };
}

const CommentListWithSubscription = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
);

const BlogPostWithSubscription = withSubscription(
  BlogPost,
  (DataSource, props) => DataSource.getBlogPost(props.id)
});
```

### Don't Mutate the Original Component. Use Composition.

勿要改动原始组件原型，任何方法任何属性。这里贴出官网给的anti-patterns例子
```js
function logProps(InputComponent) {
  InputComponent.prototype.componentWillReceiveProps(nextProps) {
    console.log('Current props: ', this.props);
    console.log('Next props: ', nextProps);
  }
  // The fact that we're returning the original input is a hint that it has been mutated.
  return InputComponent;
}

// EnhancedComponent will log whenever props are received
const EnhancedComponent = logProps(InputComponent);
```
一方面InputComponent将不可在EnhancedComponent外复用；
另一方面HOC本身作为纯函数是可以自由组合的，多个HOCs组合调用时，原型上修改的东西存在可能被覆盖；
再者，没有生命周期的函数式组件，这个HOC无效；

### Convention: Pass Unrelated Props Through to the Wrapped Component

选择性的传递属性：
```js
render() {
  // HOC使用到的extraProp不传给WrappedComponent
  const { extraProp, ...passThroughProps } = this.props;

  // 给WrappedComponent注入属性，通常为state值或者示例方法
  const injectedProp = someStateOrInstanceMethod;

  // 传属性
  return (
    <WrappedComponent
      injectedProp={injectedProp}
      {...passThroughProps}
    />
  );
}
```

### Convention: Maximizing Composability

```js
// const EnhancedComponent = connect(commentSelector)(withRouter(WrappedComponent))
const enhance = compose(
  connect(commentSelector),
  withRouter
)
const EnhancedComponent = enhance(WrappedComponent)
```

### Convention: Wrap the Display Name for Easy Debugging

### 注意事项⚠️

  * render函数中不要使用HOCs：HOC调用输出的实质上是新创建的组件，render函数重复执行。
  * 静态方法存在则必须复制：有个辅助库hoist-non-react-statics。
  * refs并不传递：
