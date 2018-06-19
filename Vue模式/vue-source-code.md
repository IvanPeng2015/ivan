# Vue源码

一个View框架究竟从整体上要做什么？

抓住主线：从Vue构造函数的设计、原型的设计、选项的设计入手，紧接着从模版渲染、指令设计、数据绑定的实现入手，紧接着从virtual dom更新视图入手

## 构造函数设计

- `package.json` ->>

- `npm run ${scripts}` ->>

- config: `./build/config.js` ->>

穿插: entry的那些path解析完全是由node API: `path.resolve()`加上简单自定义函数得到。

- entry: `./src/platforms/web/runtime-with-compiler.js` ->> (decorator Vue constructor)

穿插: `module specifier`为具名模块string IDs，原理上这个是由[module loader API](http://exploringjs.com/es6/ch_modules.html#sec_module-loader-api)配置的，这个地方是间接通过flow配置文件`.flowconfig`[[options]module.name_mapper(regex -> string)](https://flow.org/en/docs/config/options/#toc-module-name-mapper-regex-string)来修改配置的

在构造函数Vue上定义compile方法: // TODO

这里面有个用法值得探讨, 预先把原型$mount属性定义为变量mount，然后再定义原型上$mount属性，也在定义函数中尾调用mount。

```js
const mount = Vue.prototype.$mount

Vue.prototype.$mount = function (el, hydrating) {
  return mount.call(this, el, hydrating)
}
```
$mount属性的实现 缓后

- decorator: `./src/platforms/web/runtime/index.js` ->>

- `./src/core/index.js` ->>
