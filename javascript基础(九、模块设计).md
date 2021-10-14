## javascript基础(九、模块设计)

1. #### 模块化的特点

   - 模块就是一个独立的文件，里面是函数或者类库
   - 虽然JS没有命名空间的概念，使用模块可以解决全局变量冲突
   - 模块需要隐藏内部实现，只对外开发接口
   - 模块可以避免滥用全局变量，造成代码不可控
   - 模块可以被不同的应用使用，提高编码效率

2. #### 实现原理

   - 在过去JS不支持模块时我们使用`AMD/CMD（浏览器端使用）`、`CommonJS（Node.js使用）`、`UMD(两者都支持)`等形式定义模块。

   - AMD代表性的是 `require.js`，CMD 代表是淘宝的 `seaJS` 框架。

   - ```javascript
     let module = (function() {
       //模块列表集合
       const moduleLists = {};
       function define(name, modules, action) {
         modules.map((m, i) => {
           modules[i] = moduleLists[m];
         });
         //执行并保存模块
         moduleLists[name] = action.apply(null, modules);
       }
     
       return { define };
     })();
     
     //声明模块不依赖其它模块
     module.define("hd", [], function() {
       return {
         show() {
           console.log("hd module show");
         }
       };
     });
     
     //声明模块时依赖其它模块
     module.define("xj", ["hd"], function(hd) {
       hd.show();
     });
     ```

3. #### 使用模块化的基础知识

   1. 标签使用
      - 在html文件中导入模块，需要定义属性 `type="module"`
   2. 模块路径
      - 在浏览器中引用模块必须添加路径如`./` ，但在打包工具如`webpack`中则不需要，因为他们有自己的存放方式。
   3. 延迟解析
      - 模块总是会在所有html解析后才执行，下面的模块代码可以看到后加载的 `button` 按钮元素。
      - 建议为用户提供加载动画提示，当模块运行时再去掉动画
   4. 严格模式
      - 模块默认运行在`严格模式`，没有使用声明语句将报错
      - `this`也会是`undefined` 而不是`window`
   5. 作用域
      - 模块都有独立的顶级作用域，未依赖的模块之间不能互相访问
      - 单独文件作用域也是独立的，下面的模块 `1.2.js` 不能访问模块 `1.1.js` 中的数据
   6. 预解析
      - 模块在导入时只执行一次解析，之后的导入不会再执行模块代码，而使用第一次解析结果，并共享数据。
      - 可以在首次导入时完成一些初始化工作
      - 如果模块内有后台请求，也只执行一次即可

4. #### 导入与导出

   - 简单知识
     - 使用`export` 将开发的接口导出
     - 使用`import` 导入模块接口
     - 使用`*`可以导入全部模块接口
     - 导出是以引用方式导出，无论是标量还是对象，即模块内部变量发生变化将影响已经导入的变量
   - 导入模块
     - 具名导入：`import { User, site, func } from "./hd.js";`
     - 批量导入：如果要导入的内容比较多，可以使用 `*` 来批量导入
     - 导入别名：`as`
   - 导出模块
     - `export { site, func, User };`
     - 导出别名：`export { site, func as action, User as user };`
     - 默认导出：(只能一个)使用`default` 定义默认导出的接口，导入时不需要使用 `{}`
     - 混合导出：`export { site, func, User as default };`
     - 导出合并：将多个可导入的模块合并到一个入口文件中

5. #### 按需动态加载模块

   1. 使用 `import` 必须在顶层静态导入模块，而使用`import()` 函数可以动态导入模块，它返回一个 `promise` 对象。

   2. ```javascript
      <button>后盾人</button>
      <script>
        document.querySelector("button").addEventListener("click", () => {
          let hd = import("./hd.js").then(({ site, func }) => {
            console.log(site);
          });
        });
      </script>
      ```

