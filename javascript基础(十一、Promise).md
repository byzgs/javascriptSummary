## javascript基础(十一、Promise)

1. 异步状态

   - 状态说明：Promise包含`pending`、`fulfilled`、`rejected`三种状态

     - `pending` 指初始等待状态，初始化 `promise` 时的状态
     - `resolve` 指已经解决，将 `promise` 状态设置为`fulfilled`
     - `reject` 指拒绝处理，将 `promise` 状态设置为`rejected`
     - `promise` 是生产者，通过 `resolve` 与 `reject` 函数告之结果
     - `promise` 非常适合需要一定执行时间的异步任务
     - 状态一旦改变将不可更改

   - `promise` 创建时即立即执行即同步任务，`then` 会放在异步微任务中执行，需要等同步任务执行后才执行。

   - `promise` 操作都是在其他代码后执行，下面会先输出 `houdunren.com` 再弹出 `success`

     - `promise` 的 then、catch、finally的方法都是异步任务
     - 程序需要将主任务执行完成才会执行异步队列任务

   - 同步任务 --> 微任务(`then`、`catch` 、`finally`) --> 宏任务 (`setTimeout`)

     经典例子：

     ```javascript
     let promise1 = new Promise(resolve => {
           setTimeout(()=> {
             resolve();
             console.log('setTimeout');
           },0)
           console.log('Promise里的同步任务');
         }).then(value => {
           console.log('微任务then');
         })
         console.log('同步任务');
         //输出顺序： 
         //Promise里的同步任务 --> 同步任务 --> setTimeout --> 微任务then
         //原因： 这里的宏任务提升是一种错觉，主要是resolve() 这个微任务的定义，在宏任务里
       
     ```

2. **then**

   - `then` 用于定义当 Promise 状态发生改变时的处理

     - then 方法必须返回 promise，用户返回或系统自动返回
     - 第一个函数在`resolved` 状态时执行，即执行`resolve`时执行`then`第一个函数处理成功状态
     - 第二个函数在`rejected`状态时执行，即执行`reject` 时执行第二个函数处理失败状态，该函数是*可选的*
     - 两个函数都接收 `promise` 传出的值做为参数
     - 也可以使用`catch` 来处理失败的状态
     - 如果 `then` 返回 `promise` ，下一个`then` 会在当前`promise` 状态改变后执行

   - 语法说明

     ```javascript
     promise.then(onFulfilled, onRejected)
     //onFulfilled 或 onRejected 不是函数将被忽略
     ```

   - 基础知识

     - 如果只关心失败时状态，`then` 的第一个参数传递 `null`
     - promise 传向then的传递值，如果then没有可处理函数，会一直向后传递
     
   - 链式调用
   
     - 每次的 `then` 都是一个全新的 `promise`，默认 then 返回的 promise 状态是 fulfilled，不要认为上一个 promise状态会影响以后then返回的状态
   
     - 如果 `then` 返回`promise` 时，后面的`then` 就是对返回的 `promise` 的处理，需要等待该 promise 变更状态后执行。
   
       ```javascript
       new Promise((resolve, reject) => {
         resolve();
       })
       .then(v => {
         return new Promise((resolve, reject) => {
           resolve("第二个promise");
         });
       })
       .then(value => {
         console.log(value);
         return value;
       })
       .then(value => {
         console.log(value);
       });
       ```
   
   - 其他类型
   
     - 循环调用
   
       - 如果 `then` 返回与 `promise` 相同将禁止执行
   
     - promise
   
       - 如果返加值是 `promise` 对象，则需要更新状态后，才可以继承执行后面的`promise`
   
     - Thenables
   
       - 包含 `then` 方法的对象就是一个 `promise` ，系统将传递 resolvePromise 与 rejectPromise 做为函数参数
   
       - 下例中使用 `resolve` 或在`then` 方法中返回了具有 `then`方法的对象
   
         - 该对象即为 `promise` 要先执行，并在方法内部更改状态
   
         - 如果不更改状态，后面的 `then` promise都为等待状态
   
         - ```javascript
           new Promise((resolve, reject) => {
             resolve({
               then(resolve, reject) {
                 resolve("解决状态");
               }
             });
           })
           .then(v => {
             console.log(`fulfilled: ${v}`);
             return {
               then(resolve, reject) {
                 setTimeout(() => {
                   reject("失败状态");
                 }, 2000);
               }
             };
           })
           .then(null, error => {
             console.log(`rejected: ${error}`);
           ```
   
       - 包含 `then` 方法的对象可以当作 promise来使用
   
         - ```javascript
           class User {
             constructor(id) {
               this.id = id;
             }
             then(resolve, reject) {
               resolve(ajax(`http://localhost:8888/php/houdunren.php?id=${this.id}`));
             }
           }
           ```
   
       - 如果对象中的 `then` 不是函数，则将对象做为值传递
   
3. catch

   - catch用于失败状态的处理函数，等同于 `then(null,reject){}`

     - `catch` 可以捕获之前所有 `promise` 的错误，建议使用 `catch` 处理错误

     - 错误的处理：冒泡形式，一直往下找处理,找到错误处理环节为止 --> 将 `catch` 放在最后面用于统一处理前面发生的错误(包含`then`中的未定义错误、主动`throw` 的错误)

     - 但像下面的在异步中 `throw` 将不会触发 `catch`，而使用系统错误处理

       ```javascript
       const promise = new Promise((resolve, reject) => {
         setTimeout(() => {
           throw new Error("fail");	//这里可以用reject来达到抛给catch的目的
         }, 2000);
       }).catch(msg => {
         console.log(msg + "后盾人");
       })
       ```

   - 定制错误

     - 可以根据不同的错误类型进行定制操作，下面将参数错误与404错误分别进行了处理

       ```javascript
       class ParamError extends Error {
         constructor(msg) {
           super(msg);
           this.name = "ParamError";
         }
       }
       class HttpError extends Error {
         constructor(msg) {
           super(msg);
           this.name = "HttpError";
         }
       }
       function ajax(url) {
         return new Promise((resolve, reject) => {
           if (!/^http/.test(url)) {
             throw new ParamError("请求地址格式错误");
           }
           let xhr = new XMLHttpRequest();
           xhr.open("GET", url);
           xhr.send();
           xhr.onload = function() {
             if (this.status == 200) {
               resolve(JSON.parse(this.response));
             } else if (this.status == 404) {
               // throw new HttpError("用户不存在");
               reject(new HttpError("用户不存在"));
             } else {
               reject("加载失败");
             }
           };
           xhr.onerror = function() {
             reject(this);
           };
         });
       }
       
       ajax(`http://localhost:8888/php/user.php?name=后盾人`)
       .then(value => {
         console.log(value);
       })
       .catch(error => {
         if (error instanceof ParamError) {
           console.log(error.message);
         }
         if (error instanceof HttpError) {
           alert(error.message);
         }
         console.log(error);
       });
       ```

   - 事件处理

     - **unhandledrejection**事件用于捕获到未处理的Promise错误，下面的 then 产生了错误，但没有`catch` 处理，这时就会触发事件。~~该事件有可能在以后被废除~~，处理方式是对没有处理的错误直接终止。

       ```javascript
       window.addEventListener("unhandledrejection", function(event) {
         console.log(event.promise); // 产生错误的promise对象
         console.log(event.reason); // Promise的reason
       });
       
       new Promise((resolve, reject) => {
         resolve("success");
       }).then(msg => {
         throw new Error("fail");
       });
       ```

4. finally

   - 无论状态是`resolve` 或 `reject` 都会执行此动作，`finally` 与状态无关。
   - 

