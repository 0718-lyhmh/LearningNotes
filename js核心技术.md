### 一、垃圾收集

- 标记清除：利用标记清楚算法找到环境变量中已经无法访问到的变量，垃圾收集器会销毁并回到它们所占用的空间。全局变量的自动释放空间很难判断，所以尽量少用全局变量，当数据不再有用，可将其值设为null解除引用
- 引用计数：当值的引用计数为0时就将内存回收

### 二、执行上下文

#### 1、概念

javascript执行代码时，会创建一个执行环境，这个环境被称为执行上下文。以下三种情况都会创建执行上下文放入执行栈中

- 全局环境
- 函数环境（只有函数执行时才创建，声明不会）
- eval环境

#### 2、执行上下文的生命周期

- 创建阶段

  - 创建变量对象（VO）

    - 确定函数参数arguments（全局环境没有这个变量）并赋值

    - 获取所有函数声明。属性值为引用地址（函数提升）

    - 获取所有var变量声明，属性值为undefined（变量提升）

      注意：函数提升比变量提升优先级高，所以同名时函数提升会覆盖函数提升和变量提升，但是可以重新赋值

  - 确定作用域链：作用域链的用途是保证当前执行环境有权访问的所有变量和函数的有序访问。作用域链的最前端始终是当前环境的对象变量，下一个对象变量就是下一个包含环境，一直延续到全局执行环境。

  - 确定this指向

- 执行阶段

  - 完成变量赋值（当前处于激活的执行上下文的变量对象称为活动对象AO，包含所有AO变量对象的属性并且已赋值，还包含this指向）
  - 函数引用
  - 执行其他代码

### 三、引用类型

所有对象都有以下三种方法：toLocaleString（）、toString（）、valueOf（）

#### 1、Array类型

```javascript
//检测数组
value instanceof Array
Array.isArray(value)//es5
```

转换方法：join（分隔符）

栈方法：push（）、pop（）

队列方法：shift（）和push、 unshift（）和pop（）

重排序方法：reverse（）、sort（）

```javascript
//sort方法内部是调用toString将数组转换成字符载进行比较大小，所以对数值的比较会出错,对数值比较可采用一下方法
var array = [4,6,3,6,3,6]
array.sort(function(arr1,arr2){
    return arr2 - arr1  //升序，降序则返回arr1 - arr2
})

```

操作方法：concat（）、slice（）使用这两种方法可以复制一个新数组，但只是浅复制、splice（位置，删除个数，添加项）

位置方法：indexOf（）、lastIndexOf（）

```javascript
//迭代方法：
var arr = [4,3,5,6]
arr.name(function(item,index,array){
    //name是every：对每一项item执行函数全部为true，则返回true，否则返回false
    //name是some：对每一项执行函数若有一项为true则返回true
    
    //name是filter，返回一个数组，对每一项执行函数时如果为true，则将这个item添加到数组中
    //name是map，返回和原数组同长度的数组，对每一项执行函数后的返回值添加到数组中
})

//缩小方法
arr.redude(function(pre,cur,index,array){
    //迭代从第二项开始，cur会作为下一次的pre，可用此函数计算数组的累加
})
```

### 四、函数表达式

#### 1、递归

arguments.callee是一个指向正在执行的函数的指针，可以用它来实现对函数的递归

```javascript
function factorail(num){
    if(num<1){
        return 1
    }
    else{
        return num * factorail(num-1)
        //return num * arguments.callee(num-1)
    }
}
var anotherFactorail = factorail
factorail = null
alert(anotherFactorail(4))  
//出错，因为调用annotherFactorail函数时，里面的factorail已不再是函数可使用

```

#### 2、闭包

闭包是指有权访问另一个函数作用域变量的函数。创建闭包常见的方式就是在一个函数内部创建另一个函数

- 闭包与作用域：无论什么时候在函数中访问一个变量时，就会从作用域中搜索具有相应名字的变量。一般来说，当函数执行完毕后，局部活动变量就会被销毁，内存中仅保存全局作用域。但是闭包不同，当函数返回一个闭包时，闭包的作用域引用着外部函数的活动对象，在外部环境执行完毕后，其作用域会被销毁，但是活动对象仍然存在内存中，知道闭包函数被销毁才会被释放
- 闭包与变量：闭包只能取得包含函数中任何变量的最后一个值，闭包中保存的是整个变量对象，而不是某个特殊值
- 闭包的this：在全局环境中，this是指window。而函数被某个对象调用时，this是那个对象。而闭包的this指向的是全局window（不适用call或apply的条件下）。因为每个函数每调用时，活动对象都会自动创建this和arguments两个特殊变量。内部函数在搜索这两个变量中，只会搜索到其活动变量为止，因此不可能直接访问到外部函数的这两个变量。如若想要访问，就要把这两个变量保存到闭包函数能够访问到的变量中。
- 容易导致内存泄漏
- 创建私有变量和特权方法，在闭包中可以创建公共方法去访问他们
- 单例对象（只有一个实例的对象）的模块模式和增强的模块模式

### 五、BOM相关

#### 1、window

BOM的核心对象是window，它具有双重角色，既是javascript访问浏览器窗口的一个接口，又是ECMAScript规定的一个Global对象。

- 全局作用域：在全局作用域中定义的变量和方法都成为window对象的属性和方法。全局变量不能通过delete操作符删除，而直接在window对象上定义的属性和方法可以。location、navigator等都是window的属性

- 窗口位置（大多浏览器支持window.screenLeft，FireFox是window.screenX）

  ```javascript
  var leftPos = (typeof window.screenLeft == 'number')? window.screenLeft : window.screenX;
  var topPos = (typeof window.screenTop == 'number')? window..screenTop : window.screenY
  
  //可通过moveTo（）、moveBy（）移动窗口位置
  ```

- 窗口大小：innerWidth,innerHeight,outerWidth,outerHeight，document.documentElement.clientWidth，document.documentElement.clientHeight,可使用resizeTo和resizeBy调整浏览器窗口的大小

间歇调用和超时调用：setTimeout，clearTimeout，setInterval，clearInterval。尽量避免使用间歇调用，因为后一个间歇调用有可能会在前一个间歇调用结束之前启动，使用超时调用模拟间歇调用可以避免这个问题，这两个调用中的回调函数this指向window，除非使用箭头函数

系统对话框：alert（提示）//警告框，confirm（提示）//确认框，prompt（提示，输入内容）//提示框

#### 2、location

既是window对象，也是document对象，所以window.location和document.location引用的是同一个对象。属性有hash、host、hostname、href、pathname、port、protocol、search。除了hash，修改其他属性页面都会以新的URL重新加载，并生成一条历史记录，使用replace不会生成记录，即不能回退。

```javascript
//使用assign方法立即打开新的URL并且生成记录
location.assign("http://www.baidu.com")
//使用一下两种方法设置URL也是调用的assign方法
window.location = "http://www.baidu.com"
location.href = "http://www.baidu.com" //最常用
```

#### 3、navigator

常用于检测显示网页的浏览器类型。可以检测插件plugins、用户代理userAgent等

#### 4、screen

常用来获取屏幕可用宽度（screen.availWidth）、高度（screen.availHeight）

#### 5、history

保存用户上网的历史记录，history.go（），参数为正数表示前进，负数表示后退，字符串地址表示跳转到历史记录中最近的位置。也可使用back和forward后退或前进一步。

### 六、DOM相关

