# Closures

- JSX中props属性传递、JSX中children传递，实质上都是render函数返回的一个复杂的plain对象上的某些属性。

- 当这些属性中存在`PropTypes.func`时，这个render函数完全的就是一个闭包，教学时演示的那种典型闭包的例子。

  ```javascript
  function render() {
    let a = 'render'
    return {
      prop: function () {
        a += '_prop'
        console.info(a)
      },
    }
  }
  render().prop()
  const $render = render()
  $render.prop()
  ```

  传func类型的props说的就是这里函数调用输出的prop属性。根据prop的值我们知道，render函数执行多次得到的prop属性值是不同的，每个都是独立的。我们把prop函数依然保留有对render函数作用域的引用称之为闭包。而这个引用`prop`就被当作属性给pass到子组件中了。

- 组件调用这样传func形成闭包之后，其缺陷也是明明白白可以观察到的，即每次render函数被执行，都会更换function（引用类型）实例传给子组件。而props值更新，就会触发组件的更新。

- 一般我们所见，不会使用JSX／props或者JSX／children传引用类型的值给子组件，这里主要指的plain对象，当必须传引用类型时，解决方案为下一条。 **TODO 待斟酌**

- 引用类型值之所以更新，是因为每次执行render函数都在render的作用域内创建了一个新的引用类型值传给了子组件。规避这一点的方法就是把值写在render函数作用域外。

- 需要注意的就是，对象字面量，函数声明字面量，箭头函数字面量，数组字面量，都是会定义引用类型新的值的。前述的所有值都应该直接定义在组件实例上，或者组件外部。

- 规避了上述问题，也就规避了创建新闭包引用传给子组件。其中，在jsx字面量绑定值直接给箭头函数定义或者使用bind硬绑定都是生成新的函数。

# Passing setState a function

- setState异步，直接参数对象中获取this.state不可靠，此时需要将其考虑为reducer模型，给setState函数传一个简化版无action的reducer函数。

  ```javascript
  // pass object模型. this取值基于调用setState的词法作用域
  this.setState({flag: !this.state.flag});
  // pass callback function模型.
  this.setState(prevState => ({flag: !prevState.flag}))
  ```
