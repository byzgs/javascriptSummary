## javascript基础(八、类的使用)

#### 1. 类的本质

- `class` 本质上只是个语法糖，为了让类的声明与继承更加简洁清晰
- 类其实是**函数**，类中定义的**方法**也保存在函数**原型**中

#### 2. 构造函数

- `constructor` 会在 new 时自动执行

- 构造函数用于传递对象的初始参数，但不是必须定义的，如果不设置系统会设置如下类型

  ```javascript
  constructor(...args) {
    super(...args);
  }
  ```

- 子构造器中调用完`super` 后才可以使用 `this`

#### 3. 函数差异

- `class` 是使用函数声明类的语法糖，但也有些区别
  - `class` 中定义的方法不能枚举
  - `class` 默认使用`strict` 严格模式执行

#### 4. 静态访问

1. 静态属性

   - 静态属性即为类设置属性，而不是为生成的对象设置(不继承)

   - 在 `class` 中为属性添加 `static` 关键字即声明为静态属性

   - 可以把为所有对象使用的值定义为静态属性

2. 静态方法

   - 在 `class` 中为方法添加 `static` 关键字即声明为静态方法
   - 原理就是`User.__proto__.show = function() {...}`
   - 对多个对象操作的方法，定义为静态方法

#### 5. 访问器

- 使用访问器可以管控属性，有效的防止属性随意修改

- 访问器就是在函数前加上 `get/set`修饰，操作属性时不需要加函数的扩号，直接用函数名

  ```javascript
  class User {
    constructor(name) {
      this.data = { name };
    }
    get name() {
      return this.data.name;
    }
    set name(value) {
      if (value.trim() == "") throw new Error("invalid params");
      this.data.name = value;
    }
  }
  let hd = new User("向军大叔");
  hd.name = "后盾人";
  console.log(hd.name);
  ```


#### 6. 访问控制

- 命名保护

  - 将属性定义为以 `_` 开始，来告诉使用者这是一个私有属性，请不要在外部使用。
  - 但这只是提示，就像吸烟时烟盒上的吸烟有害健康，但还是可以抽的

- Symbol

  - 下面使用 `Symbol`定义私有访问属性，即在外部通过查看对象结构无法获取的属性

  - ```javascript
    const protecteds = Symbol();
    class Common {
      constructor() {
        this[protecteds] = {};
        this[protecteds].host = "https://houdunren.com";
      }
      set host(url) {
        if (!/^https?:/i.test(url)) {
          throw new Error("非常网址");
        }
        this[protecteds].host = url;
      }
      get host() {
        return this[protecteds].host;
      }
    }
    class User extends Common {
      constructor(name) {
        super();
        this[protecteds].name = name;
      }
      get name() {
        return this[protecteds].name;
      }
    }
    let hd = new User("后盾人");
    hd.host = "https://www.hdcms.com";
    // console.log(hd[Symbol()]);
    console.log(hd.name);
    ```

- 使用WeakMap

  - **WeakMap** 是一组键/值对的集，下面利用`WeakMap`类型特性定义私有属性

  - ```javascript
    const _host = new WeakMap();
    class Common {
      constructor() {
        _host.set(this, "https://houdunren.com");
      }
      set host(url) {
        if (!/^https:\/\//i.test(url)) {
          throw new Error("网址错误");
        }
        _host.set(this, url);
      }
    }
    class Article extends Common {
      constructor() {
        super();
      }
      lists() {
        return `${_host.get(this)}/article`;
      }
    }
    let article = new Article();
    console.log(article.lists()); //https://houdunren.com/article
    article.host = "https://hdcms.com";
    console.log(article.lists()); //https://hdcms.com/article
    ```

- `private` 私有属性使用

  - 只能当前类来使用,且不允许继承

  - 使用 `#` 符号来标识

  - ```javascript
    class User {
      //private
      #host = "https://houdunren.com";
      constructor(name) {
        this.name = name ;
        this.#check(name);
      }
      set host(url) {
        if (!/^https?:/i.test(url)) {
          throw new Error("非常网址");
        }
        this.#host = url;
      }
      get host() {
        return this.#host;
      }
      #check = () => {
        if (this.name.length <= 5) {
          throw new Error("用户名长度不能小于五位");
        }
        return true;
      };
    }
    let hd = new User("后盾人在线教程");
    hd.host = "https://www.hdcms.com";
    console.log(hd.host);
    ```

- 详解继承

  - 属性继承

    - 属性继承的原型如下

    - ```javascript
      function User(name) {
        this.name = name;
      }
      function Admin(name) {
        User.call(this, name); 
      }
      let hd = new Admin("后盾人");
      console.log(hd);
      ```

    - 这就解释了为什么在子类构造函数中要先执行`super` --> 执行父类的constructor

    - ```javascript
      class User {
        constructor(name) {
          this.name = name;
        }
      }
      class Admin extends User {
        constructor(name) {
          super(name);
        }
      }
      let hd = new Admin("后盾人");
      console.log(hd);
      ```

  - 方法继承

    - 原生的继承主要是操作原型链，实现起来比较麻烦，使用 `class` 就要简单的多了。
    - 继承时必须在子类构造函数中调用 super() 执行父类构造函数
    - super.show() 执行父类方法

  - `super`

    - 表示从当前原型中执行方法
    - `super` 一直指向当前对象
    - 解决多次继承时的 `this` 传递问题
    - 使用 `super` 调用时，在所有继承中 `this` 始终为调用对象
    - `super` 是用来查找当前对象的原型，而不像上面使用 `this` 查找原型造成死循环
    - 也就是说把查询原型方法的事情交给了 `super`，`this` 只是单纯的调用对象在各个继承中使用

  - `constructor`

    - `super`指调父类引用，在构造函数`constructor`中必须先调用`super`
    - `super()` 指调用父类的构造函数
    - 必须在 `constructor` 函数里的`this` 调用前执行 `super()`-->为了子覆盖父
    - `constructor` 中先调用 `super` 方法的原理：`Parent.apply(this, args);`
    - `super.show()` --> 调用父级的方法

  - 静态继承原理

    - 静态的属性和方法也是可以被继承使用的，下面是原理分析

    - ```javascript
      function User() {}
      User.site = "后盾人";
      User.url = function() {
        return "houdunren.com";
      };
      function Admin() {}
      Admin.__proto__ = User;
      console.dir(Admin);
      console.log(Admin.url());
      ```

    - 下面使用 `class` 来演示静态继承

    - ```javascript
      class User {
        static site = "后盾人";
        static host() {
          return "houdunren.com";
        }
      }
      class Admin extends User {}
      console.dir(Admin);
      ```

- 对象检测

  - `instanceof`检测一个对象的原型链当中是否存在一个构造函数的原型

  - ```javascript
    //自己实现instanceof
    function checkPrototype(obj, constructor) {
      if (!obj.__proto__) return false;
      if (obj.__proto__ == constructor.prototype) return true;
      return checkPrototype(obj.__proto__, constructor);
    }
    ```

  - `isPrototypeOf()`使用 `isPrototypeOf` 判断一个对象是否在另一个对象的原型链中

- `mixin` 混合模式使用技巧

  - `JS`不能实现多继承，如果要使用多个类的方法时可以使用`mixin`混合模式来完成。

  - `mixin` 类是一个包含许多供其它类使用的方法的类

  - `mixin` 类不用来继承做为其它类的父类

  - ```javascript
    const Tool = {
      max(key) {
        return this.data.sort((a, b) => b[key] - a[key])[0];
      }
    };
    
    class Lesson {
      constructor(lessons) {
        this.lessons = lessons;
      }
      get data() {
        return this.lessons;
      }
    }
    
    Object.assign(Lesson.prototype, Tool);//将Tool方法minin进来
    const data = [
      { name: "js", price: 100 },
      { name: "mysql", price: 212 },
      { name: "vue.js", price: 98 }
    ];
    let hd = new Lesson(data);
    console.log(hd.max("price"));
    ```

- 实例操作: 一个点击动画小案例

  ```javascript
  <style>
    * {
      padding: 0;
      margin: 0;
      box-sizing: content-box;
    }
    body {
      padding: 30px;
    }
    .slide {
      width: 300px;
      display: flex;
      flex-direction: column;
      /* box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.3); */
    }
    .slide dt {
      height: 30px;
      background: #34495e;
      color: white;
      display: flex;
      align-items: center;
      padding-left: 10px;
      cursor: pointer;
    }
    .slide dt:first-of-type {
      border-top-left-radius: 10px;
      border-top-right-radius: 10px;
    }
    .slide dd {
      height: 100px;
      background: #f1c40f;
      overflow: hidden;
    }
    .slide dd div {
      padding: 10px;
    }
    .slide dd:last-of-type {
      border-bottom-left-radius: 10px;
      border-bottom-right-radius: 10px;
    }
  </style>
  <body>
    <div class="slide s1">
      <dt>后盾人</dt>
      <dd>
        <div>houdunren.com</div>
      </dd>
      <dt>后盾人</dt>
      <dd>
        <div>hdcms.com</div>
      </dd>
      <dt>后盾人</dt>
      <dd>
        <div>hdcms.com</div>
      </dd>
    </div>
  </body>
  
  <script>
    class Animation {
      constructor(el) {
        this.el = el;
        this.timeout = 5;
        this.isShow = true;
        this.defaultHeight = this.height;
      }
      hide(callback) {
        this.isShow = false;
        let id = setInterval(() => {
          if (this.height <= 0) {
            clearInterval(id);
            callback && callback();
            return;
          }
          this.height = this.height - 1;
        }, this.timeout);
      }
      show(callback) {
        this.isShow = false;
        let id = setInterval(() => {
          if (this.height >= this.defaultHeight) {
            clearInterval(id);
            callback && callback();
            return;
          }
          this.height = this.height + 1;
        }, this.timeout);
      }
      get height() {
        return window.getComputedStyle(this.el).height.slice(0, -2) * 1;
      }
      set height(height) {
        this.el.style.height = height + "px";
      }
    }
    class Slide {
      constructor(el) {
        this.el = document.querySelector(el);
        this.links = this.el.querySelectorAll("dt");
        this.panels = [...this.el.querySelectorAll("dd")].map(
          item => new Panel(item)
        );
        this.bind();
      }
      bind() {
        this.links.forEach((item, i) => {
          item.addEventListener("click", () => {
            this.action(i);
          });
        });
      }
      action(i) {
        Panel.hideAll(Panel.filter(this.panels, i), () => {
          this.panels[i].show();
        });
      }
    }
    class Panel extends Animation {
      static num = 0;
      static hideAll(items, callback) {
        if (Panel.num > 0) return;
        items.forEach(item => {
          Panel.num++;
          item.hide(() => {
            Panel.num--;
          });
        });
        callback && callback();
      }
      static filter(items, i) {
        return items.filter((item, index) => index != i);
      }
    }
    let hd = new Slide(".s1");
  </script>
  ```

  
