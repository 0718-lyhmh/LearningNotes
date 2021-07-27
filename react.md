## 一、核心概念

### 1、组件&Props

组件无论是使用函数声明还是通过class声明，，都决不能修改自身的props。rreact所有组件都必须像纯函数一样保护它们的props不被更改。

纯函数：函数不会尝试更改入参，且多次调用相同的入参始终返回相同的结果

### 2、state&生命周期

class组件应该始终使用props参数来调用父类的构造函数------super（props）

挂载：componentDidMount

卸载：componentWillUnmount

react不推荐直接修改state中的数据，而是通过this.setState()方法来更新state中的数据。构造函数是唯一可以给this.state赋值的地方

state的更新可能是异步的。出于性能，react可能会将多个setState()调用合并成一个调用。因为this.props和this.state可能会异步更新，所以不能依赖他们的值来更新下一个状态。为解决这个问题，可以让setState接受一个函数而不是一个对象。state作为第一个参数，props作为第二个参数

```react
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});

// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```

数据向下流动（“自上而下”或“单向”数据流）：任何的state总是属于特定的组件，而且从该state派生的任何数据或UI只能影响书中“低于”它们的组件

### 3、事件处理

绑定this的三种方法：public class fields（推荐，通过将一个箭头函数赋给一个变量）、箭头函数（在调用时使用箭头函数，事件对象e作为第二个参数）、Function.prototype.bind绑定this（事件对象e被隐式传递）。

### 4、条件渲染

if-else   、  && 、    三目运算符

### 5、表单

受控组件：表单元素(如input、textarea、select)，通常自己维护state并根据用户输入进行更新。但是可变状态state只能通过setState来更新，把两者结合起来，使React的state成为“唯一数据源”。渲染表单的react组件还控制着用户输入过程中表单发生的操作。被react以这种方式控制取值的表单输入元素就叫做“受控组件”。相当于vue的v-model双向绑定（定义两个事件，一个监听输入数据去更新state，另一个对state进行相应的操作）。

多个受控组件，通过event.target.name去辨别

### 6、状态提升

在多个组件中需要共享的state向上移动到 它们的最近共同父组件中，父组件通过props传递给子组件，便可实现共享state。这就是所谓的“状态提升”。

### 7、组合VS继承

包含关系：prop的children属性、自定义（类似插槽）

特例关系：

## 二、高级指引

### 1、高阶组件



