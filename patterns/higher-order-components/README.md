## [React in patterns](../../README.md) / Higher-order components

* [Source code](https://github.com/krasimir/react-in-patterns/tree/master/patterns/higher-order-components/src)

---

### Creating a higher-order component

Higher-order components look really similar to the [decorator design pattern](http://robdodson.me/javascript-design-patterns-decorator/). It is wrapping a component and attaching some new functionalities or props to it.
高阶组件看上去非常类似[装饰器模式](http://robdodson.me/javascript-design-patterns-decorator/)。它封装组件并绑定上新的函数或属性。

Here is a function that returns a higher-order component:
下面这个函数就返回了一个高阶组件：

```js
var enhanceComponent = (Component) =>
  class Enhance extends React.Component {
    render() {
      return (
        <Component
          {...this.state}
          {...this.props}
        />
      )
    }
  };

export default enhanceComponent;
```

Very often we expose a factory function that accepts our original component and when called returns the enhanced/wrapped version of it. For example:
我们经常暴露一个接收原始组件的工厂函数，当调用时返回这个原始组件的增强/封装的版本。比如：

```js
var OriginalComponent = () => <p>Hello world.</p>;
var EnhancedComponent = enhanceComponent(OriginalComponent);

class App extends React.Component {
  render() {
    return <EnhancedComponent />;
  }
};
```

The very first thing that the higher-order component does is to render the original component. It's a good practice to pass the `state` and `props` to it. This is helpful when we want to proxy data and use the higher-order component as it is our original component.
高阶组件做的第一件就是渲染原始组件。传递 `state` 和 `props`给它是一个好的习惯。当我们想代理数据时，使用高阶组件来代替原始组件是非常有帮助的。

[Dan Abramov](https://github.com/gaearon) made a really [good point](https://github.com/krasimir/react-in-patterns/issues/12) that the actual creation of the higher-order component (i.e. calling a function like `enhanceComponent`) should happen at a component definition level. Or in other words, it's a bad practice to do it inside another React component because it's slow and we basically generate a new type every time.
[Dan Abramov](https://github.com/gaearon)指出，真实创建高阶组件的时机应该是在组件定义的时候。换言之，如果在其它组件内部创建则不是好的实践因为它会很慢并且每次都会重新生成。

### Benefits
### 优点

The higher-order component gives us control on the input. The data that we want to send as props. Let's say that we have a configuration setting that `OriginalComponent` needs:
高阶组件使我们控制输入。即我们想将哪些数据做为属性传递。假设我们有个配置需设置 `OriginalComponent`

```js
var config = require('path/to/configuration');

var enhanceComponent = (Component) =>
  class Enhance extends React.Component {
    render() {
      return (
        <Component
          {...this.state}
          {...this.props}
          title={ config.appTitle }
        />
      )
    }
  };
```

The knowledge for the configuration is hidden into the higher-order component. `OriginalComponent` knows only that it receives a `prop` called `title`. Where it comes from it is not important. That's a huge advantage because it helps us testing the component in an isolation and provides nice mechanism for mocking. Here is how the `title` may be used:
配置的内容被隐藏在高阶组件中。 `OriginalComponent` 只知道接收到了一个 `title` 的属性。并不关心这个属性是从哪传来的。这是个巨大的优势，因为它帮助我们在测试时隔离其它组件并提供了一个良好的模仿机制。下边是 `title` 可能被使用的方法：

```js
var OriginalComponent  = (props) => <p>{ props.title }</p>;
```

### What's next

Check out [dependency injection](https://github.com/krasimir/react-in-patterns/tree/master/patterns/dependency-injection) and [presentational and container ](https://github.com/krasimir/react-in-patterns/tree/master/patterns/presentational-and-container) sections. There are good example of higher-order components.
