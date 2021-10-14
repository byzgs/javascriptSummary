## javascript基础(三.语句)

#### 1. if语句

#### 2. do-while语句

- 后测试语句，代码至少执行一次

#### 3. while语句

- 前测试语句

#### 4. for语句

- 前测试语句

- `for in` 、 `for of ` 与 ` forEach `

  - for in 只能获得对象的键名，不能获得键值

  - for of 允许遍历获得键值

    ```javascript
    var arr = ['red', 'green', 'blue']
     
    for(let item in arr) {
      console.log('for in item', item)
    }
    /*
      for in item 0
      for in item 1
      for in item 2
    */
     
    for(let item of arr) {
      console.log('for of item', item)
    }
    /*
      for of item red
      for of item green
      for of item blue
    */
    ```

  - 对于普通对象，没有部署原生的 iterator 接口，直接使用 for...of 会报错

  - 可以使用 for...in 循环遍历键名

    ```javascript
    var obj = {
       'name': 'Jim Green',
       'age': 12
     }
     
     for(let key of obj) {
       console.log('for of obj', key)
     }
     // Uncaught TypeError: obj is not iterable
    
    for(let key in obj) {
       console.log('for in key', key)
     }
     /*
       for in key name
       for in key age
     */
    ```

    - 也可以使用 Object.keys(obj) 方法将对象的键名生成一个数组，然后遍历这个数组

      ```
      for(let key of Object.keys(obj)) {
         console.log('key', key)
       }
       /*
         key name
         key age
       */
      ```

  - for...in 循环不仅遍历数字键名，还会遍历手动添加的其它键，甚至包括原型链上的键。for...of 则不会这样

    ```javascript
    let arr = [1, 2, 3]
    arr.set = 'world'  // 手动添加的键
    Array.prototype.name = 'hello'  // 原型链上的键
     
    for(let item in arr) {
      console.log('item', item)
    }
     
    /*
      item 0
      item 1
      item 2
      item set
      item name
    */
     
    for(let value of arr) {
      console.log('value', value)
    }
     
    /*
      value 1
      value 2
      value 3
    */
    ```

  - forEach 循环无法中途跳出，break 命令或 return 命令都不能奏效,

    for...of 循环可以与break、continue 和 return 配合使用，跳出循环

    ```javascript
    let arr = [1, 2, 3, 5, 9]
    arr.forEach(item => {
      if(item % 2 === 0) {
        return
      }
      console.log('item', item)
    })
    /*
      item 1
      item 3
      item 5
      item 9
    */
    
    for(let item of arr) {
       if(item % 2 === 0) {
         break
       }
       console.log('item', item)
     }
     // item 1
    ```

  - 无论是 for...in 还是 for...of 都不能遍历出 Symbol 类型的值，遍历 Symbol 类型的值需要用 Object.getOwnPropertySymbols() 方法

    ```javascript
    {
      let a = Symbol('a')
      let b = Symbol('b')
    
      let obj = {
        [a]: 'hello',
        [b]: 'world',
        c: 'es6',
        d: 'dom'
      }
    
      for(let key in obj) {
        console.info(key + ' --> ' + obj[key])
      }
    
      /*
        c --> es6
        d --> dom
      */
    
      let objSymbols = Object.getOwnPropertySymbols(obj)
      console.info(objSymbols)    //  [Symbol(a), Symbol(b)]
      objSymbols.forEach(item => {
        console.info(item.toString() + ' --> ' + obj[item])
      })
    
      /*
        Symbol(a) --> hello
        Symbol(b) --> world
      */
    
      // Reflect.ownKeys 方法可以返回所有类型的键名，包括常规键名和Symbol键名
      let keyArray = Reflect.ownKeys(obj)
      console.log(keyArray)      //  ["c", "d", Symbol(a), Symbol(b)]
    }
    
    ```

    

- for in的特点

  1. for ... in 循环返回的值都是数据结构的 键值名。

  2. 遍历对象返回的对象的key值,遍历数组返回的数组的下标(key)。

  3. for ... in 循环不仅可以遍历数字键名,还会遍历原型上的值和手动添加的其他键。

  4. 特别情况下, for ... in 循环会以看起来任意的顺序遍历键名 --> 牵扯到常规属性和排序属性

  - ```javascript
    function Foo() {
      this[100] = 'test-100'
      this[1] = 'test-1'
      this["B"] = 'bar-B'
      this[50] = 'test-50'
      this[3] = 'test-3'
      this[5] = 'test-5'
      this["A"] = 'bar-A'
      this["C"] = 'bar-C'
    }
    var bar = new Foo()
    for(key in bar){
      console.log(`index:${key} value:${bar[key]}`)
    }
    
    /* 打印结果
    index:1 value:test-1
    index:3 value:test-3
    index:5 value:test-5
    index:50 value:test-50
    index:100 value:test-100	
    --> 数字属性应该按照索引值⼤⼩升序排列，对象中的数字属性称为「排序属性」
    -->在V8中被称为 elements，
    
    index:B value:bar-B
    index:A value:bar-A
    index:C value:bar-C		
    -->字符串属性就被称为「常规属性」，字符串属性根据创建时的顺序升序排列 
    --> 在V8中被称为 properties。
    
    */
    ```

  - **for in 循环特别适合遍历对象**

- for of 特点

  - for of 循环用来获取一对键值对中的值
  - 一个数据结构只要部署了 **Symbol.iterator** 属性, 就被视为具有 iterator接口, 就可以使用 for of循环
    - 数组 Array
    - Map
    - Set
    - String
    - arguments对象
    - Nodelist对象, 就是获取的dom列表集合
  - 凡是部署了 iterator 接口的数据结构也都可以使用数组的 扩展运算符(...)、和解构赋值等操作。

#### 5. break和continue

- break -->退出循环

- continue-->退出当次，从循环顶部继续执行/退出内部循环

- 配合label

  ```javascript
  var num = 0;
  
  outermost:
  for(let i = 0;i<10;i++) {
      for(var j = 0;j<10;j++) {
          if(i==5&&j==5) {
              continue outermost;	//	-->退出内部,55-59不执行
          }
          num++
      }
  }
  console.log(num)	//95
  ```

#### 6. swith语句

- ```javascript
  switch (i) {
      case 25:
          //合并两种情况,这里最好加备注，说明有意省略了break
      case 35:
          alert("25 or 35")
          break;		//每个 case 后面都跟break
      default:
          alert("Other")
  }
  ```

- 比较时用的是全等操作符，不会进行数据转换