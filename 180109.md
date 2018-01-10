## JSON数据

较为常见的数据交换格式为JSON，在数据处理中，应尊重JSON数据取值的每种condition

针对属性key，其value情况如下定义

JSON = null
    or true or false
    or JSONNumber
    or JSONString
    or JSONObject
    or JSONArray

以上是取值情况，还有无值情况即undefined

(1) value === undefined
(^1) value !== undefined

(2) value === null
(^2) value !== null

(3) typeof value === 'boolean'
(^3) typeof value !== 'boolean'

(4) typeof value === 'number'
(^4) typeof value !== 'number'

(5) typeof value === 'string'
(^5) typeof value !== 'string'

(6) Object.prototype.toString.call(value) === '[object Object]'
(^6) Object.prototype.toString.call(value) !== '[object Object]'

(7) Array.isArray(value) === true
(^7) Array.isArray(value) === false

一般设计API时，属性key值value类型已经定义好。针对以上否定判定可以进行默认值设定，避免数据操作错误。

## combineReducer处理reducer函数的拆分

把一个case的type写进两个reducer.js居然还是有作用的。。。。两个都予以分发执行的。

因此reducer可以说对action没有任何限制，两者是完全的独立发布者/订阅者。就结构上而言，完全是独立的，action按照行为动作来组织结构，reducer按照数据结构来组织结构。
