# 区别

 - **react-router**：浏览器和原生应用的通用部分。
 - **react-router-dom**：用于浏览器。
 - **react-router-native**：用于原生应用。

# 简述

 - **BrowserRouter**：这是对Router接口的实现。使得页面和浏览器的history保持一致。如：`window.location`。

 - **HashRouter**：和上面的一样，只是使用的是url的hash部分，比如：`window.location.hash`。

 - **MemoryRouter**：将url的历史记录保存在内存中（不读取或写入地址栏），在测试和非浏览器环境（如React Native）中很有用

 - **NativeRouter**：处理react native内的路由。

 - **StaticRouter**：处理静态路由，和v3一样。

 > **BrowserRouter**与**HashRouter**的选择：这两个是可以在浏览器中使用的。如果使用的是一个非静态的站点、要处理各种不同的url那么就需要使用BrowserRouter。相反的如果server只处理静态的url，那么就使用HashRouter。

# 组件

## Route

**属性：**

 - **path**：字符串类型，它的值就是用来匹配url的。
 - **component**：它的值是一个组件。在path匹配成功之后会绘制这个组件。
 - **exact**：这个属性用来指明这个路由是不是排他的匹配。
 - **strict**： 这个属性指明路径只匹配以斜线结尾的路径。

# 实例

## 安装

`npm install react-router-dom --save-dev`

## 基本操作

建立`hohome.js`和`detail.js`

> home.js

```js
import React from "react";

export default class Home extends React.Component {
  render() {
    return (
      <div>
        <a href="#/detail">去detail</a>
      </div>
    );
  }
}
```

> detail.js

```js
import React from "react";

export default class Detail extends React.Component {
  render() {
    return (
      <div>
        <a href="#/">回到home</a>
      </div>
    );
  }
}
```

建立一个路由组件`Router.js`

```js
import React from "react";
import { HashRouter, Route, Switch } from "react-router-dom";
import Home from "../container/home";
import Detail from "../container/detail";

const BasicRoute = () => (
  <HashRouter>
    <Switch>
      <Route exact path="/" component={Home} />
      <Route exact path="/detail" component={Detail} />
    </Switch>
  </HashRouter>
);

export default BasicRoute;
```

编写入口文件`indexjs`

```js
import React from "react";
import ReactDOM from "react-dom";
import Router from "./container/Router";

ReactDOM.render(<Router />, document.getElementById("root"));
```

其中的跳转是通过标签实现的。

## 通过函数跳转

修改`Router.js`中的代码

```js
...
import { HashRouter, Route, Switch, BrowserRouter } from "react-router-dom";
...
<HashRouter history={BrowserRouter}>
...
```

修改`home.js`中的代码

```js
import React from "react";

export default class Home extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      <div>
        <a href="#/detail">去detail</a>
        <br />
        <button onClick={() => this.props.history.push("detail")}>
          通过函数跳转
        </button>
      </div>
    );
  }
}
```

`onClick()`事件通过`this.props.history.push`这个函数跳转到detail页面。在路由组件中加入的代码就是将history这个对象注册到组件的props中去，然后就可以在子组件中通过props调用history的push方法跳转页面。

## URL传参

修改`Router.js`

```js
...
<Route exact path="/detail/:id" component={Detail}/>
...
```

修改`detail.js`，使用`this.props.match.params`接收URL传过来的参数

```js
...
componentDidMount() {
    console.log(this.props.match.params);
}
...
```

在地址栏输入`http://localhost:3000/#/detail/1`，打开控制台。

## 隐式传参

修改`home.js`

```js
import React from "react";

export default class Home extends React.Component {
  constructor(props) {
    super(props);
  }

  render() {
    return (
      <div>
        <a href="#/detail/1">去detail</a>
        <br />
        <button onClick={() => this.props.history.push("detail")}>
          通过函数跳转
        </button>
        <button
          onClick={() =>
            this.props.history.push({
              pathname: "/detail",
              state: {
                id: 3
              }
            })
          }
        >
          通过函数隐式传参跳转
        </button>
      </div>
    );
  }
}
```

修改`detail.js`

```js
componentDidMount() {
    //console.log(this.props.match.params);
    console.log(this.props.history.location.state);
}
```

## 其他函数

**replace：**

有些场景下，重复使用push或a标签跳转会产生死循环，为了避免这种情况出现，react-router-dom提供了replace。在可能会出现死循环的地方使用replace来跳转：

`this.props.history.replace('/detail');`

**goBack：**

场景中需要返回上级页面的时候使用：

`this.props.history.goBack();`