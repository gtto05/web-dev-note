# React

## React 基础

### React 简介

React 特点

1. 声明式
2. 组件化
3. React Native 编写原生应用
4. 高效 Diff 算法

React 高效的原因

1. 使用虚拟 DOM, 不总是直接操作真实 DOM
2. DOM Diff 算法，最小化页面重绘

> **注意**：React 并不会提高渲染速度，反而可能会增加渲染速度，真正高效的原因是它能有效的减少**渲染次数**

创建虚拟 DOM 的两种方法

1. JS 创建（不推荐）

```js
// 1. 创建虚拟DOM——嵌套DOM
const VDOM = React.createElement('h1',{class:'title',React.createElement('span',{},'hello React!')})
// 2. 渲染虚拟DOM到页面
ReactDOM.render(VDOM,document.getElementById('root'))
```

2. JSX 创建

```js
// 1. 创建虚拟DOM——嵌套DOM
const VDOM = (
  <h1 className="title">
    <span>hello React!</span>
  </h1>
);
// 2. 渲染虚拟DOM到页面
ReactDOM.render(VDOM, document.getElementById("root"));
```

> 可以看出，JSX 的写法明显更符合开发人员的习惯，当出现多重嵌套时，JS 创建就会麻烦很多。但是，浏览器是不会识别 JSX 语法的，JSX 只是帮我们做了一层编译，当我们写完 JSX 代码后，最终是要被编译 JS 的书写方法
> 此时我们可以打印下虚拟 DOM，与真实 DOM 有什么优势？

虚拟 DOM

1. 本质还是 Object 类型的对象（一般对象）
2. 虚拟 DOM 比较轻，没有真实 DOM 那么多属性
3. 虚拟 DOM 最终会被 React 转化为真实 DOM

### JSX 语法规则

规则

1. 定义虚拟 DOM 时，不要写引号
2. 标签混入 JS 表达式用`{}`
3. 样式类名用`className`代替原生的`class`
4. 内联样式用配置对象形式`{{}}`去写
5. 只有一个根标签
6. 标签必须闭合
7. 标签首字母大小写含义不同
   - 若小写字母开头，则该标签转为 html 中同名元素，若 html 中无该标签对应的同名元素，则**报错**
   - 若大写字母开头，React 就去渲染对应组件，若组件未定义，也**报错**

### 创建组件

React 定义组件

1. 函数式组件

2. 类式组件

> 我们很容易理解，ReactDOM.render 之后，React 会解析组件标签，找到 MyComponent 组件，当发现是函数定义的，随后会调用函数，将返回的虚拟 DOM 转化为真是 DOM 并渲染到页面
>
> 如果是类式组件呢？肯定是先 new 出来该类的实例，并通过调用该实例的原型上的 render 方法返回虚拟 DOM
>
> 那么，组件的 render 放在哪呢？Mycomponent 原型对象上，供实例使用。
> 组件中 render 中的 this 是谁？Mycomponent 实例对象

模块与模块化

1. 模块

> **思考**： 什么是模块？

在 React 中，一般就是一个 js 文件，向外提供特定功能的 js 程序

> **思考** 为什么要拆成模块呢？

随着业务逻辑的增加，代码越来越多且复杂，所以我们需要复用并简化 js 的编写，提高 js 运行效率

2. 模块化

当应用的 js 都以模块来编写，这个应用就是一个模块化应用

组件与组件化

1. 组件（局部功能的资源集合）
2. 组件化（组件方式实现的应用就是组件化）

### 面向组件编程

#### 组件三大属性 state

1. `state`是组件中很重要的属性，值为对象
2. 状态驱动视图的更新（重新状态会重新渲染组件）
3. 不可变数据

> **注意**：组件中 render 方法中 this 是组件实例对象，自定义方法中 this 是 undefined。解决方法有很多种，基本思想就是改变 this 指向

#### 组件三大属性 props

1. 每个组件都会有 props 属性
2. 组件标签所有的属性都保存在 props 中

类组件使用 props

```js
class UserList extends React.Component {
  render() {
    // console.log(this)
    const { name, age, sex } = this.props;
    return (
      <ul>
        <li>姓名：{name}</li>
        <li>性别：{sex}</li>
        <li>年龄：{age + 1}</li>
      </ul>
    );
  }
}
// 渲染组件到页面
ReactDOM.render(
  <UserList name="hansum" age={18} sex="男" />,
  document.getElementById("root")
);
```

你会发现上面方式传参数，看起来会很复杂，我们可以提前定义一个对象，通过 React 中 ...来展开对象放到组件上

```js
const userInfo = { name: "hansum", age: 18, sex: "男" };
ReactDOM.render(<UserList {...userInfo} />, document.getElementById("root"));
```

这样看起来代码会简洁很多！

函数式组件使用 props

```js
function UserList(props) {
  const { name, sex, age } = props;
  return (
    <ul>
      <li>姓名：{name}</li>
      <li>性别：{sex}</li>
      <li>年龄：{age + 1}</li>
    </ul>
  );
}
// 限制传参类型
Person.propTypes = {
  name: PropTypes.string.isRequired,
  sex: PropTypes.string,
  age: PropTypes.number,
};
// 指定默认标签属性值
Person.defaultProps = {
  sex: "男",
  age: "18",
};
ReactDom.render(<UserList {...userInfo} />, document.getElementById("root"));
```

#### 组件三大属性 refs

> React 本意是不让我们操作真实 DOM，有时候我们需要获取 DOM 节点上的值，所以我们需要一个东西来标识它

字符串形式的 ref（不推荐）

```js
class InputPanel extends React.Component {
  showData = () => {
    const { input1 } = this.refs;
    alert(input1.value);
  };
  showData = () => {
    const { input2 } = this.refs;
    alert(input2.value);
  };
  render() {
    return (
      <div>
        <input ref="input1" type="text" placeholder="点击按钮提示输入框内容" />
        &nbsp;
        <button onClick={this.showData}>点击按钮获取内容</button>&nbsp;
        <input
          ref="input2"
          onBlur={this.showData2}
          type="text"
          placeholder="失去焦点获取内容"
        />
      </div>
    );
  }
}
```

回调形式的 ref

```js
class InputPanel extends React.Component {
  showData = () => {
    const { input1 } = this;
    alert(input1.value);
  };
  showData = () => {
    const { input2 } = this;
    alert(input2.value);
  };
  render() {
    return (
      <div>
        <input
          ref={(inputNode) => (this.input1 = inputNode)}
          type="text"
          placeholder="点击按钮提示输入框内容"
        />
        &nbsp;
        <button onClick={this.showData}>点击按钮获取内容</button>&nbsp;
        <input
          ref={(inputNode) => (this.input2 = inputNode)}
          onBlur={this.showData2}
          type="text"
          placeholder="失去焦点获取内容"
        />
      </div>
    );
  }
}
```

createRef 创建 ref 容器（最推荐）

```js
// 专人专用
const input1Ref = createRef();
const input2Ref = createRef();
class InputPanel extends React.Component {
  showData = () => {
    alert(this.inputRef1.current.value);
  };
  showData = () => {
    alert(this.inputRef2.current.value);
  };
  render() {
    return (
      <div>
        <input
          ref={input1Ref}
          type="text"
          placeholder="点击按钮提示输入框内容"
        />
        &nbsp;
        <button onClick={this.showData}>点击按钮获取内容</button>&nbsp;
        <input
          ref={input2Ref}
          onBlur={this.showData2}
          type="text"
          placeholder="失去焦点获取内容"
        />
      </div>
    );
  }
}
```

#### 事件处理与收集表单数据

事件处理

> React 中通过 onEventName 属性指定事件处理函数，跟原生很类似
> 通过 event.target 得到发生事件的 DOM 元素对象——目的是不要过度使用 ref

- React 使用的是自定义事件，而不是原生事件——为了更好的兼容性
- React 的事件是通过事件委派的方式处理的——为了更高效

收集表单数据

> React 中收集分两种，一种是通过节点拿到数据，称为非受控组件；一种是通过表单的`onChange`事件，动态维护到 React 状态中拿到数据，称为受控组件

- 受控组件

```js
class Login extends React.Component {
  constructor(props) {
    super(props);
    state = {
      username: "",
      password: "",
    };
  }
  saveUserName = (e) => {
    this.setState({ username: e.target.value });
  };
  savePassword = (e) => {
    this.setState({ password: e.target.value });
  };
  handleSubmit = (e) => {
    e.preventDefault();
    const { username, password } = this.state;
    alert(`你输入的用户名是：${username}，你输入的密码是：${password}`);
  };
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        用户名：
        <input onChange={this.saveUserName} type="text" name="username" />
        密码：
        <input onChange={this.savePassword} type="password" name="password" />
        <button>登录</button>
      </form>
    );
  }
}
```

你会发现，此时定义了两个功能差不多的函数，有点浪费，我们可以利用柯里化函数优化，下面有记录

```js
class Login extends React.Component {
  constructor(props) {
    super(props);
    state = {
      username: "",
      password: "",
    };
  }
  saveInfo = (attr) => (e) => {
    this.setState({ [attr]: e.target.value });
  };
  handleSubmit = (e) => {
    e.preventDefault();
    const { username, password } = this.state;
    alert(`你输入的用户名是：${username}，你输入的密码是：${password}`);
  };
  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        用户名：
        <input
          onChange={this.saveInfo("username")}
          type="text"
          name="username"
        />
        密码：
        <input
          onChange={this.saveInfo("password")}
          type="password"
          name="password"
        />
        <button>登录</button>
      </form>
    );
  }
}
```

- 非受控组件（同上面 ref）

#### 高阶函数与柯里化函数

高阶函数

> 满足一下条件之一，就称为高阶函数

1. 若该函数接收的参数是一个函数
2. 若该函数调用的返回值依然是一个函数
> 常见的高阶函数有：Promise、setTimeout、arr.map()等等

柯里化函数

> 通过函数调用继续返回函数的形式，实现对参数的最后统一处理的函数编程形式
