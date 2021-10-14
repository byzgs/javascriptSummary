## Map与WeakMap类型

#### 1. Map类型

- Map是一组键值对的结构，用于解决以往不能用对象做为键的问题

  - 具有极快的查找速度
  - 函数、对象、基本类型都可以作为键或值

- `map.set()` 方法添加元素，支持链式操作

  - map.set(obj, "houdunren.com").set("name", "hdcms");

- 使用构造函数`new Map`创建的原理如下

  - ```javascript
    const hd = new Map();
    const arr = [["houdunren", "后盾人"], ["hdcms", "开源系统"]];
    
    arr.forEach(([key, value]) => {
      hd.set(key, value);
    });
    console.log(hd);
    ```

- 对于键是对象的`Map`， 键保存的是内存地址，值相同但内存地址不同的视为两个键。

  - ```javascript
    let arr = ["后盾人"];
    const hd = new Map();
    hd.set(arr, "houdunren.com");
    console.log(hd.get(arr)); //houdunren.com
    console.log(hd.get(["后盾人"])); //undefined
    ```

- Map类型的增删改查操作

  - `map.set()`增 、`map.delete()` `map.clear()` 删 、`map.has()` 查

- 遍历 Map 类型数据

  - `for of` + `map.keys()` `map.values()` `map.entries()配合展开语法`
  - `map.forEach((value,key)=>console.log(value,key))`

- Map类型转换操作

  - ```javascript
    let hd = new Map([["中国最好的大学", "北京大学"], ["北京最好的大学", "北京大学"],["新疆最好的大学","石河子大学"]])
    let newArr = [...hd].filter(item =>  item[1].includes("北京大学"))
    console.log(newArr);
    let edu = new Map(newArr)
    console.log(edu);
    console.log(...edu.values())
    ```

- Map类型管理DOM节点

  - 用key 来保存Dom对象，value来保存其他数据

    ```javascript
    const divMap = new Map();
    const divs = document.querySelectorAll("div");
    
    divs.forEach(div => {
        divMap.set(div, {
            content: div.getAttribute("desc")
        });
    });
    divMap.forEach((config, elem) => {
        elem.addEventListener("click", function() {
            alert(divMap.get(this).content);
        });
    });
    ```

  - 实例：当不接受协议时无法提交

    ```javascript
    function post() {
        let map = new Map();
    
        let inputs = document.querySelectorAll("[message]");
        //使用set设置数据
        inputs.forEach(item =>
          map.set(item, {
            message: item.getAttribute("message"),
            status: item.checked
          })
        );
    
        //遍历Map数据
        return [...map].every(([item, config]) => {
          config.status || alert(config.message);
          return config.status;
        });
      }
    ```

    

#### 2.WeakMap类型

- 键名必须是对象，WeaMap对**键名是弱引用**的，**键值是正常引用**
  - 弱引用，则垃圾回收不考虑，不可迭代，没有keys( )，values( )，entries( )等方法和size属性,只有add、delete、has三个方法
  - 当键的外部引用删除时，希望自动删除数据时使用 `WeakMap`

- 选课案例

  ```javascript
  <style>
    * {
      padding: 0;
      margin: 0;
    }
    body {
      padding: 20px;
      width: 100vw;
      display: flex;
      box-sizing: border-box;
    }
    div {
      border: solid 2px #ddd;
      padding: 10px;
      flex: 1;
    }
    div:last-of-type {
      margin-left: -2px;
    }
    ul {
      list-style: none;
      display: flex;
      width: 200px;
      flex-direction: column;
    }
    li {
      height: 30px;
      border: solid 2px #e67e22;
      margin-bottom: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding-left: 10px;
      color: #333;
      transition: 1s;
    }
    a {
      border-radius: 3px;
      width: 20px;
      height: 20px;
      text-decoration: none;
      text-align: center;
      background: #16a085;
      color: white;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 5px;
    }
    .remove {
      border: solid 2px #eee;
      opacity: 0.8;
      color: #eee;
    }
    .remove a {
      background: #eee;
    }
    p {
      margin-top: 20px;
    }
    p span {
      display: inline-block;
      background: #16a085;
      padding: 5px;
      color: white;
      margin-right: 10px;
      border-radius: 5px;
      margin-bottom: 10px;
    }
  </style>
  
  <body>
    <div>
      <ul>
        <li><span>php</span> <a href="javascript:;">+</a></li>
        <li><span>js</span> <a href="javascript:;">+</a></li>
        <li><span>向军讲编程</span><a href="javascript:;">+</a></li>
      </ul>
    </div>
    <div>
      <strong id="count">共选了2门课</strong>
      <p id="lists"></p>
    </div>
  </body>
  
  <script>
    class Lesson {
      constructor() {
        this.lis = document.querySelectorAll("ul>li");
        this.countELem = document.getElementById("count");
        this.listElem = document.getElementById("lists");
        this.map = new WeakMap();
      }
      run() {
        this.lis.forEach(item => {
          item.querySelector("a").addEventListener("click", event => {
            const elem = event.target;
            const state = elem.getAttribute("select");
            if (state) {
              elem.removeAttribute("select");
              this.map.delete(elem.parentElement);
              elem.innerHTML = "+";
              elem.style.backgroundColor = "green";
            } else {
              elem.setAttribute("select", true);
              this.map.set(elem.parentElement, true);
              elem.innerHTML = "-";
              elem.style.backgroundColor = "red";
            }
            this.render();
          });
        });
      }
      count() {
        return [...this.lis].reduce((count, item) => {
          return (count += this.map.has(item) ? 1 : 0);
        }, 0);
      }
      lists() {
        return [...this.lis]
          .filter(item => {
            return this.map.has(item);
          })
          .map(item => {
            return `<span>${item.querySelector("span").innerHTML}</span>`;
          });
      }
      render() {
        this.countELem.innerHTML = `共选了${this.count()}课`;
        this.listElem.innerHTML = this.lists().join("");
      }
    }
    new Lesson().run();
  </script>
  ```

  