# K combinator

  ```js
  /**
   * Composes single-argument functions from right to left. The rightmost
   * function can take multiple arguments as it provides the signature for
   * the resulting composite function.
   *
   * @sig ((y -> z), (x -> y), ..., (o -> p), ((a, b, ..., n) -> o)) -> ((a, b, ..., n) -> z)
   * @param {...Function} funcs The functions to compose.
   * @returns {Function} A function obtained by composing the argument functions
   * from right to left. For example, compose(f, g, h) is identical to doing
   * (...args) => f(g(h(...args))).
   */

  export default function compose(...funcs) {
    if (funcs.length === 0) {
      return arg => arg
    }

    if (funcs.length === 1) {
      return funcs[0]
    }

    return funcs.reduce((a, b) => (...args) => a(b(...args)))
  }
  ```

  * 理解Array.prototype.reduce函数的acc参数就理解了compose函数或者pipe函数的操作方式。结合reduce得到如下的签名。

  ```js
  /**
   * Perform left-to-right function composition.
   * The leftmost function may have any arity, the remaining functions must be unary.
   * @sig (((a, b, ..., n) -> o), (o -> p), ..., (x -> y), (y -> z)) -> ((a, b, ..., n) -> z)
   * @param {...Functions} func The functions to pipe.
   * @returns {Function}
   */

  export default function pipe(...funcs) {
    if(funcs.length === 0) {
      return arg => arg
    }

    if(funcs.length === 1) {
      return funcs[0]
    }

    return funcs.reduce((a, b) => (...args) => b(a(...args)))
  }
  ```

  ramda库中构建的composeP，传递promise的函数，
