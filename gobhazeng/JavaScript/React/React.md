# 组件

## 生命周期

**初始化阶段：**

 - getDefaultProps：只调用一次，实例之间**共享引用**
 - getInitialState：初始化每个实例的**状态**
 - componentWillMount：render之前最后一次**修改状态**的机会
 - render：只能访问`this.props`和`this.state`，只有一个顶层组件，**不允许**修改状态和DOM输出
 - componentDidMount：成功render并渲染完成真实DOM之后触发，**可以修改**DOM

**运行中阶段：**

 - componentWillReceiveProps：父组件修改属性触发，**可以**修改新属性、修改状态
 - shouldComponentUpdate：返回false会**阻止**render调用
 - componentWillUpdate：**不能**修改属性和状态
 - render：只能访问`this.props`和`this.state`，只有一个顶层组件，**不允许**修改状态和DOM输出
 - componentDidUpdate：**可以**修改DOM

**销毁阶段：**

 - componentWillUnmount：在删除组件之前进行清理操作，比如计时器和事件监听器

## props和state

### props（属性）

组件的属性是只读的，不可以改变。

组件的props属性的值有类型限制。

组件中可以定义默认的属性值

### state（状态）

**状态的含义：**

 - state：在组件中可以改变

**状态的用法：**

 - getInitialState:初始化每个实例的特有状态
 - setState：更新组件状态

# 语法

## dangerouslySetInnerHTML

用于在React中渲染Html，需要使用`dangerouslySetInnerHTML={{ __html: htmlString}}`

如：

```js
<div
  dangerouslySetInnerHTML={{
    __html: htmlString
  }}
/>
```