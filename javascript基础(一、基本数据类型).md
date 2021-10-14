## javascript基础(一.基本数据类型)

#### 1. 基本数据类型的了解

- ##### 七种：
  
  - Number
  - String 
  - Boolean 
  - Null
  - Undefined
  - Symbol           -->(es6)  以上六种为原始数据类型
  - Object
  
- ##### Null
  
  - 表示空的对象
  - 用typeof 检查一个null -->返回object
  
- ##### Undefined
  
  - 声明一个变量而不赋值
  - 用typeof --> undefined
  
- ##### Boolean
  
  - true \ false
  
- ##### Number
  
  - 包含整数和浮点数
    - 浮点运算可能得到不精确的结果 --> 0.1+0.2 != 0.3
  - Number.MAX_VALUE -->能表示的最大值
  - Infinity  -->正无穷大
  - NaN 
    - 非法数字
    - typeof NaN -->number
  
- ##### String
  
  - 最大长度 2^53 -1 -->并不是字符数-->字符串的最大长度，实际上是受字符串的编码(UTF-16)长度影响的。
  
- ##### Symbol

  - 它是一切非字符串的对象 key 的集合

  - Symbol 可以具有字符串类型的描述，但是即使描述相同，Symbol 也不相等。-->全局唯一

  - 我们创建 Symbol 的方式是使用全局的 Symbol 函数。

    ```javascript
    var mySymbol = Symbol("my symbol");
    ```

- ##### 对象(引用)类型

  - Object

  - Function  -->可执行的对象

  - Array        -->有序的特殊对象

  - 分类：

    - **宿主对象** (host Objects) --> javascript宿主环境提供的对象，它们的行为完全由宿主环境决定、

      - 全局对象 window 一部分来自浏览器环境 一部分来自 javascript

    - **内置对象** (Built-in Objects) --> javascript语言提供的对象

      - **固有对象** (Intrinsic Objects) --> 由标准规定，随着Javascript运行时创建而自动创建的对象实例
      - **原生对象** (Native Objects) 可以由用户通过Array、RegExp等内置构造器或特殊语法创建的对象
      - **普通对象** (Ordinary Objects) 由{}语法、Object构造器或者class关键字定义类创建的对象，它能够被原型继承

    - 用对象来模拟函数与构造器：函数对象与构造器对象

      - 固有对象包含了所有的原生对象的创造者，例如 var array = new Array(), 其中 array 是原生对象，而Array是其创造者，属于固有对象。

        这些固有对象往往包含两个特殊的私有属性 [[call]] 和 [[construct]]，拥有 [[call]] 的对象可以作为函数进行调用，拥有 [[construct]] 的可以作为构造器被new调用。这两个属性是由js引擎写入，通过上层js代码是无法进行赋值或修改的。

      - 对于宿主和内置对象来说，它们实现[[call]]（作为函数被调用）和[[construct]]（作为构造器被调用）不总是一致的。如: Date() 和 new Date, Image() 和 new Image

      - 在 ES6 之后 => 语法创建的函数仅仅是函数，它们无法被当作构造器使用，

        ```javascript
        new (a => 0) // error
        ```

      - 我们大致可以认为，使用 function 语法或者 Function 构造器创建的对象[[construct]]的执行过程如下：

        1. 以 Object.prototype 为原型创建一个新对象;

           - ```javascript
              let obj = Object.create(Constructor.prototype);
             ```

        2. 以新对象为 this，执行函数的[[call]]；

           - ```javascript
             let resObj = Constructor.apply(obj, arguments);
             ```

        3. 如果[[call]]的返回值是对象，那么，返回这个对象，否则返回第一步创建的新对象。

           - ```javascript
             return typeOf resObj === 'object' && resObj !== null ? resObj : obj;
             ```

        - 这样的规则造成了个有趣的现象，如果我们的构造器返回了一个新的对象，那么 new 创建的新对象就变成了一个构造函数之外完全无法访问的对象，这一定程度上可以实现“私有”。

          --> **闭包的背景知识**

          ```javascript
          function cls(){ 
              this.a = 100; 
              return { 
                  getValue:() => this.a  //这个this指向cls --> 箭头函数的知识
              }
          }
          var o = new cls;	//所以这里只能访问到return后的数据
          					//即var o={getValue:() => this.a;}; 所以对于 o 来说， a 					是未定义。这个this指向cls
          o.getValue();//100
          //闭包的背景知识，在其它面向对象的语言中 a就相当于私有变量（或属性），只能通过方法来进行访问，a对于外部而言不可见
          ```

    - 特殊行为的对象 

      -->它们常见的下标运算（就是使用中括号或者点来做属性访问）或者设置原型跟普通对象不同

      - Array：Array 的 length 属性根据最大的下标自动发生变化
      - Object.prototype：作为所有正常对象的默认原型，不能再给它设置原型了。
      - String：为了支持下标运算，String 的正整数属性访问会去字符串里查找。
      - Arguments：arguments 的非负整数型下标属性跟对应的变量联动。
      - 模块的 namespace 对象：特殊的地方非常多，跟一般对象完全不一样，尽量只用于 import 吧
      - 类型数组和数组缓冲区：跟内存块相关联，下标运算比较特殊。
      - bind 后的 function：跟原来的函数相关联。

  - 不使用 new 运算符，尽可能找到获得对象的方法

    - ```javascript
      // 1. 利用字面量
      var a = [], b = {}, c = /abc/g
      // 2. 利用dom api
      var d = document.createElement('p')
      // 3. 利用JavaScript内置对象的api
      var e = Object.create(null)
      var f = Object.assign({k1:3, k2:8}, {k3: 9})
      var g = JSON.parse('{}')
      // 4.利用装箱转换
      var h = Object(undefined), i = Object(null), k = Object(1), l = Object('abc'), m = Object(true)
      ```

#### 2. 对类型的判断 + 类型转换

- ##### 对类型的判断

  - typeof
    - --> 返回数据类型的字符串表达 'object'
    - 可以判断： undefined / Number / String / Boolean
    - 不能判断：null / object
  - instanceof
    - --> 判断对象的具体类型
    - A insyanceof B --> A 是否是 B 的实例
  - === 
    - 可以判断undefined / null

- ##### 补充问题

  1. undefined 与 null 的区别

     --> undefined 代表定义未赋值，null 定义并赋值了，只是值为null

  2. 什么时候赋值为null?

     --> 初始为null，表示将要赋值为对象

     --> 最后让x指向的对象成为垃圾对象(被回收机制 回收)

  3. 严格区别变量类型与数据类型

     --> 数据的类型

     	1. 基本类型
     	2. 对象类型

     --> 变量的类型（变量内存值的类型）

     	1. 基本类型 ： 保存的是基本类型的数据
     	2. 引用类型 ： 保存的是地址值

- ##### 强制类型转换  

  - 转为String
    1. a.toString()方法  -->不会影响到原变量，将转换结果返回  a = a.toString() -->null和undefined没有
    2.  String() 函数 --> a = String(a) -->对于number和boolean 实际上就是调用toString()方法

  - 转为Number

    1. Number()函数

       - 字符串 -->数字
         - 纯数字-->数字
         - 有非数字-->NaN
         - 空串/全是空格 -->0

       - Boolead -->Number
         - true -- 1 / false -- 0

       - undefined --> NaN
       - null --> 0

    2. 针对字符串 parseInt/parseFloat
       - 如果对非String使用 --> 先将其转换为字符串，再操作

  - 转为Boolean

    1. 使用Boolean()函数
       - Number-->Boolean     除了0和NaN，其他都是true
       - String --> Boolean 除了空串，其他都是true
       - null/undefined --> false
       - object --> true

- ##### 隐式转换
  
  - 任何值和字符串做+运算都会先转换为字符串，然后再和字符串拼串 c + "" -->实际上String()
  - 任何值做- * / 运算时 都会自动转换为Number 直接 +a 也可以转换为Number
  - 取反两次 --> Boolean
  - <img src="https://upload-images.jianshu.io/upload_images/2791152-ba592aa9b81fe174.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" style="zoom:50%;" />
  - 如图，任意两种类型比较时，如果不是同一个类型比较的话，则按如图方式进行相应类型转换，如对象和布尔比较的话，对象 => 字符串 => 数值 布尔值 => 数值。
  
- ##### 装箱操作

  每一种基本类型 Number、String、Boolean、Symbol 在对象中都有对应的类，所谓装箱转换，正是把基本类型转换为对应的对象，它是类型转换中一种相当重要的种类。

  - 全局的 Symbol 函数无法使用 new 来调用，但我们仍可以利用装箱机制来得到一个 Symbol 对象，我们可以利用一个函数的 call 方法来强迫产生装箱。

    ```javascript
    var symbolObject = (function(){ return this; }).call(Symbol("a")); console.log(typeof symbolObject); //object 
    console.log(symbolObject instanceof Symbol); //true
    console.log(symbolObject.constructor == Symbol); //true
    ```

  - 使用内置的 Object 函数，我们可以在 JavaScript 代码中显式调用装箱能力。

    ```javascript
    	var symbolObject = Object(Symbol("a"));
    
        console.log(typeof symbolObject); //object
        console.log(symbolObject instanceof Symbol); //true
        console.log(symbolObject.constructor == Symbol); //true
    ```

  - 每一类装箱对象皆有私有的 Class 属性，这些属性可以用 Object.prototype.toString 获取：

    ```javascript
        var symbolObject = Object(Symbol("a"));
    
        console.log(Object.prototype.toString.call(symbolObject)); //[object Symbol]
    ```

  - 在 JavaScript 中，没有任何方法可以更改私有的 Class 属性，因此 

    - Object.prototype.toString 是可以准确识别对象对应的基本类型的方法，它比 instanceof 更加准确。
    - 但需要注意的是，call 本身会产生装箱操作，所以需要配合 typeof 来区分基本类型还是对象类型。

- ##### 拆箱转换

  - ToPrimitive 函数，转换为原始类型。它是对象类型到基本类型的转换（即，拆箱转换）--> 先拆箱，再转换

  -  拆箱转换会尝试调用 valueOf 和 toString 来获得拆箱后的基本类型。如果 valueOf 和 toString 都不存在，或者没有返回基本类型，则会产生类型错误 TypeError。

    ```javascript
        var o = {
            valueOf : () => {console.log("valueOf"); return {}},
            toString : () => {console.log("toString"); return {}}
        }
    
        o * 2
        // valueOf
        // toString
        // TypeError
    	//先执行了 valueOf，接下来是 toString，最后抛出了一个 TypeError，这就说明了这个拆箱转换失败了。
    ```

  - 到 String 的拆箱转换会优先调用 toString。我们把刚才的运算从 o*2 换成 String(o)，那么你会看到调用顺序就变了。

    ```javascript
        var o = {
            valueOf : () => {console.log("valueOf"); return {}},
            toString : () => {console.log("toString"); return {}}
        }
    
       String(o)
        // toString
        // valueOf
        // TypeError
    ```

  - 在 ES6 之后，还允许对象通过显式指定 @@toPrimitive Symbol 来覆盖原有的行为。

    ```javascript
        var o = {
            valueOf : () => {console.log("valueOf"); return {}},
            toString : () => {console.log("toString"); return {}}
        }
    
        o[Symbol.toPrimitive] = () => {console.log("toPrimitive"); return "hello"}
    
    
        console.log(o + "")
        // toPrimitive
        // hello
    ```

#### 3. 数据、内存、变量的关系 + 深、浅拷贝

- ##### 数据 -- 内存 -- 变量

  1. 什么是数据

     存储在内存中代表特定信息的东西

     特点： 可传递，可运算

     内存中所有操作的目标：数据

     - 算数运算
     - 逻辑运算
     - 赋值
     - 运行函数

  2. 什么是内存

     - 内存条通电后产生的可储存信息的空间

     - 内存分类：

       - 栈：全局变量/局部变量
       - 堆：对象

     - js如何管理内存

       1. 内存生命周期	分配小内存空间，得到使用权 --> 存储数据，反复操作 --> 释放小内存空间

       2. 释放内存    -- 局部变量：函数执行完自动释放

          ​					--对象： 成为垃圾对象 -->垃圾回收机制回收

  3. 什么是变量

     可变化的量，由变量名和变量值组成

     每个变量对应一块小内存，变量名用来查找对应的内存，变量值就是内存中保存的数据

  4. 他们三者之间的关系

     - 内存用来存储数据的空间
     - 变量是内存的标识

  5. 引用变量赋值的问题

     - 2个引用变量指向同一个对象，改A --> B也变

     - js调用函数时传递变量参数，是值传递还是引用传递？ 

       - 理解1 都是值(基本/地址值)传递
       - 理解2 可能是值传递，也可能是引用传递(地址值)

     - 引出问题：**深浅拷贝**

     - 赋值

       **赋的其实是该对象的在栈中的地址，而不是堆中的数据。也就是两个对象指向的是同一个存储空间，无论哪个对象发生改变，其实都是改变的存储空间的内容**

       - 三者区别一览
       - <img src="https://upload-images.jianshu.io/upload_images/13253432-4eae0bbcf9d34ae3?imageMogr2/auto-orient/strip|imageView2/2/w/620/format/webp" style="zoom: 67%;" />

     - 浅拷贝

       **浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。**

       ```javascript
       // 浅拷贝
       var obj1 = {
          'name' : 'zhangsan',
          'age' :  '18',
          'language' : [1,[2,3],[4,5]],
       };
       var obj3 = shallowCopy(obj1);
       obj3.name = "lisi";
       obj3.language[1] = ["二","三"];
       function shallowCopy(src) {
          var dst = {};
          for (var prop in src) {
              if (src.hasOwnProperty(prop)) {
                  dst[prop] = src[prop];
              }
          }
          return dst;
       }
       console.log('obj1',obj1)
       /*
       obj1 Object
           age: "18"
           language: Array(3)
               0: 1
               1: (2) ["二", "三"]		-->变了
           2: (2) [4, 5]
           length: 3
           [[Prototype]]: Array(0)
           name: "zhangsan"			 -->没变
       [[Prototype]]: Object
       */
       console.log('obj3',obj3)
       /*
       obj3 Object
               age: "18"
               language: Array(3)
               0: 1
               1: (2) ["二", "三"]		-->
               2: (2) [4, 5]
               length: 3
               [[Prototype]]: Array(0)
               name: "lisi"			 -->
       [[Prototype]]: Object
       */
       ```

       - 浅拷贝的实现方式

         1. Object.assign({},obj)	-->注意，当obj只有一层的时候，是深拷贝

            ```javascript
            var obj = {
                a: {
                   name:"kobe",
                   age: 39
                }
            }
            var initalObj = Object.assign({},obj)
            initalObj.a.name = "wade";
            console.log(obj.a.name) // wade
            ```

         2. Array.prototype.concat()

            ```javascript
            let arr = [1, 3, {
               username: 'kobe'
            }];
            let arr2=arr.concat();    
            arr2[2].username = 'wade';
            console.log(arr);	//[1,3,{username: "wade"}]
            ```

         3. Array.prototype.slice()

            ```javascript
            let arr = [1, 3, {
               username: ' kobe'
            }];
            let arr3 = arr.slice();
            arr3[2].username = 'wade'
            console.log(arr);	//[1,3,{username:'wade'}]
            ```

            

     - 深拷贝

       **深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象**

       - 深拷贝的实现方式

         1. JSON.parse(JSON.stringfy())

            ```javascript
            let arr = [1, 3, {
               username: ' kobe'
            }];
            let arr4 = JSON.parse(JSON.stringify(arr));
            arr4[2].username = 'duncan';
            console.log(arr, arr4)
            ```

            - 原理：用JSON.stringify将对象转成JSON字符串，再用JSON.parse()把字符串解析成对象，一去一来，新的对象产生了，而且对象会开辟新的栈，实现深拷贝

            - 但是：**这种方法虽然可以实现数组或对象深拷贝，但不能处理函数**

              ```javascript
              let arr = [1, 3, {
                 username: ' kobe'
              },function(){}];
              let arr4 = JSON.parse(JSON.stringify(arr));
              arr4[2].username = 'duncan';
              console.log(arr, arr4);	// arr[3]:f() arr4[3]: null
              ```

              这是因为 `JSON.stringify()` 方法是将一个JavaScript值(对象或者数组)转换为一个 JSON字符串，不能接受函数。

         2. 手写递归方法

            - 原理：**遍历对象、数组直到里边都是基本数据类型，然后再去复制，就是深度拷贝**

              ```javascript
              const obj1 = {
                  name: 'zhanggenshuo',
                  age: 18,
                  address: {
                      country: 'china'
                  },
                  arr: ['a','b','c']
              }
              
              const obj2 = deepClone(obj);
              obj2.address.country = 'zhanjiang';
              obj2.name = 'yezi';
              obj2.arr[0] = 'ccc'
              console.log(obj1);
              
              //deepClone函数
              function deepClone(obj = {}) {
                  //判断为null 或者非对象-->直接返回
                  if(typeof obj !== 'object' || obj == null) {
                      return obj
                  }
                  
                  //初始化返回结果 
                  let result 
                  if(obj instanceof Array) {
                      result = []
                  }else {
                      return = {}
                  }
                  
                  for(let key in obj) {
                      //保证key不是原型的属性
                      if(obj.hasOwnProperty(key)) {
                          //递归调用，一个一个复制
                          result[key] = deepClone(obj[key])
                      }
                  }
                  return result
              }
              ```

         3. 函数库loadsh

            -  `_.cloneDeep()`

              ```javascript
              var _ = require('lodash');
              var obj1 = {
                 a: 1,
                 b: { f: { g: 1 } },
                 c: [1, 2, 3]
              };
              var obj2 = _.cloneDeep(obj1);
              console.log(obj1.b.f === obj2.b.f);
              // false
              ```

#### 4. 数组的挖掘

- 数组从 `0`开始编号 --> arr[0]

- 用 const 关键字定义数组，可以更改数组中的值，因为 const 定义的内存地址不变就可以

- `...args` --> 展开语法，可以转为数组(伪数组)，使用数组的方法

- 解构赋值 --> 将对象里的值批量地赋值给变量 

  ```javascript
  let arr = ['张根硕','18']
  let [name,year] = arr	//--> 一次性赋值多个变量
  console.log(name,year)
  ```

  

- 数组的方法

  - `arr.length` --> 属性，返回值是数组长度 --> 一个应用是 *arr.length = 0* 清空数组，--> 不用 *arr=[]*，是因为数组是引用类型，这样做是开辟一个新空间，其他指向该数组的仍未清空

  - `Array(5)` --> 创建长度为 5 的空数组

  - `Array.of()` --> 创建数组 let arr = Array.of(6) --> [6]

  - `isArray()` --> 检测是否是数组 --> 返回true/false

  - `join()` --> 连接数组成字符串 --> arr.join('&') --> 参数为 '' 就是转化为字符串

  - `str.split("")` --> 将字符串拆分为数组， --> str.split(",") 按照逗号拆分 

  - `Array.from(str)` -->  将有length属性的字符串(对象也可以，只要有 length属性 )拆分为数组

    还可以传入第二个参数，对DOM元素进行操作的函数

  - 添加元素的多种操作技巧

    - `array.push(...args)` --> 往后追加，返回值是追加后的数组长度

    - `array.unshift(...args)` --> 往前添加，返回值是添加后的数组长度

    - `arrar[array.length] = "hxx"` --> 一次加一个

    - `let array = [...array1,...array2,...array3]` --> 数组合并

    - `arr1 = arr1.concat(arr2,arr3)` --> 数组合并

    - `arr.copyWithin()` --> 复制数组元素到特定位置

      ```javascript
      let hd = [1,2,3,4,5,6]
      console.log(hd.copyWithin(4,1,3)) //[1,2,3,4,2,3] --> 将[1,3)复制到4位置
      ```

      

  - 数据出栈与入栈及填充操作

    - `array.pop()`  --> 从后弹出，返回值是弹出的元素
    - `array.shift()` --> 从前移出，返回值是移出的元素
    - `Array(5).fill('hxx',1,3)` --> 填充满 hxx 到 `[1,3)` 位置

  - `arr.slice(1,3)` --> 截取 `[1,3)`的数组元素，并返回 --> 参数写一个就是从x开始直到末尾 ---> 不改变原数组 

  - `arr.splice(x,y)` --> 从下标 x 开始，截取 y 个，并返回 --> 会改变原数组 -->截取操作

    `arr.splice(1,1,'hxx') `--> 从 1 开始，截取 1 个，再填充进去 'hxx' --> 替换操作

    `arr.splice(2,0,'zgs')` --> 在 2 后面，增加 'zgs' --> 增加操作

  - 查找元素基本使用
    - `arr.indexOf('hxx',2)` --> 从**左**侧**下标为 2 **的开始查找，查找到就返回下标(懒式查找)，查找不到则返回 **-1** --> 严格类型
    - `arr.lastIndexOf('zgs',-2)` --> 从**右**侧**倒数第2个**开始查找，查找到就返回下标(懒式查找)，查找不到则返回 **-1** --> 严格类型
    - `arr.includes('gjj')` --> 查找到返回 true /否则 false --> 无法查找引用类型(因为地址不同)
    - `arr.find(function(item) {return item.name == 'zgs'})` --> 返回此元素,没找到就返回 **undefined**
    - `arr.findIndex(function(item) {return item.name == 'hxx'})` --> 返回此元素的下标，没找到就返回 **-1**

