## [React in patterns](../../README.md) / Composition

* [Source code](https://github.com/krasimir/react-in-patterns/tree/master/patterns/composition/src)

---

One of the biggest benefits of [React](http://krasimirtsonev.com/blog/article/The-bare-minimum-to-work-with-React) is composability. I personally don't know a framework that offers such an easy way to create and combine components. In this section we will explore few composition techniques which proved to work well.

最好的好处之一是它的可组合性。就个人而言我并不知道有其它框架也提供了这么简单的创建和组合组件的功能。这个部分我们将来探索下那么已被证明可以很好的工作的一些技术。

Let's get a simple example. Let's say that we have an application with a header and we want to place a navigation inside. We have three React components - `App`, `Header` and `Navigation`. They have to be nested into each other so we end up with the following markup:

先来个粟子。假设我们程序的上面一部分准备放一个导航功能。现在有三个 React 组件 - `App`， `Header` 和 `Navigation`。他们是多层嵌套的关系，因此我们得到下代码的代码：

```js
<App>
  <Header>
    <Navigation> ... </Navigation>
  </Header>
</App>
```

The trivial approach for combining these components is to reference them in the places where we need them.

最简单的组合组件的方法就是在需要的地方引用它。

```js
// app.jsx
import Header from './Header.jsx';

export default class App extends React.Component {
  render() {
    return <Header />;
  }
}

// Header.jsx
import Navigation from './Navigation.jsx';

export default class Header extends React.Component {
  render() {
    return <header><Navigation /></header>;
  }
}

// Navigation.jsx
export default class Navigation extends React.Component {
  render() {
    return (<nav> ... </nav>);
  }
}
```

However, following this pattern we introduce several problems:
但是，我们说一下这种方式存在的几个问题：

* We may consider the `App` as a place where we wire stuff, as an entry point. So, it's a good place for such composition. The `Header` though may have other elements like a logo, search field or a slogan. It will be nice if they are passed somehow from the outside so we don't create a hard-coded dependency. What if we need the same `Header` component but without the `Navigation`. We can't easily achieve that because we have the two bound tightly together.
* 我们可以认为 `App` 是一个联线各个功能的地方，一个入口。因此在这组织是代码是个不错的地方。`Header` 可能会有其它的组件,如logo、 查找框或一个口号文案之类的。这些组件最好是也能从外部传入，这样就不必有硬编码依赖。我们需要的`Header`组件只需要移除`Navigation`就很好了。不然很难达到这个目的，因为他们偶合的太紧。
* It's difficult to test. We may have some business logic in the `Header` and in order to test it we have to create an instance of the component. However, because it imports other components we will probably create instance s of those components too and it becomes heavy for testing. We may break our `Header` test by doing something wrong in the `Navigation` component which is totally misleading. *(Note: while testing the [shallow rendering](https://facebook.github.io/react/docs/test-utils.html#shallow-rendering) solves this problem by rendering only the `Header` without its nested children.)*
* 难于测试。在 `Header` 中我们可能会有些业务逻辑，为了测试它，我们必须创建它的实例。但是，由于它引入了其它的组件，我们也可能需要创建这些组件的实例，测试将变得越来越重。`Navigation` 组件中的错误可能导致 `Header` 的测试中断，而这将误导我们。

### Using React's children API

In React we have the handy [`this.props.children`](https://facebook.github.io/react/docs/multiple-components.html#children). That's how the parent reads/accesses its children. This API will make our Header agnostic and dependency-free:
React 非常的灵活 [`this.props.children`](https://facebook.github.io/react/docs/multiple-components.html#children)。这就是父结点读取/访问其子结点的方式。这个 API 让我的们 Header 不可知且无依赖。

```js
// App.jsx
export default class App extends React.Component {
  render() {
    return (
      <Header>
        <Navigation />
      </Header>
    );
  }
}

// Header.jsx
export default class Header extends React.Component {
  render() {
    return <header>{ this.props.children }</header>;
  }
};
```

It's also easy to test because we may render the `Header` with an empty `<div>`. This will isolate the component and will let us focus on only one piece of our application.
同样它非常好测试，我们可以使用一个空的 `<div>` 来渲染 `Header`。这将隔离其余组件，并让我们只专注于程序的一部分。

### Passing a child as a property
### 传递子结点做为属性

Every React component receive props. It's nice that these props may contain all kind of data. Even other components.
所有的 React 组件都接收属性。好消息是这些属性可能包含所有类型的数据，甚至是别的组件。

```js
// App.jsx
class App extends React.Component {
  render() {
    var title = <h1>Hello there!</h1>;

    return (
      <Header title={ title }>
        <Navigation />
      </Header>
    );
  }
};

// Header.jsx
export default class Header extends React.Component {
  render() {
    return (
      <header>
        { this.props.title }
        <hr />
        { this.props.children }
      </header>
    );
  }
};

```

This technique is helpful when we have a mix between components that exist inside the `Header` and components that have to be provided from the outside.
这个技巧在 `Header` 内的组件存在可由多个不同的组件组成时，且组件必须由外面传入时情景下非常有帮助。


个人理解：
* 这个模式不依赖于状态， 是个无状态的组件；也不依赖于其它组件；
* 看上面示例 `Header` 中加了一个共用的 title ，再之后才是children，也就是说有组件有部分功能需要重用，部分是变化的。也就这种场景适合使用这种模式，如果是全部变化，这个组件其实没存在的必要，直接写就是了。（有状态的组件切换可以参考viewStack）
