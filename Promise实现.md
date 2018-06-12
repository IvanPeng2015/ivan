# Promise实现

### Promise标准

- [ ] then方法: 参数及返回值
- [ ] 不同实现的promise可以互相调用
- [ ] 状态转换

### 构造函数

创建Promise实例的形式
```js
var promise = new Promise(function (resolve, reject) {}/* executor */)
```
定义Promise
```js
function noop() { } // 工具函数 空函数
// Polyfill for Function.prototype.bind
function bind(fn, thisArg) {
  return function () {
    return fn.apply(thisArg, arguments)
  }
}
// 处理函数
function handle(deferred, self) {
  while (self._status === 3) {
    self = self._value
  }
  if (self._status === 0) {
    self._deferreds.push(deferred)
    return
  }
  self._handled = true
  Promise._immediateFn(function () {
    var cb = self._status === 1 ? deferred.onFulfilled : deferred.onRejected
    if (cb === null) {
      (self._status === 1 ? resolve : reject)(self._value, deferred.promise)
      return
    }
    var ret
    try {
      ret = cb(self._value)
    } catch (e) {
      reject(e, deferred.promise)
      return
    }
    resolve(ret, deferred.promise)
  })
}

// 执行被推进promise执行数组的函数
function finale(self) {
  // rejected状态, 且执行数组中不存在函数
  if (self._status === 2 && self._deferreds.length === 0) {
    Promise._immediateFn(function () {
      if (!self._handled) {
        Promise._unhandledRejectionFn(self._value)
      }
    })
  }
  for (var i = 0, len = self._deferreds.length; i < len; i += 1) {
    handle(self._deferreds[i], self)
  }
  self._deferreds = null // 回收
}
// Promise reject
function reject(value, self) {
  self._status = 2
  self._value = value
  finale(self)
}
// Promise resolve
function resolve(value, self) {
  try {
    if (value === self) {
      throw new TypeError('A Promise cannot be resolved with itself')
    }
    // resolve处理的值, value可能为promise实例, 或者为其它实现
    if (value && (typeof value === 'object' || typeof value === 'function')) {
      var then = value.then
      if (value instanceof Promise) { // 本身的promise实例
        self._status = 3
        self._value = value
        finale(self)
        return
      } else if (typeof then === 'function') { // 其它promise实例
        doExecutor(bind(then, value), self)
        return
      }
    }
    self._status = 1
    self._value = value
    finale(self)
  } catch (e) {
    reject(e, self)
  }
}
// 函数, reject或者resolve只执行一次, self为promise实例
function doExecutor(executor, self) {
  var done = false
  try {
    executor(
      function (value) {
        if (done) {
          return;
        }
        done = true
        resolve(value, self)
      },
      function (reason) {
        if (done) {
          return;
        }
        done = true
        reject(reason, self)
      }
    )
  } catch (err) {
    if (done) {
      return;
    }
    done = true
    reject(err, self)
  }
}

function Promise(executor) {
  if (!(this instanceof Promise)) {
    throw new TypeError('Promise must be constructed via new')
  }
  if (typeof executor !== 'function') {
    throw new TypeError('Promise executor must be a function')
  }
  this._status = 0 // 状态值0-3
  this._value = undefined // 回调值值
  this._deferreds = [] // 处理数组
  this._handled = false // 初始处理状态
  doExecutor(executor, this) // 执行器
}
// 处理器
function Handler(onFulfilled, onRejected, promise) {
  this.onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : null
  this.onRejected = typeof onRejected === 'function' ? onRejected : null
  this.promise = promise
}

// 实现then方法, 主要在于返回值为新的promise对象
Promise.prototype.then = function (onFulfilled, onRejected) {
  var prom = new this.constructor(noop)

  handle(new Handler(onFulfilled, onRejected, prom), this)
  return prom
}

// 事件循环当前回合结束时调用的函数. polyfill
Promise._immediateFn = (typeof setImmediate === 'function' && function (fn) {
  setImmediate(fn) // Nodejs的立即调用定时器函数
}) ||
  function (fn) {
    setTimeout(fn, 0)
  }
// promise error未捕获
Promise._unhandledRejectionFn = function _unhandledRejectionFn(err) {
  if (typeof console !== 'undefined' && console) {
    console.warn('Possible Unhandled Promise Rejection:', err); // eslint-disable-line no-console
  }
};
```
