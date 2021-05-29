# 一、基础知识

### 1、computed、methods、watch的区别

computed是计算属性，调用时不用加（），methods定义的是方法，调用时需要加上（）；computed具有缓存机制，只有依赖的data发生变化时才会重新调用get方法执行，而methods是每次进入页面都要执行。

computed依赖data中的属性，所以属性名不能和data中相同；而watch是侦听器，侦听data或是computed某个属性，函数有newVal和oldVal两个参数；两者都是放数据发生变化时才重新执行，但是watch是当数据变化时需要进行异步操作或是开销较大的操作时调用。

### 2、比较三种方法，理解let区块作用域

```javascript
for(var i=0 ;i<5; i++)
{
    btn[i].addEventLinstener('click',function(){
        console.log(i)
    })
} //全部打印4
```

```javascript
for(var i=0; i<5; i++)
{
    (btn[i].addEventListenser('click',function){
    console.log(i)
    })(i)
}//正确打印0,1,2,3,4
```

```javascript
for(let i=0; i<5 ;i++)
{
	btn[i].addEventLinstener('click',function(){
        console.log(i)
    })
}//正确打印
```

### 3、v-on事件监听参数问题

（1）方法：handle(value1)

​          调用：@click="handle()"   //value1为undefined

​                       @click="handle" //value1为MouseEvent事件对象

（2）方法：handle(value1,value2)

​          调用：@click="handle('aaa' , $event)" //value2为MouseEvent事件对象

### 4、input复用问题

互斥组件，底层渲染时如果组件相同会进行复用，导致input中切换是value不能及时更改。给input加上不同的key，只有key相同才会复用。

### 5、v-show与v-if的区别

v-if是通过是否操作dom结点进行是否显示，v-show则是通过添加css样式display：none | | block进行是否显示。v-if具有更高的切换消耗，v-show有更高的初始性能消耗，所以在进行频繁切换操作时使用v-show更多，运行条件变化少时使用v-if更好。

### 6、v-for中key的作用

为了提高性能，在组件中绑定一个与数据唯一对应的值，使得底层进行diff算法是能找到能快找到需要更新的组件。如果不绑定唯一对应的key，vue底层就不是对dom元素进行移动来匹配数据项，而是通过简单的就地复用，使得性能降低

### 7、为什么组件中data需要返回的是函数

因为组件就是被拿来复用的，组件中的数据不能相互影响，应该做到每复用一次，data数据就被复制一次。以函数的形式返回就是返回一份新的data数据，这样就给每个组件的data一个私有数据空间。如果以对象的形式返回，组件共享data，会造成一个变了全部都会变的后果。

### 8、组件间通信方式有几种？

父传子 props

子传父 $emit(子)      v-on:(父)

父获取子实例  $children 、$refs

子获取父实例 $parent

获取根实例 $root

Vue.prototype.$bus = new Vue()   // event Bus 用于无关系组件间的通信，this.$bus.$emit发送事件   this.$bus.$on监听事件

### 9、v-model修饰符有哪些？

.lazy  :  只有输入框失去焦点或回车时才绑定

.number :  输入数据为number类型，而不是默认的string类型

.trim ： 剥除输入框的空格



# 二、webpack

### 1、模块化

模块化解决问题：命名冲突、文件依赖

commonJS：（1）同步加载（2）可多次加载，但只会在第一次加载时运行，即运行结果会缓存

适用：服务器、node.js

```javascript
//导出
module.exports = {a} 或 exports.a = "dnbch"
//导入
var moduleA = require("./aaa.js")
```

ES6模块化：（1）按需加载（2）import和export命令只能在模块的顶层，不能在代码块之中（如：if语句中）,import()语句可以在代码块中实现异步动态按需动态加载

```javascript
//方法一
export var a = 1;
import {a} from './aaa.js'
//方法二
export function Person(){
}
import {Person} from './aaa.js'
//方法三
export {a,b}
import {a,b} from './aaa.js'
//方法4（每个模块只能有一个default）
export default function(){}
import aaa from './aaa.js' //aaa为自定义名字，默认导入的是文件中export default的内容
//方法五
export {a,b,c,d,e,f,g,h}
import * as all from '.aaa.js' //使用时 all.a , all.b
```

### 2、loader与plugin有什么不同

**loader** : loader是一个加载器，webpack原生只能加载js文件，loader使得webpack具有加载非js文件的能力，例如css，less等等。loader在webpack配置的module.rules中配置，类型为数组。常用的loader有css-loader（加载css），style-loader（把css注入js，通过dom操作结点），babel-loader（es6转es5），file-loader（把文件输出到文件夹），url-loader（文件较小时可以将文件内容转成base64注入代码）

**plugin**：plugin是一个插件，扩展webpack的功能。在webpack配置中的Plugins中配置，类型是数组，每一项都是plugin实例，参数通过构造函数传入。常用plugin有HtmlWebpackPlugin（打包html文件）、UglifyjsWebpackPlugin（将js代码压缩）、HotModuleReplacementPlugin（热加载）

### 3、bundle、chunk，module分别是什么？

bundle是webpack打包出来的文件；

chunk是指webpack在进行模块的依赖分析时，分割出来的代码块

module是开发时的单个模块

### 4、webpack与grunt、gulp有什么不同

webpack是基于入口文件的构建工具，会自动递归地解析入口文件所需要加载的所有资源文件，利用loader处理不同文件，利用plugin扩展webpack功能。

grunt和gulp是基于任务的流的，找到一个文件，对其进行一系列链式操作，更新流上的数据，整条链式操作构成一个任务，多个任务构成整个web构建流程。

### 5、什么是模块热更新

模块热更新是webpack的一个功能，在修改代码后可以不用刷新浏览器就可以自动更新，是高级版的自动更新浏览器

# 三、vue-cli

### 1、el、template、render的联系与区别

el中提供的是一个dom对象作为vue实例的对象，如果new一个vue时有el对象就会立即进入编译模式，否则需要显示调用 vue.$mout()手动开启编译

template是一个模板，如果存在el，模板内容将会替换掉el的dom元素。template存在的好处可以使开发时较少改动index.html,只需要从模板中插入内容

render函数的优先级最高，存在render时，vue不会从template或el中提取模板渲染

### 2、runtime-compiler 和 runtime-only的区别

runtime-compiler的vue运行过程：

template  =》ast =》render =》virtual DOM =》UI

（模板 ）解析成 （抽象语法树）编译成 （render函数）形成 （虚拟DOM） 转成（真实UI）

runtime-only的vue运行过程：

render =》 virtual Dom =》UI

render函数中返回虚拟dom是使用的方法叫做createElement（标签，属性，内容）参数也可接受一个template对象。

对比：runtime-only的性能更高、代码量更少（因为从template到render这个过程需要依赖插件vue-template-compiler去编译）

# 四、vueRouter（https://router.vuejs.org/）

### 1、什么是前端渲染，什么是后端渲染

后端渲染阶段：服务器里存储HTML+css+java代码，当用户在浏览器中输入url时，服务器拿到url然后找到对应的html+css+java代码渲染出页面，传回浏览器时已经是渲染好的页面。而在服务器中管理页面与url之间的映射关系的就是后端路由

前后端分离阶段：在此阶段，有一个静态服务器中存储着多个html+css+js代码，当用户在浏览器中输入url时，会从静态服务器中读取相应的代码到服务器，服务器执行js代码用到ajax请求时，才会根据请求的地址从服务器中读取数据。

SPA：单页面富应用。就是在前后端分离阶段加上了前端路由。在前后端分离阶段是静态服务器中存储的是多个页面的html+css+js代码，但是SPA阶段静态资源服务器中存储的只有一个html+css+js，请求是浏览器会获取所有的代码，在用户输入url时，通过前端路由匹配该url请求的页面按需加载html+css+js代码。所以前端路由就是在前端管理url与页面之前映射关系的。

### 2、vue-router中hash模式和history模式的区别

hash模式中url会出现#，#内容就是hash散列，不会发送到服务器，即页面不会发生刷新。当地址栏的散列值发生变化，会触发window.location.hash变化，从而触发hashchange事件

history模式中url没有#，window.history指向History对象，地址是通过栈存储。方法主要有history.pushState() ,history.replaceState(), history.back(), history.go(), history.forward()

vue-router'中默认使用hash模式，需要通过加上mode:"history"来改成history模式

history有个缺点就是在当前地址中强制刷新页面时，如果服务器中没有相应准备会出现404的情况。为避免这种情况，服务器端应该为无法匹配的路径返回index.html那个唯一静态页面资源

配置404页面

```javascript
//此配置一定要放在最后
{
    path:'*',
    component : ()=>import('组件')
}
```

### 3、路由懒加载

在vue的单页面富应用中使用webpack打包时，会把所有js文件打包在一起，使得js包变得非常大，首次加载时全部下载，会影响性能。路由懒加载就是能把不同路由对应组件分割成不同的代码块，实现按需加载组件，访问性能更高效

```javascript
const routes = {
	path:'/home',
	component:()=>import(../componengt/Home)
}
```

### 4、active-class是哪个组件的属性

active-class是router-link中的属性，只有处于活跃时才会有的属性，可通过配置路由时的linkActiveClass进行更改默认名字

### 5、vue-router中的组件有哪些

router-link：使用to属性跳转到对应路径

router-view: 占位符，表示路径跳转对应的组件放在什么位置

keep-alive: 使用组件保持活跃，不会重复created和destroyed。具有属性include和exclude，利用组件实例中的name名字选择哪些组件需要保持活跃。使用keep-alive组件时具有两个函数actived和disactived

### 6、路由传参params和query

params可作为定义动态路由是取参数。动态路由就是path属性上加上/:id，

```javascript
//params
this.$router.push({
    name : '/home',  //params只能使用name
    params:{         //params类似ajax的post传参，不会出现在url中
        number : 1   //页面强制刷新数据不会存在
    })                //接受参数时：this.$route.params.number
}

//query
this.$router.push({
    path:'/home',   //query可只用name也可使用path
    query:{         //query类似ajax的get传参，会出现在url中
        number : 1  //页面强制刷新数据也会存在
    })              //this.$route.query.number
}
```

### 7、this.$router与this.$route的区别

this.$router是一个VueRouter对象，是一个全局路由对象，在配置好路由映射之后，可在页面中使用其push（）、replace（），go（）等方法、

this.$route表示当前正在跳转的路由器对象，可调用其path（），params（），query（）等方法

### 8、导航守卫

全局导航守卫（beforeEach通常用来验证登录、beforeResolve、beforeAfter）

路由独享守卫（beforEnter）、组件内守卫（beforeRouterEnter、beforeRouterUpdate、beforeRouterLeave通常用来未保存修改就离开）

beforeRouterEnter不能访问this，因为在导航确定之前，组件还未创建。befroe前的钩子必须调用next（）函数进入下一个钩子

### 9、如何响应路由参数的变化

当使用路由参数，如从user/foo到use/bar时，由于都是渲染同一个组件，所以组件会进行复用，这意味着组件的生命周期钩子函数不会再被调用

```javascript
//watch监听方式
watch:{
    $route(to,from){
        if(to != from)
            console.log("做出处理")
    }
}
//路由导航方式
beforeRouteUpdate(to,from,next){
    //做出处理
}
```

### 10、跳转新路由，如何滚动到固定位置

```javascript
scrollBehavior(to,from,savedPosition){
    if(savedPosition){
        return savedPosition
    }
    else{
        return {x:0,y:0}
    }
}
```

# 五、promise

### 1、event loop

javascript执行机制：

（1）javascript是单线程，所有同步任务都在主线程上执行，形成一个“执行栈”

（2）执行过程中遇到异步任务，就把异步任务放置“任务队列”

（3）当主线程执行栈为空，就会查找“任务队列”中的执行执行事件。对应的异步任务结束等待状态，添加到执行栈中执行

（4）主线程不断重复这三个步骤

主线程从"任务队列"中读取事件，这个过程是循环不断的，所以整个的这种运行机制又称为Event Loop（事件循环）

Event loop执行顺序：

（1）一开始整个脚本作为一个宏任务执行

（2）同步代码直接执行，宏任务放入宏任务列表，微任务放入微任务列表

（3）当前宏任务执行完出队，检查微任务列表，依次执行，直到执行完毕

而异步任务分为宏任务和微任务：promise是微任务，settimeout是宏任务

### 2、什么是promise

promise是异步编程的一种解决方案。传统的异步ajax请求，如果在异步请求时再有请求，如此链式异步会造成“回调地狱”，回调地狱代码臃肿，可读性差不容易维护。未解决这一问题出现了promise这种优雅的异步编程。使用resolve方法成功请求，reject方法失败请求。然后再then中执行请求成功的回调，在catch中执行失败的回调。而在后续为了解决.then的链式问题，有提出了async function。使用async和await能够解决.then链式的问题。promise在事件循环中是微任务的概念。

promise存在三个状态：pending（等待）、fulfilled（执行）、rejected（拒绝）

async函数返回的是一个promise，await只能在async函数中使用，表示暂停代码执行，等待返回结果。可使用try、catch捕获错误，也可使用.catch捕获

### 3、promise的all和race

- promise.all接受一组异步操作，并且会等到左右异步操作执行完成才会进入.then函数回调中，.then中的结果数组顺序与promise.all接受的异步数组顺序相同（使用场景：请求不同来源图片再一同渲染）
- promise.race也是野兽一组异步操作，但是第一个先执行完的异步操作拿到结果后执行进入.then回调，所以.then中接受不到后面的完成的异步操作，但不影响它们的执行。（使用场景：请求超时时另外处理）
- 两者中如果有错误抛出，.catch只能接受到第一个抛出的错误

# 六、vuex（https://vuex.vuejs.org/）

### 1、对vuex的理解

- vuex是为vue.js开发的一种状态管理模式。
- 对于多组件依赖同一个状态或不同视图需要改变同一变量的时候使用。
- vuex的存储状态都是响应式的，vuex有五大核心概念：state（数据存储的地方），getters（对数据进行计算，类似组件的computed属性），mutations（同步操作且是更改state数据的唯一途径）、actions（异步操作），modules（模块划分）。

### 2、mutations和actions的区别

- mutations是进行同步操作的地方，actions是进行异步操作的地方，如果在mutations中进行异步操作，devtool调试工具检测不到数据的变化
- mutations是变更state数据的唯一途径，actions进行异步操作之后，，还需使用commit提交mutations中的方法
- mutations中函数第一个参数是state，第二个参数接收组件传递的参数。actions函数第一个参数时context，context李包含state，commit等，第二个参数也是接收组件传递 参数

### 3、如何在模块中访问全局内容

- 访问全局state和getters时，rootSate和rootGetters会作为第三和第四个参数传给getters，也会通过context对象的属性传入actions
- 访问全局mutations和actions时，只需要分发或提交的时候，将{root:true}作为第三个参数传给dispatch或commit即可

### 4、辅助函数（组件调用多个状态或方法时）

```javascript
//组件中
computed:{
    //mapState函数返回的是一个对象，利用对象的解构可以将状态放入computed中,mapState中映射的是state中和getters中的变量
    ...mapState({
        num : 'number'  //映射this.number = this.$router.state.number
    })
    
    //mapGetters映射的是只有getters中的变量
    ...mapGetters({
        num : 'number'  
    })
}

methods:{
    ...mapMututations({
        add : "increment"
        //将this.add映射this.$store.commit('increment')
    })
    ...mapActions({
        add : "increment"
        //将this.add映射this.$store.dispatch('increment')
    })
}
```

# 七、项目

### 1、better-scroll可滚动区域的bug

- scrollerHeight属性是根据放在better-scroll中content的子组件的高度

- 但是在首页中，刚开始计算scrollerHeight属性时，只有item的高度，没有图片的高度

- 所以计算出来的可滚动高度是错误的，这就导致项目中上拉只能上拉一小部分

  解决方法：监听每一张图片是否加载完成，只要一张图片加载完成，就执行一次refresh()







