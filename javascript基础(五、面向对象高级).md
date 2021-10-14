## javascript基础(五、面向对象高级)

#### 1. 对象创建模式

- 方式1：Object 构造函数模式
  - 先创建空 Object 对象，再动态添加属性/方法 `var p = new Object()`
  - 使用场景：起始时不确定对象内部数据
  - 问题：语句太多
- 方式2： 对象字面量模式
  - 使用`{}`来创建对象，同时指定属性/方法
  - 使用场景：起始时对象内部数据是确定的
  - 问题：如果创建多个对象，有重复代码 
- 方式3： 工厂模式
  - 通过工厂函数动态创建对象并 return 该对象
  - 使用场景：需要创建多个对象
  - 问题：对象没有一个具体的类型，都是 Object 类型
- 方式4：自定义构造函数模式
  - 自定义构造函数，通过new创建对象
  - 使用场景：需要创建多个类型确定的对象
  - 问题：每个对象都有相同的数据(方法)，浪费内存
- 方式5：构造函数 + 原型的组合模式
  - 自定义构造函数，`属性`在*函数*中初始化，`方法`添加到*原型*上 `Person.prototype.setName = fun...`
  - 使用场景：需要创建多个类型确定的对象

#### 2. 继承模式

- 方式1： 原型链继承
  - 关键：子类型的原型为父类型的一个实例对象
  - `Sub.prototype = new Supper` --> 为了得到*方法*
  - 让子类型的原型的 constructor 指向子类型
  - `Sub.prototype.constructor = Sub`
- 方式2：借用构造函数继承(假的)
  - 关键：在子类型中调用 call() 调用父类型的构造函数
  - 在子类型中: `Person.call(this,name,age)` --> 为了得到*属性*

- 方式3：组合继承

  - 就是方式1 + 方式2 实现属性与方法的继承

  ------

- 继承是原型的继承，改变构造函数原型并不是继承

  - 两种定义方式：

    1. `Admin.prototype.__proto__ = User.prototype;	`

       本来指向 Object.prototype,现在指向 User.prototype
       设置方式1：这样设置，何时添加自己的方法都没关系,因为相当于它自己没变，只是变了自己对上一级的指向

    2. `Member.prototype = Object.create(User.prototype)`

       设置方式2：这样设置，必须先Object.create再添加自己的方法，
       原因：这种方式是新建一个对象-->再让原型指过去-->再加方法

  - 解决 constructor 丢失

    1. `Admin.prototype.constructor = Admin;` --> 子类型的 constructor 会丢失，所以要指回去,还有个 constructor 被遍历问题，

    2. `Object.defineProperty(Member.prototype,'constructor',{
               value:Member,
               enumerable:false	//可遍历 默认为true
           })` --> 这样设置解决 constructor 可遍历问题

  - 方法的重写与父级属性访问

    - 方法重写：

      `Admin.prototype.name = function() {console.log("我是重写的name方法")}`

    - 父级属性访问：

      `console.log(User.prototype.show() + 'admin.name'`

  - 使用父类构造函数初始属性

    - 在子类中`User.apply(this,args)`

- ```javascript
  function User(school,grade) {
      this.school = school
      this.grade = grade
  }
  User.prototype.name = function() {
      console.log("user.name")
  }
  User.prototype.show = function() {
      return '雀氏秀'
  }
  
  function Admin() {
      Admin.prototype.__proto__ = User.prototype;	
      // 本来指向 Object.prototype,现在指向 User.prototype
      // 设置方式1：这样设置，位置就没关系,因为相当于它自己没变，只是变了自己对上一级的指向
      Admin.prototype.constructor = Admin;
      //子类型的constructor会丢失，所以要指回去,还有个constructor被遍历问题，看Member
      Admin.prototype.role = function() {
          console.log("admin.role")
      }
  }
  function Member() {
      Member.prototype = Object.create(User.prototype)
      // 设置方式2：这样设置，必须先Object.create再添加自己的方法，
      // 原因：这种方式是新建一个对象-->再让原型指过去-->再加方法
      Object.defineProperty(Member.prototype,'constructor',{
          value:Member,
          enumerable:false	//可遍历 默认为true
      })
      //解决constructor可遍历问题
      Member.prototype.role = function() {
          console.log("Member.role")
      }
  }
  
  let a = new Admin();
  a.role()	//"admin.role"
  
  Admin.prototype.name = function() {console.log("我是重写的name方法")}
  a.name()	//"我是重写的name方法"
  
  Admin.prototype.name = function() {
      console.log(User.prototype.show() + 'admin.name'
  }			//"雀氏帅 admin.name"
  
  function Student(...args) {
          User.apply(this,args)	//--> 属性继承
      }
  Student.prototype = Object.create(User.prototype)
  let hxx = new Student("北京邮电大学","大四")
  console.log(hxx.school + hxx.grade)	//"北京邮电大学大四" 
  ```

- 使用**原型工厂**封装继承

  ```javascript
  function extend(sub,sup) {
      sub.prototype = Object.create(sup.prototype)
      Object.defineProperty(sub.prototype,'constructor',{
          value:sub,
          enumerable:false
      })
  }
  
  function User(school,grade) {
      this.school = school
      this.grade = grade
  }
  User.prototype.name = function() {
      console.log("user.name")
  }
  User.prototype.show = function() {
      return '雀氏秀'
  }
  
  extend(Admin,User);
  Admin.prototype.name = function() {
          console.log("admin.name")
      }
  let a = new Admin();
  a.name()	//admin.name
  
  extend(Member,User);
  Member.prototype.name = function() {
          console.log("member.name")
      }
  let b = new Member();
  b.name()	//member.name
  ```

- **对象工厂派生对象并实现继承**

  ```javascript
  function User(name,age) {
      this.name = name 
      this.age = age
  }
  User.prototype.show = function() {
      console.log(this.name,this.age)
  }
  
  function admin(name,age) {
      const instance = Object.create(User.prototype)
      User.call(instance,name,age)
      instance.role = function() {
          console.log("张根硕来也")	//--> 子类添加方法
      }
      return instance					//--> 这就是对象工厂的意思
  }
  
  let hxx = admin("张根硕",18)
  hxx.show()
  hxx.role()
  
  let zyg = admin("古巨基",20)
  zyg.show()
  ```

- 多继承造成的困扰：为了继承某个方法，子无法同时继承两个父，只好子-->父-->爷，这样强行关联

- 使用 **mixin** 实现多继承

  - 将方法用对象的形式定义

    ```javascript
    const Request = {
        ajax() {
            console.log("请求后台")
        }
    }
    const Address = {
        getAddress() {
            console.log("获取地址")
        }
    }
    extent(Member,user)		//自己封装的原型继承
    Member.prototype = Object.assign(Admin.prototype,Request,getAddress)	
    //合并对象，用以实现多继承
    let hxx = new Member('张根硕',18)
    ```

    - mixin 的内部继承与 super 关键字

      ```javascript
      const Request = {
          ajax() {
              return "请求后台"
          }
      }
      const Address = {
          __proto__:request,
          getAddress() {
              //super = this.__proto__
              console.log(super.ajax() + "获取地址")
          }
      }
      ```

      

