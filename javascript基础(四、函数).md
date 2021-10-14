## javascript基础(四、函数)

#### 1. 什么是函数

- 实现特定功能的n条语句的封装体 --> 可执行

#### 2. 为什么要用函数

- 提高代码复用
- 便于阅读交流

#### 3. 如何定义函数

- 函数声明 `function fn() {}`
- 表达式  `var fn = function() {}`

#### 4. 如何调用(执行)函数

- fn() --> 直接调用
- obj.sayName() --> 通过对象的方法调用
- new test() --> new 调用
- test.call/allpy(obj) --> 临时让test成为obj的方法调用 --> 这也就是把this指向了该obj的原理

#### 5. 回调函数

- 什么是回调函数
  1. 自定义的
  2. 没主动调用
  3. 但他执行了
- 常见的回调函数
  - DOM事件回调函数
  - 定时器回调函数
  - Ajax回调函数
  - 生命周期回调函数

#### 6. 立即执行函数(IIFE)

- 理解  

- ```javascript
  (function() {})()
  ```

- ```javascript
  (function() {
      var a = 1
      function test() {
          console.log(++a)
      }
      window.$ = function() {	//向外暴露一个全局函数
          return {
              test: test
          }
      }
  })()
  // --> jQuary的封装、模块化
  ```

- 作用

  - 隐藏实现 
  - 防止污染外部(全局)命名空间
  - js模块编码

#### 7. 函数中的this

1.  this 是什么

   - 任何函数本质上都是通过某个对象来调用的
   - 所有函数内部都有一个变量this

2. 如何确定this的值

   - 它的值是:
     - 直接调用函数 --> window	
     - obj.方法 --> obj   谁调用指向谁
     - new test() --> 构造函数形式，this指向新创建的对象
     - p.call(obj)   p.apply(obj) --> obj 

   - this的4种绑定规则分别是：

     - 默认绑定、隐式绑定、显示绑定、new 绑定。优先级从低到高

     1. 默认绑定 ：没有其他绑定规则存在时的默认规则(严格模式下，全局对象无法使用默认绑定)

        --> window

     2. 隐式绑定：obj.foo();

        - 多层调用链，找最后一层

        - 隐式丢失(函数别名)

          ```javascript
          function foo() { 
              console.log( this.a );
          }
          
          var a = 2;
          var obj = { 
              a: 3,
              foo: foo 
          };
          
          var bar = obj.foo;
          bar(); //	2	
          	// -->obj.foo 是引用属性，赋值给bar的实际上就是foo函数（即：bar指向foo本身）。
          	//通过bar找到foo函数，进行调用。整个调用过程并没有obj的参数，所以是默认绑定，全局属性a
          ```

        - 隐式丢失(回调函数)

          ```javascript
          function foo() { 
              console.log( this.a );
          }
          
          var a = 2;
          
          var obj = { 
              a: 3,
              foo: foo 
          };
          
          setTimeout( obj.foo, 100 ); // 2
          	//虽然参传是obj.foo，因为是引用关系，所以传参实际上传的就是foo对象本身的引用
          	//对于setTimeout的调用，还是 setTimeout -> 获取参数中foo的引用参数 -> 执行 foo 函数，中间没有obj的参与。这里依旧进行的是默认绑定。
          ```

     3. 显示绑定

        - 通过改变对象的prototype关联对象 --> 使用 call() 或 apply() 方法

        - 硬绑定

          ```javascript
          function foo() { 
              console.log( this.a );
          }
          
          var a = 2;
          
          var obj1 = { 
              a: 3,
          };
          
          var obj2 = { 
              a: 4,
          };
          
          var bar = function(){
              foo.call( obj1 );
          }
          
          bar(); // 3
          setTimeout( bar, 100 ); // 3
          
          bar.call( obj2 ); // !!这是3  bar的this-->obj2，但是foo内的this仍-->obj1
          // -->这里需要注意下，虽然bar被显示绑定到obj2上，对于bar，function(){...} 中的this确实被绑定到了obj2，而foo因为通过foo.call( obj1 )已经显示绑定了obj1，所以在foo函数内，this指向的是obj1，不会因为bar函数内指向obj2而改变自身。所以打印的是obj1.a（即3）。
          ```

        - 规则例外: 在显示绑定中，对于null和undefined的绑定将不会生效。

          ```javascript
          function foo() { 
              console.log( this.a );
          }
          var a = 2;
          foo.call( null ); // 2
          foo.call( undefined ); // 2
          /*
          这种情况主要是用在不关心this的具体绑定对象（用来忽略this），而传入null实际上会进行默认绑定，导致函数中可能会使用到全局变量，与预期不符。
          
          所以对于要忽略this的情况，可以传入一个空对象ø，该对象通过Object.create(null)创建。这里不用{}的原因是，ø是真正意义上的空对象，它不创建Object.prototype委托，{}和普通对象一样，有原型链委托关系。
          */
          ```

     4. new绑定

        - 每次调用生成的是全新的对象，该对象又会自动绑定到this上

3. 箭头函数中的this

   - 它的this绑定取决于外层（函数或全局）作用域

     ```javascript
     function foo() {
         console.log('foo中的是' + this.a);
         return () => {
             console.log('箭头函数中的是' + this.a);
         }
     }
     
     
     
     var a = 2;
     
     var obj = {
         a: 3,
         foo: foo
     };
     
     var bar = obj.foo();
     bar(); //foo中的是3
     	   //箭头函数中的是3
     ```

   - 箭头函数中显示绑定不会生效

     ```javascript
     var foo = () => {     
         console.log( this.a );
     }
     
     var a = 2;
     
     var obj = { 
         a: 3,
         foo: foo 
     };
     
     obj.foo(); //2	外层作用域(此为全局)的a
     foo.call(obj); //2 ，箭头函数中显示绑定不会生效
     ```

     

4. call、apply、bind的区别及实现

   - ```javascript
     foo.call(obj,3,4);
     ```

   - ```javascript
     foo.apply(obj,[3,4]);	//call apply的区别仅为传参格式
     ```

   - call和apply会立即执行函数，bind不会

   - **call的实现**

     - 思路：

     1.  将函数作为要更改this绑定的对象的一个属性。也就是把函数作为call方法中第一个参数中的一个属性。
     2. 通过 `对象 . 方法` 执行这个函数。
     3. 返回当前函数执行后的结果。
     4. 删除该对象上的属性。

     - call的第一个参数还有几个特点：

     1. 当第一个参数(要更改的this绑定的对象)为null或者undefined时，this绑定为window(非严格模式)。如果为严格模式，均为第一个参数的值

     2. 当call方法中第一个参数为除null和undefined外的基本类型(String，Number，Boolean)时，先对该基本类型进行"装箱"操作

        ```javascript
        Function.prototype.myCall = function(context,...args) {
            let handler = Symbol();
            // 生成一个唯一的值，用来作为要绑定对象的属性key，储存当前调用call方法的函数
            //判断调用者的类型
            if(typeof this !== 'function') {
                //调用者不是函数
                throw this + '.myCall is not a function'
        }
            if(typeof context === 'object' || typeof context === 'function') {
                 // 如果为null 则this为window
                context = context || window;
            }else {
                // 如果为undefined 则this绑定为window
                if(typeof context === 'undefined') {
                    context = window;
                }else {
                    // 如果为String，Number，Boolean --> 基本类型包装
                    context = Object(context);
                }
            }
            
            context[handler] = this; //把调用者先存给目标对象context里,作为一个属性handler
            let result = context[handler](...args);	
            /*--------------这一步相当于context.handler()执行,更改了this指向-----------*/
            delete context[handler] //把handler属性删了
            retuen result;			//返回结果
        }
        ```

   - apply的实现

     - apply 和call唯一的区别是 除了第一个参数外，其他参数必须数组形式

       ```javascript
       Function.prototype.myApply = function (context, argsArr) {
           let handler = Symbol();
           if (typeof this !== 'function') {
               throw this + '.myApply is not a function'
           }
           let args = []
           // 如果传入的参数是不是数组，则无效
           if (typeof argsArr === 'object' 
               || typeof context === 'function' 
               || typeof argsArr === 'undefined') {
               args = Array.isArray(argsArr) ? argsArr : [];
           } else {
               // 如果为基本类型，如果是undefined，则无效，其它类型则抛出错误。
               throw 'TypeErrow: CreateListFromArrayLike called on non-object'
           }
           if (typeof context === 'object' || typeof context !== 'function') {
               context = context || window;
           } else {
               if (typeof context === 'undefined') {
                   context = window;
               } else {
                   context = Object(context);
               }
           }
       
           context[handler] = this
           let result = context[handler](...args)
           delete context[handler]
           return result
       }
       ```

   - bind的实现

     bind的特点：

     1. 调用bind方法会创建一个新函数,我们称它为绑定函数(boundF)
     2. 当我们直接调用boundF函数时，内部this被绑定为bind方法的第一个参数
     3. 当我们把这个boundF函数当做构造函数通过new关键词调用时，函数内部的this绑定为新创建的对象。(相当于bind提供的this值被忽略)
     4. 调用bind方法时，除第一个参数外的其余参数，将作为boundF的预置参数，在调用boundF函数时默认填充进boundF函数实参列表中

     bind方法中第一个参数的特点：

     1. 当第一个参数(要更改的this绑定的对象)为null或者undefined时，this绑定为window(非严格模式)
     2. 当call方法中第一个参数为除null和undefined外的基本类型(String，Number，Boolean)时，先对该基本类型进行"装箱"操作。

#### 8. 原型 prototype

1. 函数的prototype 属性

   - 每个函数都有一个 prototype 属性，它默认指向一个Object空对象(原型对象)
   - 原型对象中有个属性 constructor 它指向函数对象
2. 给原型对象添加属性

   - 作用: 函数的所有实例对象自动拥有原型中的属性(方法)
3. 显示原型(`prototype`)和隐式原型(`__proto__`)

   - 每个**函数** function 里有个`prototype` ，默认指向**原型对象** --> 定义函数时自动添加，默认为空Object对象	---->(只有Object例外 Object.prototype)
   - 每个**实例对象**都有一个 `__proto__`,它也指向**原型对象** --> 创建对象时添加，内部语句:`this.__proto__ = Fn.prototype`,**实例对象的隐式原型 === 构造函数的显示原型**
   - **原型对象**的`constructor`，指向构造函数.
4. setPrototypeOf 与 getPrototypeOf 
   - 设置、获取对象的原型
   -  --> `__proto__`是浏览器厂商搞的，非标准，可以设置也可以获取，并不是严格意义上的一个属性，它会根据具体操作来 setter 或 getter
5. Object.creat()
   - 创建一个新对象时使用现有对象做为新对象的原型对象 `let hd = Object.create(user);`
6. 原型链  -->(隐式原型链)

   - 访问一个对象的属性(方法)时
     - 先在自身属性(方法)找，找到就返回
     - --> 如果没有找到，沿着`__proto__`向上找**原型对象** 
     - --> 再找**Object的原型对象**,这是尽头，`Object.prototype.__proto__=null`

   - 方法一般定义在原型中，属性在实例上
   - ![](https://img2020.cnblogs.com/blog/1878009/202101/1878009-20210126085016996-914526814.jpg)
7. instanceof 

   - `A instanceof B` 如果B的显示原型对象在A对象的原型链上，返回true
8. isPrototypeOf
   - `A isPrototypeOf B`检测一个对象是否存在于另一个对象的原型链中
9. in
   - in 不仅会检测对象，还会检测原型链
10. hasOwnProperty
    - 只会单纯的检测当前对象，不会追溯原型链(不会管继承属性)

#### 9.变量提升

- 变量声明提示
  - 通过var定义(声明)的变量，在定义语句前就可以访问到
  - 值: undefined
- 函数声明提升
  - 通过 function 声明的函数，在 之前就可以直接调用 --> 用 `var fn1 = function() {}`这样定义的不行
  - 值: 函数定义(对象)
- 变量提示和函数提升是如何产生的？
  - 先执行变量提示 --> 再执行函数提升
  - 全局执行上下文：
    - 在执行全局代码前将window确定为全局执行上下文
    - 对全局数据进行预处理
      - var 定义的全局变量 --> undefined ，添加为 window 的属性
      - function 声明的全局函数 --> 赋值(fun)，添加为 window 的方法
      - this --> 赋值(window)
    - 开始执行全局代码
  - 函数执行上下文
    - 在调用函数，准备执行函数体之前，创建对应的 函数执行上下文对象 (虚拟的活动对象，存放在栈中)
    - 对局部数据进行预处理
      - 形参变量-->赋值(实参)-->添加为执行上下文的属性
      - arguments(伪数组)-->赋值(实参列表)-->添加为执行上下文的属性
      - var 定义的全局变量 --> undefined ，添加为执行上下文 的属性
      - function 声明的全局函数 --> 赋值(fun)，添加为 执行上下文 的方法
      - this --> 赋值(调用函数的对象)
    - 开始执行函数体代码
  - 执行上下文栈
    1. 在全局代码执行前，js引擎就会创建一个`栈`来存储管理所有的执行上下文对象  --> (因此生成N+1个执行上下文对象)
    2. 在全局执行上下文(window)确定后，将其压入栈中
    3. 在函数执行上下文创建扣，将其压入栈中
    4. 当前函数执行完后，栈顶对象出栈
    5. 所有代码执行完后，栈中只剩下 window

#### 10. 作用域与作用域链

- 作用域
  - 分类
    - 全局作用域
    - 函数作用域
    - 块作用域 (ES6+) --> let / const
  - 作用
    - 隔离变量 --> 不同作用域下 同名变量不会有冲突
  - **作用域**和**执行上下文**的区别
    1. 区别1
       - 全局作用域之外，每个函数都会创建自己的作用域，作用域在函数定义时就已经确定了。而不是在函数调用时。
       - 全局执行上下文环境是在全局作用域确定之后，js代码马上执行前创建
       - 函数执行上下文环境是在调用函数时，函数体代码执行之前创建
    2. 区别2
       - 作用于是静态的，只要函数定义好了就一直存在，且不会再变化
       - 执行上下文是动态的，调用函数时创建，函数调用结束时上下文环境就会自动释放
    3. 联系
       - 执行上下文(对象)时从属于所在的作用域
       - 全局执行上下文 --> 全局作用域
       - 函数执行上下文 --> 对应的函数作用域

- 作用域链

  - 一层一层往外层作用域找，就近原则 --> 一直找到全局作用域，找不到报错

  - ```javascript
    var x = 10
    function fn() {
        console.log(x);
    }
    
    function show(f) {
        var x = 20
        f()
    }
    
    show(fn)	// 10 --> 作用域是静态的，并不会因为放在其他函数里执行而改变作用域
    ```

#### 11.闭包

- 如何产生闭包

  - 当一个嵌套的内部(子)函数引用了嵌套的外部(父)函数的变量(函数)时，就产生了闭包

- 闭包到底是什么

  - 理解一：闭包是嵌套的内部函数
  - 理解二：包含被引用变量的对象
  - 注意：闭包存在于嵌套的内部函数中
  - 闭包其实只是一个绑定了执行环境的函数。闭包与普通函数的区别是，它携带了执行的环境。

- 闭包的特点

  - 让外部变量访问函数内部变量成为可能
  - 局部变量会常驻在内存中
  - 可以避免使用全局变量，防止全局变量污染
  - --> 会造成内存泄漏(有一块内存空间被长期占用而不被释放(意外的全局变量、没清理的定时器、闭包))

- 闭包的用途

  - 可读取函数内部的变量
  - 让函数内部的某些变量保持在内存中

- 使用闭包的注意点

  - 闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题 

    --> 解决办法：将不使用的局部变量全部删除 `desc = null`

  - 闭包会在父函数外部，改变父函数内部变量的值。 

    --> 如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值

- 产生闭包的条件

  - 函数嵌套
  - 内部函数引用了外部函数的数据 (变量/函数) ，并执行函数定义

- 常见的闭包

  - 将函数作为另一个函数的返回值

    ```javascript
    function m1(){
         var x = 1;
         return function(){
              console.log(++x);
         }
    }
     
    m1()();   //2
    m1()();   //2
    m1()();   //2
     
    var m2 = m1();
    m2();   //2
    m2();   //3
    m2();   //4
    ```
  
    
  
  - 将函数作为实参传递给另一个函数调用
  
    ```javascript
    var arr = [1,2,5,9,44,66,88,22,3,7]
    function between(a,b) {
        return function(v) {
            return v >= a && v <= b
        }
    }
    
    console.log(arr.filter(between(10,50)));
    ```
  
- 闭包的应用： 防抖 、节流

  - **防抖** `debounce` --> 控制次数

    - 所谓防抖，就是指触发事件后在 n 秒内函数只能执行一次，如果在 n 秒内又触发了事件，则会重新计算函数执行时间

    - *非立即防抖*：触发事件后函数不会立即执行，而是在n秒之后执行，如果n秒之内又触发了事件，则会重新计算函数执行时间

    - *立即防抖*：触发事件后函数会立即执行，然后n秒内不触发事件才会执行函数的效果

    - ```javascript
      function deboubce(func,wait = 50,immediate = true) {
          let timer,context,args
          // 延迟执行函数
          const later = ()=> setTimeout(()=> {
              // 延迟函数执行完毕，清空缓存的定时器序号
              timer = null
              if(!immediate) {
                  // 延迟执行的情况下，函数会在延迟函数中执行
                  // 使用到之前缓存的参数和上下文
                  func.call(context,args)
                  context = args = null
              }
          },wait)
          
          // 这里返回的函数是每次实际调用的函数
          return function(...params) {
              // 如果没有创建延迟执行函数（later），就创建一个
              if(!timer) {
                  timer = later()
                  // 如果是立即执行，调用函数
                  // 否则缓存参数和调用上下文
                  if(immediate) {
                      func.call(this,params)
                  }else {
                      context = this
                      args = params
                  }
                  // 如果已有延迟执行函数（later），调用的时候清除原来的并重新设定一个
                  // 这样做延迟函数会重新计时
              }else {
                  clearTimeout(timer)
                  timer = later()
              }
          }
      }
      ```
      
    - 
  
      
  
  - **节流** `throttle` --> 控制频率
  
    - 将多次执行变成每隔一段时间执行
  
    - 延迟节流 --> 在周期结束时，执行动作
  
    - 前缘节流 --> 先执行动作，再开始周期
  
    - ```javascript
      var throttling = (fn, wait = 50, immediate = false) => {
          let timer, timeStamp = 0;
          let context, args;
      
          let run = () => {
              timer = setTimeout(() => {
                  if (!immediate) {
                      fn.apply(context, args);
                  }
                  clearTimeout(timer);
                  timer = null;
              }, wait);
          }
      
          return function (...params) {
              context = this;
              args = params;
              if (!timer) {
                  //console.log("throttle, set");
                  if (immediate) {
                      fn.apply(context, args);
                  }
                  run();
              } else {
                  //console.log("throttle, ignore");
              }
          }
      
      }
      ```
  
  - 闭包应用3：自定义js模块
  
    - 具有特定功能的js文件，将所有的数据和功能封装在一个函数内部，只向外暴露一个包含n个对象的方法或函数,模块的使用者，通过模块暴露的对象调用方法来实现对应的功能
  
    - ```javascript
      //方法1：函数模块，return向外暴露对象
      function myModule() {
          var msg = 'Zhang GenShuo'
          
          function doSomething() {
              console.log('doSomething' + msg.toUpperCase())
          }
          function doOtherthing() {
              console.log('doOtherthing' + msg.toLowerCase())
          }
          
          //向外暴露对象
          return {
              doSomething,
              doOtherthing
          }
      }	//外部引用需要import,
      	//var module = myModule()
      	//module.doSomething()
      ```
  
    - ```javascript
      //方法2：自调用函数模块，挂载到window
      ;(function myModule2(window) {
          var msg = 'Zhang GenShuo'
          
          function doSomething() {
              console.log('doSomething' + msg.toUpperCase())
          }
          function doOtherthing() {
              console.log('doOtherthing' + msg.toLowerCase())
          }
          
          //向外暴露对象
          window.myModule2 = {
              doSonething,
              doOtherthing
          }
      })(window)	//写window是为了照顾代码压缩
      			//外部引用需要import
      			//myModule2.doSomething()
      ```
  
    - 

