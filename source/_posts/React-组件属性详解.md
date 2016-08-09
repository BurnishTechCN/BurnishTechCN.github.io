title: React - 组件属性详解
date: 2016-08-09 10:19:17
permalink: react-1
category: "FrontEnd"
tags: React
---

[React](https://github.com/facebook/react) 是用于构建用户界面的 javascript 库，笔者在写这边文章时，其最新版是 v15.3.0。

<!-- more -->

# 引言

作为一款专注构建用户界面的 javascript 库，它无疑是近两年来最流行的 javascript 库。许多人使用 React 作为 MVC 架构的 V 层，它是纯前端的 javascript 库。它有两个显著特点：

* **虚拟 DOM**

  React 为了超高的性能而是用虚拟 DOM 作为其实现，让用户无感的更新 UI 和数据。

* **单向数据流**
  React 实现了单向数据流，采用声明式的 JSX 语法，依靠数据驱动 UI，比传统数据绑定更简单。

# 几个重要的概念

## component (组件)

* 用 React 构建 UI 时, 页面上所有的UI控件都可以看做是一个 component，比如一个弹窗、一个侧边栏，又或者一个表单。
* 组件可以看成是一个状态机，由 `state` 和 `props` 维持状态。
* 组件名称首字母必须大写，如: `PersonBox` 而不是 `personBox` 或者 `person_box`。

## JSX

* React 中，HTML 代码与 js 代码是混合在一起写的，初学者看起来可能有些疯狂，中间看起来像 HTML 的代码就是 JSX，它很像一般的 HTML ，不过它并不是比如它不支持`class=`，而是`className=`等。
* JSX 最终会被解析成 javascript 代码，你可以使用 `.js` 或者 `.jsx` 为文件后缀名。

  ```
     // CommentBox.js(x)
     var CommentBox = React.createClass({
       render: function() {
         return (
           <div className="commentBox">
             Hello, world! I am a CommentBox.
           </div>
         );
       }
     });

     ReactDOM.render(
       <CommentBox />,
       document.getElementById('content')
     );

   ```

## 生命周期

一个 component 的执行需要经过几个过程，你可以清晰的知道 component 在整个过程中是如何执行的。比如：

* `componentWillMount`
* `componentDidMount`
* `componentWillReceiveProps`
* `shouldComponentUpdate`
* `componentWillUpdate`
* `componentDidUpdate`
* `componentWillUnmount`

## state

 * 用来维持 component 内部状态，由`getInitialState` 方法初始化，如`this.state.isHide` `this.state.disabled` 等。
 * 当检测到 `state` 变化时，会重新执行 `render` 方法。

## props

* 也是用来维护组件内部状态，由父级组件传入，如 `<CommentBox userName={'zhangsan'} />` 用 `this.props.userName` 取到。
* `props` 请使用驼峰命名法如: `userName` 而不是 `user_name` 或者 `username`。
* 当检测到 `props` 变化时, 会重新执行 `render` 和 `componentWillReciveProps` 方法。

# 组件方法

## render [Function]

* `render` 方法是每个 component 所必须的。
* `render` 返回值只能是 JSX、`null` 或者 `false`。
* `render` 是纯粹的，不能够在 render 里面修改 `state` 或者读写 DOM 信息，也不能与浏览器交互如调用 `setTiemout` 等。
* 当组件检测到 `state` 或 `props` 改变时，`render` 会重新被调用。

## getInitialState [Function]

* 在 component 挂载之前调用一次，返回值将作为 `this.state.xxx` 的初始值。
* 如果使用 ES6 (注: 以下例子中都会使用ES6 来编写) 来创建组件，请在 `constructor` 中指定，如:

    ```
    import React, { PropTypes } from 'react';

    export default class PersonBox extends React.Component {
      // 定义需要的props校验
      static propsTypes = {
        userName: PropTypes.string.isRequired,
      };
      // 调用父类构造器进行初始化
      constructor(props) {
        super(props);
        // getInitialState
        this.state = { count: 0 };
      }
      // 定义jsx渲染
      render() {
        const { userName } = this.props;
        return (
          <div className="comment-box">
            Hello, world! I am {userName}.
          </div>
        );
      }
    }
    ```

## getDefaultProps [Function]

* 在 component 创建的时候调用一次，如果父组件没有指定 props 中的某个键，则此返回的对象中的相应属性将会合并到 `this.props` 中。
* 如:

    ```
    import React, { PropTypes } from 'react';

    export default class PersonBox extends React.Component {

      static propsTypes = {
        userName: PropTypes.string.isRequired,
      };

      getDefaultProps() {
        return {
          userName: '张三',
        };
      }

      render() {
        // userName is 张三
        const { userName } = this.props;
        return (
          <div className="comment-box">
            Hello, world! I am {userName}.
          </div>
        );
      }
    }
    ```
## propTypes [Object]

* `propTypes` 用来验证传入到组件的 props。如果父级组件传入的 `props` 不合法，将会提示 `warning` 或者无法工作，如:

    ```
    import React, { PropTypes } from 'react';
    import PersonBox from 'PersonBox';

    export default class PersonList extends React.Component {

      // 上例 PersonBox 组件，userName 期待一个 string 却输入 number
      // 将提示 `warning`
      render() {
        return (
          <PersonBox userName={12345} />
        );
      }
    }
    ```

## statics [Object]

* `statics` 对象允许你定义静态的方法，这些静态的方法可以在组件类上调用。
* `statics` 对象不能调用 `state` 和 `props`，如果你真的想调用的话，可以通过参入传入，例如:

    ```
    var PersonBox = React.createClass({
      statics: {
        customMethod: function(foo) {
          return foo === 'bar';
        }
      },
      render: function() {
      }
    });

    PersonBox.customMethod('bar');  // true

    // ES6 写法
    class PersonBox extends React.Component {
      constructor(props) {
        this.state = {
          welcome: 'Hello!',
        };
      }

      static customMethod(state) {
        // undefined
        console.info(this.state);
        // { welcome: 'Hello!' }
        console.info(state);
      }

      otherNotStaticMethod() {
        // { welcome: 'Hello!' }
        console.info(this.state);
      }
    }
    ```

## displayName [String]

* 字符串用于输出调试信息。
* 不推荐使用 `displayName` 来调用 component, 而是使用组件名称引用。

 ```
 // bad
 export default React.createClass({
   displayName: 'PersonBox',
 });

 // good
 export default class PersonBox extends React.Component {

 }

 ```

# 组件生命周期

## componentWillMount  [Function]

* 组件加载的整个过程中只调用一次，在初始化渲染执行之前立刻调用。
* 如果在这个方法内使用了 `setState`, 那么 `render` 方法将会重新执行一次。

## componentDidMount  [Function]

* 在初始化渲染执行之后立刻调用一次， 只执行一次。
* 你可以使用 `ReactDOM.getDOMNode()` 来获取 DOM 元素。
* 如果你使用了如 `jquery` 等三方库，请在 `componentDidMount` 后执行。
* 你可以在该方法内执行 `setState` 操作。

## componentWillReceiveProps(nextProps) [Function]

* 在组件接收到新的 `props` 的时候调用。在初始化渲染的时候，该方法不会调用。
* 在该函数中调用 `setState` 将不会引起第二次渲染。

    ```
    componentWillReceiveProps(nextProps) {
      // 组件接收到的新的 props
      console.info(nextProps !== this.props);
      this.setState({
        // cannot work!
        nextState: 'new State'
      });
    }
    ```
* 该属性配合 [redux](https://github.com/reactjs/redux) 使用时非常有用，因为当 `redux` 触发新的 `action` 时, 组件会接收到 `nextProps`。

## shouldComponentUpdate(nextProps, nextState) [function]

不常用，返回 `false` 则 `render` 方法不会被重新调用，实际应用中较少使用，因为 React 全局渲染是高效的。

## componentWillUpdate(nextProps, nextState) [Function]

* 不常用，接收到新的 props 或者 state 之前立刻调用。
* 不能在该方法中使用 `setState`，如果需要更新`state`来响应某个`prop`的改变，请使用 `componentWillReceiveProps`。

## componentDidUpdate(prevProps, prevState)  [Function]

不常用，在组件的更新已经同步到 DOM 中之后立刻被调用。

##  componentWillUnmount [Function]

* 在组件从 DOM 中移除的时候立刻被调用。
* 常用来执行页面切换后的后续操作，如 `clearInterval` 等。

# 尾声

  以上就是 React 组件属性的概览说明了，想要了解更多请查看[React官方文档](https://facebook.github.io/react/)。并且虽然 React 构建 UI 非常出色，但是还是需要手工管理其 `state` 和 `props`。

  在实际运用中经常会跟 flux 模型结合使用，来自动化管理状态，推荐的使用 `redux` 来配合使用。在后续的章节会陆续讲到。

  如果你决定使用 React 来构建你的应用的话，你还会涉及到一些名词如 `babel` `webpack` 等，我们提供了一个[React 脚手架](https://github.com/BurnishTechCN/react)，来快速构建一个高效的 react 项目，希望可以帮到你。



