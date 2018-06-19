# Conditional In JSX

出于原因：Boolean值、Null值、Undefined值均不被渲染为任何东西，应该用短路操作符替换三目运算符

```js
const cpI = () => (isTrue ? (<div>真</div>) : <none/>)

const cpS = () => (isTrue && (<div>真</div>))
```
