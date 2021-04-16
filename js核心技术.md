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

### 四、面向对象的程序设计

#### 1、属性类型

数据属性

- [[Configurable]] : 能否通过delete删除属性从而重新定义，默认为true
- [[Enumerable]] : 能否枚举（即能否通过for-in返回属性），默认为true
- [[Writable]] : 能否修改属性，默认为true
- [[Value]] : 保存属性的值，默认为undefined

访问器属性

- [[Configurable]] : 能否通过delete删除属性从而重新定义，默认为true
- [[Enumerable]] : 能否枚举（即能否通过for-in返回属性），默认为true
- [[Get]] : 读取属性时调用，默认为undefined
- [[Set]] : 写入属性时调用，默认为undefined

修改数据属性默认值或是定义访问器属性只能通过Object.defineProperty()

```javascript
var person = {}
//调用这个方法，如果不指定configurable，enumerable，writable，则默认都会为false
Object.defineProperty(person,"name",{
    writable : false,
    value : 'Hebe'
})

var boob = {
    _year = 2004,
    edition = 1
}
Object.defineProperty(book,'year',{
    get:function(){
        return this._year
    },
    set:function(newValue){
        if(newValue>20004){
            this._year = newValue;
            this.edition +=newValue - 2004
        }
    }
})
book.year = 2005;
alert(book.edition) //2

//定义多个属性可以使用Object.defineProperties(),读取属性特性使用Object.getOwnPropertyDescriptor()

```

#### 2、创建对象

传统Object构造函数和字面量模式都可创建对象，但是创建多个对象时会大量重复代码

- 工厂模式（能创建多个相似对象，却无法识别对象（不知道一个对象的类型））

  ```javascript
  function createPerson(name,age,job){
      var obj = new Object();
      obj.name = name;
      obj.age = age;
      obj.job = job;
      o.sayName = function(){
          alert(obj.name)
      }
      return obj
  }
  var person1 = createPerson('Hebe',19,'software')
  var person2 = createPerson('Alice',20,'hardware')
  ```

- 构造函数模式(当有多个方法时，需要定义多个全局方法引用，封装性差)

  使用new操作符创建对象。实际会经历以下4个步骤

  - 创建一个新对象
  - 将构造函数的作用域赋给新对象（即this指向这个新对象）
  - 执行构造函数的代码（为新对象添加属性）
  - 返回新对象

  ```javascript
  function Person(name,age,job){
      this.name = name;
      this.age = age;
      this.job = job;
     /* this.sayName = function(){
          alert(this.name)
      }*/
      this.sayName = sayName
  }
  function sayName(){
      alert(this.name)
  }
  var person1 = new Person('Hebe',19,'software')
  var person2 = new Person('Alice',20,'hardware')
  ```

- 原型模式(引用类型变量（如数组）会共享)

  使用hasOwnProperty()和in可判断属性存在对象上还是存在原型中

  ```javascript
  function hasPrototypeProperty(object,name){
      return !object.hasOwnProperty(name)&&(name in Object)
  }
  
  function Person(){}
  Person.prototype.name = "Hebe";
  Person.prototype.age = 29;
  Person.prototype.job = "software";
  person.prototype.sayName = function(){
      alert(this.name)
  }
  var person = new Person()
  
  function Person(){}
  Person.prototype = {
      constructor:Person,
      name : 'Hebe',
      age : 19,
      job : 'software',
      sayName : function(){
          alert(this.name)
      }
  }
  
  ```

- 组合构造函数和原型模式(常用)

  ```javascript
  function Person(name,age,job){
      this.name = name;
      this.age = age;
      this.job = job;
      this.friends = ['Alice','Bob']
  }
  Person.prototype = {
      constructor : Person,
      sayName = function(){
          alert(this.name)
      }
  }
  ```

- 动态原型模式

- 寄生构造函数模式(代码和工厂模式相同，只是创建实例是使用new操作符)

- 稳妥构造函数模式（没有公共属性，即不使用this，所以访问属性只能通过方法访问，并且创建实例不使用new）

#### 3、继承

原型链：每个构造函数都有一个原型对象（prototype），原型对象都有一个指向构造函数的指针（constructor），而实例都包含一个指向原型对象的指针（__proto__）。但是原型对象中也包含一个指向另一个原型的指针，以此递进，知道原型对象为null，这就是原型链。

- 原型链继承(引用类型的属性被所有实例共享、不能向超类型传递参数)

  改变SubType的原型对象，使其原型对象是SuperType的实例，这样，SubType的原型对象中不仅具有SuperType的属性和方法，还有个指针指向SuperType的原型

  ```javascript
  function SuperType(){
      this.property = true
  }
  SuperType.prototype.getSuperValue = function(){
      return this.property
  }
  
  function SubType(){
      this.subProperty = false
  }
  SubType.prototype = new SuperType()
  SubType.prototype.getSubValue = function(){
      return this.subProperty
  }
  
  var instance = new SubType()
  alert(instance.getSuperValue)  //true
  ```

- 借助构造函数继承(方法都在构造函数中定义，无法复用)

  ```javascript
  function SuperType(name){
      this.name = name;
      this.color = ['red','orange','green'];
  }
  
  function SubType(){
      SuperType.call(this,'Hebe');
      this.age = 20;
  }
  
  var instance = new SubType()
  ```

- 组合继承（调用两次SuperType构造函数，使得SubType的实例中具有SuperType的属性，SubType的原型中也有）

  ```javascript
  function SuperType(name){
      this.name = name;
      this.color = ['red','orange','green'];
  }
  SuperType.prototype.sayName = function(){
      alert(this.name)
  }
  function SubType(name.age){
      SuperType.call(this,name) //继承属性，第一次调用
      this.age = age
  }
  
  SubType.prototype = new SuperType() //继承方法，第二次调用
  var instance = new SubType("Hebe",20)
  ```

- 原型式继承(引用类型属性只是浅复制，会被共享)

  ```javascript
  function object(obj){  
      //传入一个对象，返回对象原型指向这个对象的实例
      function F(){
      }
      F.prototype = obj
      return new F()
  }
  
  var person = {
      name = "Hebe",
      age = 20
  }
  
  var person1 = object(person)
  // var person1 = Object.create(person)
  ```

- 寄生式继承(方法不能复用)

  ```javascript
  function createObject(original){
      var obj = object(original)
      obj.sayHi = function(){
          alert('Hi')
      }
  }
  ```

- 寄生组合式继承（相比组合式继承，只调用一次超类型的构造函数，效率高）

  ```javascript
  function inheritPrototype(SubType,SuperType){
      var prototype = object(SuperType)
      prototype.constructor = SubType
      SubType.prototype = prototype
  }
  
  function SuperType(name){
      this.name = name
  }
  SuperType.prototype.sayName =function (){
      return this.name
  }
  
  function SubType(name,age){
      SuperType.call(this,name)
      this.age = age
  }
  
  inheritPrototype(SubType,SuperType)
  ```

  