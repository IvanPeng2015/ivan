# [React模式](Readme.md)/React basic theoretical concepts

react思路模型, deductive reasoning that lead us to the design.

## Transformation

- The core premise for React is that UIs are simply a projection of data into a different form of data.

- The same input gives the same output. react ui is a simple pure function.

```javascript
const NameBox = (name) => ({fontWeight: 'bold', labelContent: name})
```

- JSX是React.createELement方法的语法糖，你看到的UI实质就是数据。

- Store -> JSX -> JavaScript Plain Object（React.createELement函数返回的就是Plain Object对象） -> Dom Node（【嵌套的行内plain object拼接】是这一层映射的基调）

```
'Ivan Peng' ->
{ fontWeight: 'bold', labelContent: 'Ivan Peng' }
```

### Abstraction

- You can't fit a complex UI in a single function though.

- It is import that UIs can be abstracted into reusable pieces that leak their implementation details. Such as call one function from another.

```javascript
const FancyUserBox = user => ({
  borderStyle: '1px solid blue',
  childContent: [
    'Name: ',
    NameBox(`${user.firstName} ${user.lastName}`)
  ],
})
```

- **函数拆解** 类似于对reducer函数做的工作那样

- 复杂的层级对象，抽象成调用包装，利用Abstraction创建一层函数，合理的拆离为父子函数之间的逻辑。

```
{firstName: 'Ivan', lastName: 'Peng'} ->
{
  borderStyle: '1px solid blue',
  childContent: [
    'Name: ',
    { fontWeight: 'bold', labelContent: 'Ivan Peng' }
  ],
}
```

### Composition

- To achieve truly reusable features, it's not enough to simply reuse leaves and build new containers for them. **You also need to be able to build abstractions from containers that compose other abstractions.**

- The way I think about "composition" is that they're combining two or more different abstractions into a new one.

```javascript
const FancyBox = children => ({
  borderStyle: '1px solid blue',
  children,
})

const UserBox = user => FancyBox([
  'Name: ',
  NameBox(`${user.firstName} ${user.lastName}`)
])
```

- composition是基于combining的，这里是父子孙三层抽象包装

- 基于Abstraction（父子逻辑拆分方式）和Composition（父子孙三层逻辑拆分方式），以数学归纳原理，可以完整构建整个tree

### State

- A UI is NOT simply a replication of server/business logic state.

- There is actually a lot of state that is specific to an exact projection and not others.

- For example, if you start typing in a text field. That may or may not be replicated to other tabs or to your mobile device. Scroll position is a typical example that you almost never want to replicate across multiple projections.

- We tend to prefer our data model to be immutable.

```javascript
const FancyNameBox = (user, likes, onClick) => FancyBox([
  'Name: ',
  NameBox(`${user.firstName} ${user.lastName}`)
  'Likes: ',
  LikesBox(likes),
  LikeButton(onClick)
])

// implementation details
let likes = 0;
const addOneMoreLike = () => {
  likes += 1
  rerender()
}
// init
FancyNameBox({firstName: 'Ivan', lastName: 'Peng'}, likes, addOneMoreLike)
```

NOTE: These examples use side-effects to update state. My actual mental model of this is that they return the next version of state during an "update" pass.

### Memoization

- Calling the same function again and again is wasteful if we know the function is pure. We can create a memoized version of a function that keeps track of the last argument and last result. That way we don't have to reexecute if we keep use the same value.

```js
const memoize = (fn) => {
  let cacheArg = undefined
  let cacheResult = undefined
  return (arg) => {
    if (cacheArg === arg) {
      return cacheResult
    }
    cacheArg = arg
    cacheResult = fn(arg)
    return cacheResult
  }
}

const MemoizeNameBox = memoize(NameBox)

const NameAndAgeBox = (user, currentTime) => FancyBox([
  'Name: ',
  MemoizeNameBox(`${user.firstName} ${user.lastName}`),
  'Age in milliseconds: ',
  currentTime - user.dateOfBirth
])
```

- 这是函数技巧

### Lists

- To manage the state for each item in a list we can create a Map that holds the state for a particular item. 参考🔗<http://jasonjl.me/blog/2015/04/18/rendering-list-of-elements-in-react-with-jsx/>

```js
const UserList = (users, likesPerUser, updateUserLikes) =>
  users.map(user => FancyNameBox(
    user,
    likesPerUser.get(user.id),
    () => updateUserLikes(user.id, likesPerUser.get(user.id) + 1)
  ))

const likesPerUser = new Map();
const updateUserLikes = (id, likes) => {
  likesPerUser.set(id, likes)
  rerender()
}

UserList(data.users, likesPerUser, updateUserLikes)
```
Note: We now have multiple arguments passed to FancyNameBox. That breaks our memoization because we can only remember one value at a time.
