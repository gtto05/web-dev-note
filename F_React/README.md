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

### 生命周期函数

生命周期函数分三个阶段

1. 初始化阶段（由 ReactDOM.render()触发）——初次渲染

- constructor
- componentWillMount
- render
- componentDidMount——**最常用**
  > 一般在这个钩子做一些初始化操作，比如开启定时器，发送网络请求，订阅消息等

2. 更新阶段（由组件内部的 setState 或者父组件的 render 触发）

- shouldComponentUpdate——组件是否应该更新，通常用来做性能优化
- componentWillUpdate——组件将要更新
- render
- componentDidUpdate

3. 卸载组件（由 ReactDOM.unmountComponentAtNode()触发）

- componentWillUnmount——**常用**
  > 一般在这个钩子做一些收尾的事情，比如：关闭定时器，取消订阅等

React 生命周期（新）

1. 初始化阶段

- constructor
- getDerivedStateFromProps——从 Props 获得派生状态
- render
- componentDidMount——**常用**

2. 更新阶段

- getDerivedStateFromProps
- shouldComponentUpdate
- render
- getSnapshotBeforeUpdate——在更新前获得快照
- componentDidUpdate

3. 卸载组件（同旧）

### React|Vue 中的 key

> **思考** key 有什么作用？，为什么最好不用 index

1. 虚拟 DOM 中 key 的作用

- 简单来说，key 就是虚拟 DOM 对象的标识，在更新显示时 key 起着极其重要的作用
- 详细的说，当状态中的数据发生改变，react 会更具新数据生成新虚拟 DOM,随后 React 进行新旧 diff 对比
- 旧虚拟 DOM 找到与新虚拟 DOM 相同 key
  - 若虚拟 DOM 的内容没变，直接使用之前的真实 DOM
  - 若虚拟 DOM 的内容变了，则生成新的真实 DOM,随后替换掉旧的真实 DOM
- 旧虚拟 DOM 中未找到相同的 key，根据数据创建新的真实 DOM

2. 用 index 作为 key 可能会引发以下问题

- 若对数据进行逆序添加、删除等破坏顺序操作，会产生没有必要的真实 DOM 更新，界面没问题，但是效率差
- 如果结构中还包含输入类的 DOM,会产生错误 DOM 更新，界面就会有问题

3. 开发中如何选用 key 呢？

- 开发中一般会使用 id 等唯一标识作为 key
- 如果是简单的展示数据，用 index 也是可以的

## React 脚手架

项目创建并启用

```zsh
npx create-react-app my-app && cd $_
npm start
```

React 脚手架创建的项目目录结构

```markdown
- public 静态资源文件夹
  - favicon.ico 网站页签图标
  - index.html 主页面
  - logo.png logo 图
  - manifest.json 应用加壳配置文件
  - robits.txt 爬虫协议文件
- src 源码文件夹
  - components 公共组件文件夹
  - App.css App 组件的样式
  - App.js App 组件
  - index.js 主入口文件
  - logo.svg logo 图
  - reportWebVitals.js 页面性能分析文件
  - setuoTests.js 组件单元测试文件（需要 jest-dom 库的支持）
```

功能界面的组件化编码流程

1. 拆分组件
2. 实现静态组件
3. 动态化组件

- 动态显示初始化数据
  - 数据类型
  - 数据名称
  - 数据保存在哪个组件中
- 交互

TodoList 的部分代码

1. 更新时可以利用赋值结构后再传入重复字段会自动覆盖的方式进行更新数据
2. 数据批量删除可以用 filter 过滤实现

```js
// 更新一个todo对象
updateTodo = (id, done) => {
  // 获取状态中的todos
  const { todos } = this.state;
  // 匹配处理数据
  const newTodos = todos.map((todo) => {
    if (todo.id === id) return { ...todo, done };
    else return todo;
  });
};
// 删除一个todo对象
deleteTodo = (id) => {
  // 获取状态的todos
  const { todos } = this.state;
  const newTodos = todos.filter((todo) => {
    return todo.id !== id;
  });
  this.setState({ todo: newTodos });
};
// 用于全选
checkAllTodo = (done) => {
  // 获取状态中的todos
  const { todos } = this.state;
  const newTodos = todos.map((todo) => {
    return { ...todo, done };
  });
  // 更新状态
  this.setState({ todo: newTodos });
};
```

## React ajax

### 简单代理

在 package.json 中追加配置`"proxy":"http://localhost:4000"`

- 优点：配置简单，前端请求资源可以不加任何前缀
- 缺点：不能配置多个代理

### 中间件代理

在 src 下创建配置中间件 `src/setupProxy.js`

- 优点：可以配置多个代理，可以灵活的控制请求是否走代理
- 缺点：配置相对繁琐，前端请求资源必须加前缀

```js
const proxy = require("http-proxy-middleware");
module.exports = function (app) {
  proxy("api1", {
    target: "http://localhost:4000",
    changeOrigin: true, // 控制服务器接收到的请求头中host字段的值
    pathRewrite: {
      "^api1": "",
    },
  });
};
```

### fetch 请求

> 关注分离的设计思路，是原生的

与传统的 ajax 请求相比，有如下特点：

- fetch 是原生的函数，不再借助 XMLHttpRequest 对象
- 使用 fetch 无法取消一个请求，原因是 fetch 是基于 Promise,而 Promise 无法做到这一点。由于 fetch 是典型的异步场景，所以大部分问题不是 fetch,而是 Promise 的。
- 如果直接使用 fetch,返回的并不是直接的结果，它只是一个 HTTP 响应，想要获取数据，可以使用 async+await 获取，或者使用 Promise 链式调用，在第一个 then 中将其返回，在下个 then 中使用

```js
fetch(`/api1/search/user?q=${keyword}`).then(
  response=>{
    console.log('联系服务器成功')
    return response.json()
  },
  error=>{
    console.log('联系服务器失败'),
    return new Promise(()=>{})
  }
).then(
  response=>{
    console.log('获取数据成功',response)
  },
  error=>{
    console.log('获取数据失败',error)
  }
)

```

使用 async+await 优化后

```js
try {
  const response= await fetch(`/api1/search/users?q=${keyWord}`)
  const data = await response.json()
  console.log(data);
  } catch (error) {
  onsole.log('请求出错',error);
  }
}
```

## React 路由

> 单页面应用必用技术

路由就是一个映射关系，key 为路径，value 可能是 function,也可能是 component

### 路由的分类

1. 后端路由

- 理解：value 是 function,用来处理客户端提交的请求
- 注册路由：`router.get(path,function(req,res))`
- 工作过程：当 node 接收到一个请求时，根据请求路径找到匹配的路由，调用路由中的函数来处理请求，返回响应数据

2. 前端路由

- 浏览器前端路由，value 是 component,用于展示内容
- 注册路由：`<Route path="/login" component={Login}>`
- 工作过程：当浏览器 path 变为`/login`时，当前路由组件就会变为 Login 组件

### react-router-dom

就是 react 的一个插件库，专门用来实现单页面应用 SPA

API：

1. `<BrowserRouter>`,`<HashRouter>`,`<Route>`,`<Redirect>`,`<Link>`,`<NavLink>`,`<Switch>`
2. history 对象、match 对象、withRouter 函数

### 路由的基本使用

1. 明确好界面中的导航去、展示区
2. 导航区的 a 标签改为 Link 标签，to 属性指定跳转路径
3. 展示区写Route标签进行路径的匹配，path属性指定路径，component指定需要渲染的组件
4. 最外层包裹一个`<BrowserRouter>`或`<HashRouter>`

### 路由组件与一般组件

不同之处：
1. 写法不同
2. 存放位置不同
3. 接收到的props不同
```markdown
//路由属性打印结果展示
history:
	go: ƒ go(n)
	goBack: ƒ goBack()
	goForward: ƒ goForward()
	push: ƒ push(path, state)
	replace: ƒ replace(path, state)
location:
    pathname: "/about"
	search: ""
	state: undefined
	match:
params: { }
	path: "/about"
	url: "/about"
```
