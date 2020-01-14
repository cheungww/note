## React 设计思想

> 译者序：本文是 React 核心开发者、有 React API 终结者之称的 Sebastian Markbåge 撰写，阐述了他设计 React 的初衷。阅读此文，你能站在更高的高度思考 React 的过去、现在和未来。原文地址：https://github.com/reactjs/react-basic

我写此文是想正式地阐述我心中 React 的[心智模型](http://baike.baidu.com/view/2333986.htm)。目的是解释为什么我们会这样设计 React，同时你也可以根据这些论点反推出 React。

不可否认，此文中的部分论据或前提尚存争议，而且部分示例的设计可能存在 bug 或疏忽。这只是正式确定它的最初阶段。如果你有更好的完善它的想法可以随时提交 pull request。本文不会介绍框架细节中的奇技淫巧，相信这样能提纲挈领，让你看清 React 由简单到复杂的设计过程。

React.js 的真实实现中充满了具体问题的解决方案，渐进式的解法，算法优化，历史遗留代码，debug 工具以及其他一些可以让它真的具有高可用性的内容。这些代码可能并不稳定，因为未来浏览器的变化和功能权重的变化随时面临改变。所以具体的代码很难彻底解释清楚。

我偏向于选择一种我能完全 hold 住的简洁的心智模型来作介绍。

### 1. 变换（Transformation）

设计 React 的核心前提是认为 UI 只是把数据通过映射关系变换成另一种形式的数据。同样的输入必会有同样的输出。这恰好就是纯函数。

```js
function NameBox(name) {
  return { fontWeight: 'bold', labelContent: name };
}
'Sebastian Markbåge' ->
{ fontWeight: 'bold', labelContent: 'Sebastian Markbåge' };
```

### 2. 抽象（Abstraction）

你不可能仅用一个函数就能实现复杂的 UI。重要的是，你需要把 UI 抽象成多个隐藏内部细节，又可复用的函数。通过在一个函数中调用另一个函数来实现复杂的 UI，这就是抽象。

```js
function FancyUserBox(user) {
  return {
    borderStyle: '1px solid blue',
    childContent: [
      'Name: ',
      NameBox(user.firstName + ' ' + user.lastName)
    ]
  };
}
{ firstName: 'Sebastian', lastName: 'Markbåge' } ->
{
  borderStyle: '1px solid blue',
  childContent: [
    'Name: ',
    { fontWeight: 'bold', labelContent: 'Sebastian Markbåge' }
  ]
};
```

### 3. 组合（Composition）

为了真正达到重用的特性，只重用叶子然后每次都为他们创建一个新的容器是不够的。你还需要可以包含其他抽象的容器再次进行组合。我理解的“组合”就是将两个或者多个不同的抽象合并为一个。

```js
function FancyBox(children) {
  return {
    borderStyle: '1px solid blue',
    children: children
  };
}

function UserBox(user) {
  return FancyBox([
    'Name: ',
    NameBox(user.firstName + ' ' + user.lastName)
  ]);
}
```

### 4. 状态（State）

UI 不单单是对服务器端或业务逻辑状态的复制。实际上还有很多状态是针对具体的渲染目标。举个例子，在一个 text field 中打字。它不一定要复制到其他页面或者你的手机设备。滚动位置这个状态是一个典型的你几乎不会复制到多个渲染目标的。

我们倾向于使用不可变的数据模型。我们把可以改变 state 的函数串联起来作为原点放置在顶层。

```js
function FancyNameBox(user, likes, onClick) {
  return FancyBox([
    'Name: ', NameBox(user.firstName + ' ' + user.lastName),
    'Likes: ', LikeBox(likes),
    LikeButton(onClick)
  ]);
}

// 实现细节

var likes = 0;
function addOneMoreLike() {
  likes++;
  rerender();
}

// 初始化

FancyNameBox(
  { firstName: 'Sebastian', lastName: 'Markbåge' },
  likes,
  addOneMoreLike
);
```

*注意：本例更新状态时会带来副作用（addOneMoreLike 函数中）。我实际的想法是当一个“update”传入时我们返回下一个版本的状态，但那样会比较复杂。此示例待更新*

### 5. Memoization

对于纯函数，使用相同的参数一次次调用未免太浪费资源。我们可以创建一个函数的 memorized 版本，用来追踪最后一个参数和结果。这样如果我们继续使用同样的值，就不需要反复执行它了。

```js
function memoize(fn) {
  var cachedArg;
  var cachedResult;
  return function(arg) {
    if (cachedArg === arg) {
      return cachedResult;
    }
    cachedArg = arg;
    cachedResult = fn(arg);
    return cachedResult;
  };
}

var MemoizedNameBox = memoize(NameBox);

function NameAndAgeBox(user, currentTime) {
  return FancyBox([
    'Name: ',
    MemoizedNameBox(user.firstName + ' ' + user.lastName),
    'Age in milliseconds: ',
    currentTime - user.dateOfBirth
  ]);
}
```

### 6. 列表（Lists）

大部分 UI 都是展示列表数据中不同 item 的列表结构。这是一个天然的层级。

为了管理列表中的每一个 item 的 state ，我们可以创造一个 Map 容纳具体 item 的 state。

```js
function UserList(users, likesPerUser, updateUserLikes) {
  return users.map(user => FancyNameBox(
    user,
    likesPerUser.get(user.id),
    () => updateUserLikes(user.id, likesPerUser.get(user.id) + 1)
  ));
}

var likesPerUser = new Map();
function updateUserLikes(id, likeCount) {
  likesPerUser.set(id, likeCount);
  rerender();
}

UserList(data.users, likesPerUser, updateUserLikes);
```

*注意：现在我们向 FancyNameBox 传了多个不同的参数。这打破了我们的 memoization 因为我们每次只能存储一个值。更多相关内容在下面。*

### 7. 连续性（Continuations）

不幸的是，自从 UI 中有太多的列表，明确的管理就需要大量的重复性样板代码。

我们可以通过推迟一些函数的执行，进而把一些模板移出业务逻辑。比如，使用“柯里化”（JavaScript 中的 [`bind`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)）。然后我们可以从核心的函数外面传递 state，这样就没有样板代码了。

下面这样并没有减少样板代码，但至少把它从关键业务逻辑中剥离。

```js
function FancyUserList(users) {
  return FancyBox(
    UserList.bind(null, users)
  );
}

const box = FancyUserList(data.users);
const resolvedChildren = box.children(likesPerUser, updateUserLikes);
const resolvedBox = {
  ...box,
  children: resolvedChildren
};
```

### 8. State Map

之前我们知道可以使用组合避免重复执行相同的东西这样一种重复模式。我们可以把执行和传递 state 逻辑挪动到被复用很多的低层级的函数中去。

```js
function FancyBoxWithState(
  children,
  stateMap,
  updateState
) {
  return FancyBox(
    children.map(child => child.continuation(
      stateMap.get(child.key),
      updateState
    ))
  );
}

function UserList(users) {
  return users.map(user => {
    continuation: FancyNameBox.bind(null, user),
    key: user.id
  });
}

function FancyUserList(users) {
  return FancyBoxWithState.bind(null,
    UserList(users)
  );
}

const continuation = FancyUserList(data.users);
continuation(likesPerUser, updateUserLikes);
```

### 9. Memoization Map

一旦我们想在一个 memoization 列表中 memoize 多个 item 就会变得很困难。因为你需要制定复杂的缓存算法来平衡调用频率和内存占有率。

还好 UI 在同一个位置会相对的稳定。相同的位置一般每次都会接受相同的参数。这样以来，使用一个集合来做 memoization 是一个非常好用的策略。

我们可以用对待 state 同样的方式，在组合的函数中传递一个 memoization 缓存。

```js
function memoize(fn) {
  return function(arg, memoizationCache) {
    if (memoizationCache.arg === arg) {
      return memoizationCache.result;
    }
    const result = fn(arg);
    memoizationCache.arg = arg;
    memoizationCache.result = result;
    return result;
  };
}

function FancyBoxWithState(
  children,
  stateMap,
  updateState,
  memoizationCache
) {
  return FancyBox(
    children.map(child => child.continuation(
      stateMap.get(child.key),
      updateState,
      memoizationCache.get(child.key)
    ))
  );
}

const MemoizedFancyNameBox = memoize(FancyNameBox);
```

### 10. 代数效应（Algebraic Effects）

多层抽象需要共享琐碎数据时，一层层传递数据非常麻烦。如果能有一种方式可以在多层抽象中快捷地传递数据，同时又不需要牵涉到中间层级，那该有多好。React 中我们把它叫做“context”。

有时候数据依赖并不是严格按照抽象树自上而下进行。举个例子，在布局算法中，你需要在实现他们的位置之前了解子节点的大小。

现在，这个例子有一点超纲。我会使用 [代数效应](http://math.andrej.com/eff/) 这个由我发起的 [ECMAScript 新特性提议](https://esdiscuss.org/topic/one-shot-delimited-continuations-with-effect-handlers)。如果你对函数式编程很熟悉，它们 在避免由 monad 强制引入的仪式一样的编码。

```js
function ThemeBorderColorRequest() { }

function FancyBox(children) {
  const color = raise new ThemeBorderColorRequest();
  return {
    borderWidth: '1px',
    borderColor: color,
    children: children
  };
}

function BlueTheme(children) {
  return try {
    children();
  } catch effect ThemeBorderColorRequest -> [, continuation] {
    continuation('blue');
  }
}

function App(data) {
  return BlueTheme(
    FancyUserList.bind(null, data.users)
  );
}
```

> 本节转载：https://github.com/react-guide/react-basic
>
> 英文版：https://github.com/reactjs/react-basic



## 组件的生命周期（v16.4版本）

### react 16版之前的生命周期函数

先来了解一下 react 16版之前的生命周期

![react16前的生命周期](../images/react.assets/react16%E5%89%8D%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)



![react16版本前的直观版](../images/react.assets/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E6%97%A7%E7%89%88.jpg)



- 初始化阶段：
  - getDefaultProps:获取实例的默认属性
  - getInitialState:获取每个实例的初始化状态
  - componentWillMount：组件即将被装载、渲染到页面上
  - render:组件在这里生成虚拟的 DOM 节点
  - componentDidMount:组件真正在被装载之后
- 运行中状态：
  - componentWillReceiveProps:组件将要接收到属性的时候调用
  - shouldComponentUpdate:组件接受到新属性或者新状态的时候（可以返回 false，接收数据后不更新，阻止 render 调用，后面的函数不会被继续执行了）
  - componentWillUpdate:组件即将更新不能修改属性和状态
  - render:组件重新描绘
  - componentDidUpdate:组件已经更新
- 销毁阶段：
  - componentWillUnmount:组件即将销毁



#### getDefaultProps 和 getInitialState

getDefaultProps 函数的返回值可以作为 props 的初始值，这个函数只在 React.createClass 方法创造的组件类才会用到。

getInitialState 这个函数的返回值会用来初始化组件的 this.state，但是，这个方法只有用 React.createClass 方法创造的组件类才会发生作用。

假如我们用 React.createClass 定义一个组件 Sample，设定内部状态 foo 的初始值为字符串 bar，同时设定一个叫 sampleProp 的 prop 初始值为数字值 0，代码如下：

```js
const Sample = React.createClass({
  getInitialState: function() {
    return { foo: 'bar' };
  },
  getDefaultProps: function() {
    return { sapmpleProp: 0 };
  }
})
```

实际上，getDefaultProps 和 getInitialState两个方法在 ES6 的方法定义的 React 组件根本不会用到。所以这里只作为了解。

其他16版之前的生命周期将会在下文的比较中讨论。



![React16.4版之后的生命周期](../images/react.assets/16%E7%89%88%E5%90%8E%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png)



### react 16.4版之后的生命周期函数

**挂载**

当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：

- [`constructor()`](#constructor()) 
- `static getDerivedStateFromProps()`
- `render()`
- `componentDidMount()`

> 注意:
>
> 下述生命周期方法即将过时，在新代码中应该[避免使用它们](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/03/27/update-on-async-rendering.html)：
>
> - `UNSAFE_componentWillMount()`



**更新**

当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：

- `static getDerivedStateFromProps()`
- `shouldComponentUpdate()`
- `render()` 
- `getSnapshotBeforeUpdate()`
- `componentDidUpdate()` 

> 注意:
>
> 下述方法即将过时，在新代码中应该[避免使用它们](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/03/27/update-on-async-rendering.html)：
>
> - `UNSAFE_componentWillUpdate()`
> - `UNSAFE_componentWillReceiveProps()`



**卸载**

当组件从 DOM 中移除时会调用如下方法：

- `componentWillUnmount()` 



**错误处理**

当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：

- `static getDerivedStateFromError() ` 
- `componentDidCatch()` 



**其他 APIs**

组件还提供了一些额外的 API：

- `setState()`
- `forceUpdate()`



**class 属性**

- `defaultProps`
- `displayName`



**实例属性**

- `props`
- `state`



#### `constructor()`

```js
constructor(props)
```

**如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。**

在 React 组件挂载之前，会调用它的构造函数。在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 `super(props)`。否则，`this.props` 在构造函数中可能会出现未定义的 bug。

通常，在 React 中，构造函数仅用于以下两种情况：

- 通过给 `this.state` 赋值对象来初始化[内部 state](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/state-and-lifecycle.html)。
- 为[事件处理函数](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/handling-events.html)绑定实例

在 `constructor()` 函数中**不要调用 setState() 方法**。如果你的组件需要使用内部 state，请直接在构造函数中为 **this.state 赋值初始 state**：

```js
constructor(props) {
  super(props);
  // 不要在这里调用 this.setState()
  this.state = { counter: 0 };
  // 为事件处理函数绑定实例
  this.handleClick = this.handleClick.bind(this);
}
```

只能在构造函数中直接为 `this.state` 赋值。如需在其他方法中赋值，你应使用 `this.setState()` 替代。

要避免在构造函数中引入任何副作用或订阅。如遇到此场景，请将对应的操作放置在 `componentDidMount` 中。

>
> 注意
>
> **避免将 props 的值复制给 state！这是一个常见的错误：**
>
> ```js
> constructor(props) {
>  super(props);
>  // 不要这样做
>  this.state = { color: props.color };
> }
> ```
>
> 如此做毫无必要（你可以直接使用 `this.props.color`），同时还产生了 bug（更新 prop 中的 `color` 时，并不会影响 state，因为挂载的生命周期函数只执行一次，只起到初始化的作用，没有更新的作用）。
>
> **只有在你刻意忽略 prop 更新的情况下使用。**此时，应将 prop 重命名为 `initialColor` 或 `defaultColor`。必要时，你可以[修改它的 `key`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key)，以强制“重置”其内部 state。
>
> 请参阅关于[避免派生状态的博文](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html)，以了解出现 state 依赖 props 的情况该如何处理。



#### `static getDerivedStateFromProps()`

```js
static getDerivedStateFromProps(nextProps, prevState)
// 官方文档的是 static getDerivedStateFromProps(props, state)，此处的 pros 和 state 不太明显
// 所以改为 nextProps（更新后的 props）和 prevState（要更新前的 state）
```

`getDerivedStateFromProps` 从语义上理解是从更新后 props 中获取派生 state，它会在调用 render 方法之前调用，并且在初始挂载及后续更新时都会被调用。它应返回一个对象来更新 state，如果返回 null 则不更新任何内容。

此方法适用于[罕见的用例](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#when-to-use-derived-state)，即 state 的值在任何时候都取决于 props。例如，实现 `<Transition>` 组件可能很方便，该组件会比较当前组件与下一组件，以决定针对哪些组件进行转场动画。

派生状态会导致代码冗余，并使组件难以维护。 [确保你已熟悉这些简单的替代方案：](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html)

- 如果你需要**执行副作用**（例如，数据提取或动画）以响应 props 中的更改，请改用 [`componentDidUpdate`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#componentdidupdate)。
- 如果只想在 **prop 更改时重新计算某些数据**，[请使用 memoization helper 代替](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)。
- 如果你想**在 prop 更改时“重置”某些 state**，请考虑使组件[完全受控](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component)或[使用 `key` 使组件完全不受控](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key) 代替。

此方法无权访问组件实例，即这个函数不能通过 **this** 访问到class的属性，也并不推荐直接访问属性。而是应该通过参数提供的 nextProps 以及 prevState 来进行判断，根据新传入的props来映射到state。如果你需要，可以通过提取组件 props 的纯函数及 class 之外的状态，在`getDerivedStateFromProps()`和其他 class 方法之间重用代码。

请注意，不管原因是什么，都会在*每次*渲染前触发此方法。这与 `UNSAFE_componentWillReceiveProps` 形成对比，**后者是如果父组件导致组件重新渲染，即使 props 没有更改，也会调用此方法。如果只想处理更改，请确保进行当前值与变更值的比较。** 

需要注意的是，如果props传入的内容**不需要影响到你的state**，那么就需要**返回一个null**，这个返回值是必须的，所以尽量将其写到函数的末尾。

关于适不适合使用派生 state，可以参考 [你可能不需要使用派生 state](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html) 



#### `render()`

```js
render()
```

`render()` 方法是 class 组件中唯一必须实现的方法。

当 `render` 被调用时，它会检查 `this.props` 和 `this.state` 的变化并返回以下类型之一：

- **React 元素**。通常通过 JSX 创建。例如，`<div />` 会被 React 渲染为 DOM 节点，`<MyComponent />` 会被 React 渲染为自定义组件，无论是 `<div />` 还是 `<MyComponent />` 均为 React 元素。
- **数组或 fragments**。 使得 render 方法可以返回多个元素。欲了解更多详细信息，请参阅 [fragments](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/fragments.html) 文档。
- **Portals**。可以渲染子节点到不同的 DOM 子树中。欲了解更多详细信息，请参阅有关 [portals](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/portals.html) 的文档
- **字符串或数值类型**。它们在 DOM 中会被渲染为文本节点
- **布尔类型或 null**。什么都不渲染。（主要用于支持返回 `test && <Child />` 的模式，其中 test 为布尔类型。)

`render()` 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。

如需与浏览器进行交互，请在 `componentDidMount()` 或其他生命周期方法中执行你的操作。保持 `render()` 为纯函数，可以使组件更容易思考。

> 注意
>
> 如果 `shouldComponentUpdate()` 返回 false，则不会调用 `render()`。



#### `componentDidMount()`

```js
componentDidMount()
```

`componentDidMount()` 会在组件挂载后（插入 DOM 树中）立即调用。依赖于 DOM 节点的初始化应该放在这里。如需通过网络请求获取数据，此处是实例化请求的好地方。

这个方法是比较适合添加订阅的地方。如果添加了订阅，请不要忘记在 `componentWillUnmount()` 里取消订阅

你可以在 `componentDidMount()` 里**可以直接调用 setState()**。**它将触发额外渲染，但此渲染会发生在浏览器更新屏幕之前**。如此保证了即使在 `render()` 两次调用的情况下，用户也不会看到中间状态。请谨慎使用该模式，因为它会导致性能问题。通常，你应该在 `constructor()` 中初始化 state。如果你的渲染依赖于 DOM 节点的大小或位置，比如实现 modals 和 tooltips 等情况下，你可以使用此方式处理



#### `shouldComponentUpdate()`

```js
shouldComponentUpdate(nextProps, nextState)
```

根据 `shouldComponentUpdate()` 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。

当 props 或 state 发生变化时，`shouldComponentUpdate()` 会在渲染执行之前被调用。返回值默认为 true。首次渲染或使用 `forceUpdate()` 时不会调用该方法。

此方法仅作为**性能优化的方式**而存在。不要企图依靠此方法来“阻止”渲染，因为这可能会产生 bug。你应该**考虑使用内置的 PureComponent 组件**，而不是手动编写 `shouldComponentUpdate()`。`PureComponent` 会对 props 和 state 进行浅层比较，并减少了跳过必要更新的可能性。

如果你一定要手动编写此函数，可以将 `this.props` 与 `nextProps` 以及 `this.state` 与`nextState` 进行比较，并返回 `false` 以告知 React 可以跳过更新。请注意，**返回 `false` 并不会阻止子组件在 state 更改时重新渲染。**

我们不建议在 `shouldComponentUpdate()` 中进行深层比较或使用 `JSON.stringify()`。这样非常影响效率，且会损害性能。

目前，如果 `shouldComponentUpdate()` 返回 `false`，则不会调用 [`UNSAFE_componentWillUpdate()`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#unsafe_componentwillupdate)，[`render()`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#render) 和 [`componentDidUpdate()`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#componentdidupdate)。**后续版本，React 可能会将 `shouldComponentUpdate` 视为提示而不是严格的指令，并且，当返回 `false` 时，仍可能导致组件重新渲染。**



#### `getSnapshotBeforeUpdate()`

```react
getSnapshotBeforeUpdate(prevProps, prevState)
```

`getSnapshotBeforeUpdate()` 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期的任何返回值将作为参数传递给 `componentDidUpdate()`。

此用法并不常见，但它可能出现在 UI 处理中，如需要以特殊方式处理滚动位置的聊天线程等。

应返回 snapshot 的值（或 `null`）。

例如：

```react
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 我们是否在 list 中添加新的 items ？
    // 捕获滚动位置以便我们稍后调整滚动位置。
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
    // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
    //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}
```



在上述示例中，重点是从 `getSnapshotBeforeUpdate` 读取 `scrollHeight` 属性，因为 “render” 阶段生命周期（如 `render`）和 “commit” 阶段生命周期（如 `getSnapshotBeforeUpdate` 和 `componentDidUpdate`）之间可能存在延迟。



#### `componentDidUpdate()`

```react
componentDidUpdate(prevProps, prevState, snapshot)
```

`componentDidUpdate()` 会在更新后会被立即调用。首次渲染不会执行此方法。

当组件更新后，可以在此处对 DOM 进行操作。如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求。（例如，当 props 未发生变化时，则不会执行网络请求）。

```react
componentDidUpdate(prevProps) {
  // 典型用法（不要忘记比较 props）：
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

你也可以在 `componentDidUpdate()` 中**直接调用 setState()**，但请注意**它必须被包裹在一个条件语件里**，正如上述的例子那样进行处理，否则会导致死循环。它还会导致额外的重新渲染，虽然用户不可见，但会影响组件性能。不要将 props 直接赋给 state（官方：不要将 props “镜像”给 state），请考虑直接使用 props。 欲了解更多有关内容，请参阅[为什么 props 复制给 state 会产生 bug](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html)。

如果组件实现了 `getSnapshotBeforeUpdate()` 生命周期（不常用），则它的返回值将作为 `componentDidUpdate()` 的第三个参数 “snapshot” 参数传递。否则此参数将为 undefined。

> 注意
>
> 如果 [`shouldComponentUpdate()`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#shouldcomponentupdate) 返回值为 false，则不会调用 `componentDidUpdate()`。



#### `componentWillUnmount()`

```react
componentWillUnmount()
```

`componentWillUnmount()` 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作，例如，**清除 timer**，**取消网络请求**或**清除在 `componentDidMount()` 中创建的订阅**等。

`componentWillUnmount()` 中**不应调用 setState()**，因为该组件将永远不会重新渲染。组件实例卸载后，将永远不会再挂载它。



#### `static getDerivedStateFromError()`

```react
static getDerivedStateFromError(error)
```

此生命周期会在后代组件抛出错误后被调用。 它将抛出的错误作为参数，并返回一个值以更新 state

```react
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显示降级 UI
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级  UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

> 注意
>
> `getDerivedStateFromError()` 会在`渲染`阶段调用，因此不允许出现副作用。 如遇此类情况，请改用 `componentDidCatch()`。



#### `componentDidCatch()`

```react
componentDidCatch(error, info)
```

此生命周期在后代组件抛出错误后被调用。 它接收两个参数：

1. `error` —— 抛出的错误。
2. `info` —— 带有 `componentStack` key 的对象，其中包含[有关组件引发错误的栈信息](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/error-boundaries.html#component-stack-traces)。

`componentDidCatch()` 会在“提交”阶段被调用，因此允许执行副作用。 它应该用于记录错误之类的情况：

```react
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // 更新 state 使下一次渲染可以显示降级 UI
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    // "组件堆栈" 例子:
    //   in ComponentThatThrows (created by App)
    //   in ErrorBoundary (created by App)
    //   in div (created by App)
    //   in App
    logComponentStackToMyService(info.componentStack);
  }

  render() {
    if (this.state.hasError) {
      // 你可以渲染任何自定义的降级 UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
```

> 注意
>
> 如果发生错误，你可以通过调用 `setState` 使用 `componentDidCatch()` 渲染降级 UI，但在未来的版本中将不推荐这样做。 可以使用静态 `getDerivedStateFromError()` 来处理降级渲染。



#### 过时的生命周期方法

以下生命周期方法标记为“过时”。这些方法仍然有效，但不建议在新代码中使用它们。参阅此[博客文章](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/03/27/update-on-async-rendering.html)以了解更多有关迁移旧版生命周期方法的信息。

##### `UNSAFE_componentWillMount()`

```react
UNSAFE_componentWillMount()
```

> 注意
>
> 此生命周期之前名为 `componentWillMount`。该名称将继续使用至 React 17。可以使用 [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles) 自动更新你的组件。

`UNSAFE_componentWillMount()` 在挂载之前被调用。它在 `render()` 之前调用，因此在此方法中同步调用 `setState()` 不会触发额外渲染。通常，我们建议使用 `constructor()` 来初始化 state。

避免在此方法中引入任何副作用或订阅。如遇此种情况，请改用 `componentDidMount()`。

此方法是**服务端渲染唯一会调用的生命周期函数**。



##### `UNSAFE_componentWillReceiveProps()`

```react
UNSAFE_componentWillReceiveProps(nextProps)
```

> 注意
>
> 此生命周期之前名为 `componentWillReceiveProps`。该名称将继续使用至 React 17。可以使用 [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles) 自动更新你的组件。

> 注意:
>
> 使用此生命周期方法通常会出现 bug 和不一致性：
>
> - 如果你需要**执行副作用**（例如，数据提取或动画）以响应 props 中的更改，请改用 [`componentDidUpdate`](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#componentdidupdate) 生命周期。
> - 如果你使用 `componentWillReceiveProps` **仅在 prop 更改时重新计算某些数据**，请[使用 memoization helper 代替](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#what-about-memoization)。
> - 如果你使用 `componentWillReceiveProps` 是为了**在 prop 更改时“重置”某些 state**，请考虑使组件[完全受控](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-controlled-component)或[使用 `key` 使组件完全不受控](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html#recommendation-fully-uncontrolled-component-with-a-key) 代替。
>
> 对于其他使用场景，[请遵循此博客文章中有关派生状态的建议](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/blog/2018/06/07/you-probably-dont-need-derived-state.html)。

`UNSAFE_componentWillReceiveProps()` 会在已挂载的组件接收新的 props 之前被调用。如果你需要更新状态以响应 prop 更改（例如，重置它），你可以比较 `this.props` 和 `nextProps` 并在此方法中使用 `this.setState()` 执行 state 转换。

请注意，**如果父组件导致组件重新渲染，即使 props 没有更改，也会调用此方法。**如果只想处理更改，请确保进行当前值与变更值的比较。

在[挂载](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html#mounting)过程中，React 不会针对初始 props 调用 `UNSAFE_componentWillReceiveProps()`。**组件只会在组件的 props 更新时调用此方法**。调用 `this.setState()` 通常不会触发 `UNSAFE_componentWillReceiveProps()`。



##### `UNSAFE_componentWillUpdate()`

```react
UNSAFE_componentWillUpdate(nextProps, nextState)
```

> 注意
>
> 此生命周期之前名为 `componentWillUpdate`。该名称将继续使用至 React 17。可以使用 [`rename-unsafe-lifecycles` codemod](https://github.com/reactjs/react-codemod#rename-unsafe-lifecycles) 自动更新你的组件。

当组件收到新的 props 或 state 时，会在渲染之前调用 `UNSAFE_componentWillUpdate()`。使用此作为在更新发生之前执行准备更新的机会。初始渲染不会调用此方法。

注意，你不能此方法中调用 `this.setState()`；在 `UNSAFE_componentWillUpdate()` 返回之前，你也不应该执行任何其他操作（例如，dispatch Redux 的 action）触发对 React 组件的更新

通常，此方法可以替换为 `componentDidUpdate()`。如果你在此方法中读取 DOM 信息（例如，为了保存滚动位置），则可以将此逻辑移至 `getSnapshotBeforeUpdate()` 中。

> 注意
>
> 如果 `shouldComponentUpdate()` 返回 false，则不会调用 `UNSAFE_componentWillUpdate()`。







参考

> [React.Component](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-component.html) 









## setState

setState()更新状态的2种写法

​    1). setState(updater, [callback]),

​        updater为返回stateChange对象的函数: (state, props) => stateChange

​        接收的state和props被保证为最新的

​    2). setState(stateChange, [callback])

​        stateChange为对象,

​        callback是可选的回调函数, 在状态更新且界面更新后才执行

​    3). 总结:

​        对象方式是函数方式的简写方式

​            如果新状态不依赖于原状态 ===> 使用对象方式

​            如果新状态依赖于原状态 ===> 使用函数方式

​        如果需要在setState()后获取最新的状态数据, 在第二个callback函数中读取

```html
<div id="example"></div>

<script type="text/javascript" src="./js/react.development.js"></script>
<script type="text/javascript" src="./js/react-dom.development.js"></script>
<script type="text/javascript" src="./js/babel.min.js"></script>

<script type="text/babel">

  class A extends React.Component {

    state = {
      count: 1
    }

    test1 = () => {
      this.setState(state => ({count: state.count + 1}))
      console.log('test1 setState()之后', this.state.count)
    }

    test2 = () => {
      /*const count = this.state.count + 1
      this.setState({
        count
      })*/
      this.setState({
        count: 3
      })
      console.log('test2 setState()之后', this.state.count)
    }

    test3 = () => {
      this.setState(state => ({count: state.count + 1}), () => { // 在状态更新且界面更新之后回调
        console.log('test3 setState callback()', this.state.count)
      })
    }

    render() {
      console.log('A render()')
      return (
        <div>
          <h1>A组件: {this.state.count}</h1>
          <button onClick={this.test1}>A 测试1</button>&nbsp;&nbsp;
          <button onClick={this.test2}>A 测试2</button>&nbsp;&nbsp;
          <button onClick={this.test3}>A 测试3</button>&nbsp;&nbsp;
        </div>
      )
    }
  }

  ReactDOM.render(<A/>, document.getElementById('example'))
</script>
```



 setState()更新状态是异步还是同步的?
      1). 执行setState()的位置?
          在react控制的回调函数中: 生命周期勾子 / react事件监听回调
          非react控制的异步回调函数中: 定时器回调 / 原生事件监听回调 / promise回调 /...
      2). 异步 OR 同步?
          react相关回调中: 表现为“异步”
          其它异步回调中: 同步

   关于异步的setState()
      1). 多次调用, 如何处理?
          setState({}): 合并更新一次状态, 只调用一次render()更新界面 ---状态更新和界面更新都合并了
          setState(fn): 更新多次状态, 但只调用一次render()更新界面  ---状态更新没有合并, 但界面更新合并了
      2). 如何得到异步更新后的状态数据?
          在setState()的callback回调函数中

```html
<div id="example"></div>

<script type="text/javascript" src="./js/react.development.js"></script>
<script type="text/javascript" src="./js/react-dom.development.js"></script>
<script type="text/javascript" src="./js/babel.min.js"></script>

<script type="text/babel">
  class StateTest extends React.Component {

    state = {
      count: 0,
    }

    /*
     react事件监听回调中, setState()是异步更新状态
     */
    update1 = () => {
      console.log('update1 setState()之前', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('update1 setState()之后', this.state.count)
    }

    /*
     react生命周期勾子中, setState()是异步更新状态
     */
    componentDidMount () {
      console.log('componentDidMount setState()之前', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('componentDidMount setState()之后', this.state.count)
    }

    /*
    定时器回调 / 原生事件监听回调 / promise回调 /...
     */
    update2 = () => {
      setTimeout(() => {
        console.log('setTimeout setState()之前', this.state.count)
        this.setState(state => ({count: state.count + 1}))
        console.log('setTimeout setState()之后', this.state.count)
      })
    }
    update3 = () => {
      const h2 = this.refs.count
      h2.onclick = () => {
        console.log('onclick setState()之前', this.state.count)
        this.setState(state => ({count: state.count + 1}))
        console.log('onclick setState()之后', this.state.count)
      }
    }
    update4 = () => {
      Promise.resolve().then(value => {
        console.log('Promise setState()之前', this.state.count)
        this.setState(state => ({count: state.count + 1}))
        console.log('Promise setState()之后', this.state.count)
      })
    }


    update5 = () => {
      console.log('onclick setState()之前', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后', this.state.count)
      console.log('onclick setState()之前2', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后2', this.state.count)
    }

    update6 = () => {
      console.log('onclick setState()之前', this.state.count)
      this.setState({count: this.state.count + 1})
      console.log('onclick setState()之后', this.state.count)
      console.log('onclick setState()之前2', this.state.count)
      this.setState({count: this.state.count + 1})
      console.log('onclick setState()之后2', this.state.count)
    }

    update7 = () => {
      console.log('onclick setState()之前', this.state.count)
      this.setState({count: this.state.count + 1})
      console.log('onclick setState()之后', this.state.count)

      console.log('onclick setState()之前2', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后2', this.state.count)
    }


    render() {
      const {count} = this.state
      console.log('render()', count)
      return (
        <div>
          <h2 ref='count'>{count}</h2>
          <button onClick={this.update1}>更新1</button> ---
          <button onClick={this.update2}>更新2</button> &nbsp;
          <button onClick={this.update3}>更新3</button> &nbsp;
          <button onClick={this.update4}>更新4</button> ---
          <button onClick={this.update5}>更新5</button> &nbsp;
          <button onClick={this.update6}>更新6</button> &nbsp;
          <button onClick={this.update7}>更新7</button> &nbsp;
        </div>
      )
    }
  }

  ReactDOM.render(<StateTest/>, document.getElementById('example')) // 渲染组件标签, 内部会调用组件标签对象的render()虚拟DOM

</script>
```


  setState()更新状态是异步还是同步的?
      1). 执行setState()的位置?
          在react控制的回调函数中: 生命周期勾子 / react事件监听回调
          非react控制的异步回调函数中: 定时器回调 / 原生事件监听回调 / promise回调 /...
      2). 异步 OR 同步?
          react相关回调中: 异步
          其它异步回调中: 同步

   关于异步的setState()
      1). 多次调用, 如何处理?
          setState(fn): 更新多次状态, 但只调用一次render()更新界面  ---状态更新没有合并, 但界面更新合并了
          setState({}): 合并更新一次状态, 只调用一次render()更新界面 ---状态更新和界面更新都合并了
      2). 如何得到异步更新后的状态数据?
          在setState()的callback回调函数中

```html
<script type="text/babel">

  class StateTest extends React.Component {

    state = {
      count: 0,
    }

    /*
     react事件监听回调中, setState()是异步更新状态
     */
    update1 = () => {
      console.log('update1 setState()之前', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('update1 setState()之后', this.state.count)
    }

    /*
     react生命周期勾子中, setState()是异步更新状态
     */
    componentDidMount () {
      console.log('componentDidMount setState()之前', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('componentDidMount setState()之后', this.state.count)
    }

    /*
    定时器回调 / 原生事件监听回调 / promise回调 /...
     */
    update2 = () => {
      setTimeout(() => {
        console.log('setTimeout setState()之前', this.state.count)
        this.setState(state => ({count: state.count + 1}))
        console.log('setTimeout setState()之后', this.state.count)
      })
    }
    update3 = () => {
      const h2 = this.refs.count
      h2.onclick = () => {
        console.log('onclick setState()之前', this.state.count)
        this.setState(state => ({count: state.count + 1}))
        console.log('onclick setState()之后', this.state.count)
      }
    }
    update4 = () => {
      Promise.resolve().then(value => {
        console.log('Promise setState()之前', this.state.count)
        this.setState(state => ({count: state.count + 1}))
        console.log('Promise setState()之后', this.state.count)
      })
    }


    update5 = () => { // 这里是通过回调函数传入 state 来更新 count
      // 而传入的 state 始终是保持最新状态，所以这里的 count 状态的改变没有合并
      // 此处 count = state.count + 1，得到新的 count，然后用新的 count 再加一
      console.log('onclick setState()之前', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后', this.state.count)
      console.log('onclick setState()之前2', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后2', this.state.count)
    }

    update6 = () => { // 这里是直接取this.state, 又因为异步调用，
      // 所以 count 状态的改变会合并，会以最后一次的状态改变为准,
      // 此处的 count = this.state.count + 10
      console.log('onclick setState()之前', this.state.count)
      this.setState({count: this.state.count + 1})
      console.log('onclick setState()之后', this.state.count)
      console.log('onclick setState()之前2', this.state.count)
      this.setState({count: this.state.count + 10})
      console.log('onclick setState()之后2', this.state.count)
    }

    update7 = () => {
      console.log('onclick setState()之前', this.state.count)
      this.setState({count: this.state.count + 1})
      console.log('onclick setState()之后', this.state.count)

      console.log('onclick setState()之前2', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后2', this.state.count)
    }

    update8 = () => {
      console.log('onclick setState()之前2', this.state.count)
      this.setState(state => ({count: state.count + 1}))
      console.log('onclick setState()之后2', this.state.count)

      console.log('onclick setState()之前', this.state.count)
      this.setState({count: this.state.count + 1})
      console.log('onclick setState()之后', this.state.count)
    }


    render() {
      const {count} = this.state
      console.log('render()', count)
      return (
        <div>
          <h2 ref='count'>{count}</h2>
          <button onClick={this.update1}>更新1</button> ---
          <button onClick={this.update2}>更新2</button> &nbsp;
          <button onClick={this.update3}>更新3</button> &nbsp;
          <button onClick={this.update4}>更新4</button> ---
          <button onClick={this.update5}>更新5</button> &nbsp;
          <button onClick={this.update6}>更新6</button> &nbsp;
          <button onClick={this.update7}>更新7</button> &nbsp;
          <button onClick={this.update8}>更新8</button> &nbsp;
        </div>
      )
    }
  }

  ReactDOM.render(<StateTest/>, document.getElementById('example')) // 渲染组件标签, 内部会调用组件标签对象的render()虚拟DOM

</script>
```



```html
<script type="text/babel">
  class StateTest extends React.Component {

    state = {
      count: 0,
    }

    componentDidMount() {
      this.setState({count: this.state.count + 1})
      this.setState({count: this.state.count + 1})
      console.log(this.state.count) // 2 ==> 0

      this.setState(state => ({count: state.count + 1}))
      this.setState(state => ({count: state.count + 1}))
      console.log(this.state.count) // 3 ==> 0

      setTimeout(() => {
        this.setState({count: this.state.count + 1})
        console.log('timeout', this.state.count) // 10 ==> 6

        this.setState({count: this.state.count + 1})
        console.log('timeout', this.state.count) // 12 ==> 7
      }, 0)

      Promise.resolve().then(value => {
        this.setState({count: this.state.count + 1})
        console.log('promise', this.state.count)  // 6 ==>4

        this.setState({count: this.state.count + 1})
        console.log('promise', this.state.count) // 8 ==> 5
      })
    }

    render() {
      const count = this.state.count
      console.log('render', count)  // 1 ==> 0   4 ==>3   5 ==>4  7 ==>5  9 ==>6  11 ==>7
      return (
        <div>
          <p>{count}</p>
        </div>
      )
    }
  }

  ReactDOM.render(<StateTest/>, document.getElementById('example'))

</script>
```



### PureComponent

  1. Component存在的问题?
      1). 父组件重新render(), 当前组件也会重新执行render(), 即使没有任何变化
      2). 当前组件setState(), 重新执行render(), 即使state没有任何变化

  2. 解决Component存在的问题
      1). 原因: 组件的componentShouldUpdate()默认返回true, 即使数据没有变化render()都会重新执行
      2). 办法1: 重写shouldComponentUpdate(), 判断如果数据有变化返回true, 否则返回false
      3). 办法2: 使用PureComponent代替Component
      4). 说明: 一般都使用PureComponent来优化组件性能

  3. PureComponent的基本原理
      1). 重写实现shouldComponentUpdate()
      2). 对组件的新/旧state和props中的数据进行浅比较, 如果都没有变化, 返回false, 否则返回true
      3). 一旦componentShouldUpdate()返回false不再执行用于更新的render()

  4. 面试题:
      组件的哪个生命周期勾子能实现组件优化?
      PureComponent的原理?
      区别Component与PureComponent?

```html
<script type="text/babel">

  class A extends React.Component {

    state = {
      m1: {count: 1}
    }

    test1 = () => {
      /*this.setState(state => ({
        m1: {count: state.m1.count + 1}
      }))*/

//      const m1 = this.state.m1
//      m1.count = 2
// 如果用了 PureComponent,就不能这样更新 m1对象，否则不会重新 render（），
//      this.setState({m1}) // 因为 PureComponent 的 shouldComponentUpdate()只是浅比较，只改变对象 m1的内部数据不会引发state的改变

//      this.setState({m1: {...m1}})

      this.setState({})
    }

    render() {
      console.log('A render()')
      return (
        <div>
          <h1>A组件: m1={this.state.m1.count}</h1>
          <button onClick={this.test1}>A 测试1</button>
          <B m1={this.state.m1}/>
        </div>
      )
    }
  }

  class B extends React.PureComponent {

    state = {
      m2: 1
    }

    test2 = () => {
      this.setState({})
    }

    /*
    用来决定当前组件是否应该重新render(), 如果返回true, 就会去重新render(), 否则结束
     */
    /*shouldComponentUpdate (nextProps, nextState) {
      console.log('shouldComponentUpdate()')
      // 比较新旧props中的和state数据, 如果没有一个变化的返回false, 否则true
      if(this.props.m1===nextProps.m1 && this.state.m2===nextState.m2) {
        return false
      } else {
        return true
      }

      // return true  // Component中的默认为true
    }*/

    render() {
      console.log('B render()')
      return (
        <div>
          <h1>B组件: m2={this.state.m2}, m1.count={this.props.m1.count}</h1>
          <button onClick={this.test2}>B 测试2</button>
        </div>
      )
    }
  }

  ReactDOM.render(<A/>, document.getElementById('example'))
</script>
```



### 列表 key 的问题

   面试题:
      1). react/vue中的key的作用/内部原理
      2). 为什么列表的key尽量不要用index

   1. 虚拟DOM的key的作用?
      1). 简单的说: key是虚拟DOM对象的标识, 在更新显示时key起着极其重要的作用
      2). 详细的说: 当列表数组中的数据发生变化生成新的虚拟DOM后, React进行新旧虚拟DOM的diff比较
          a. key没有变
              item数据没变, 直接使用原来的真实DOM
              item数据变了, 对原来的真实DOM进行数据更新
          b. key变了
              销毁原来的真实DOM, 根据item数据创建新的真实DOM显示(即使item数据没有变)

   2. key为index的问题
      1). 添加/删除/排序 => 产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低
      2). 如果item界面还有输入框 => 产生错误的真实DOM更新 ==> 界面有问题
      注意: 如果不存在添加/删除/排序操作, 用index没有问题

   3. 解决:
      使用item数据的标识数据作为key, 比如id属性值

   4. 什么时候可以用index作为key
      不能有: 添加/删除/排序
      可以有: 更新元素内部的数据

```js
  const time = Date.now()
  class PersonList extends React.Component {

    state = {
      persons: [
        {id: time, name: 'Tom', age: 13},
        {id: time +1, name: 'Jack', age: 12},
      ]
    }

    add = () => {
      const persons = [...this.state.persons]
      persons.unshift({id: Date.now(), name: 'Bob', age: 14})
      this.setState({persons})
    }

    remove = () => {
      const persons = [...this.state.persons]
      persons.shift()
      this.setState({persons})
    }

    sort = () => {
      const persons = [...this.state.persons]
      persons.sort((p1, p2) => p1.age - p2.age)
      this.setState({persons})
    }

    render() {
      const persons = this.state.persons
      return (
        <div>
          <div>
            <button onClick={this.add}>add first</button>
            &nbsp;
            <button onClick={this.remove}>remove first</button>
            &nbsp;
            <button onClick={this.sort}>sort</button>
          </div>

          <h2>使用id作为key</h2>
          <ul>
            {
              persons.map((p, index) =>
                <li key={p.id}>{p.id}--{p.name}--{p.age}--<input/></li>
              )
            }
          </ul>

          <h2>使用index作为key</h2>
          <ul>
            {
              persons.map((p, index) =>
                <li key={index}>{p.id}--{p.name}--{p.age}--<input/></li>
              )
            }
          </ul>
        </div>
      )
    }
  }

  ReactDOM.render(
    <PersonList/>,
    document.getElementById('root')
  )
```









