## javascript基础(十、正则表达式)

1. 正则表达式是什么
   - 正则表达式是用于匹配字符串中字符组合的模式，在 JavaScript中，正则表达式也是对象。
   
2. 创建正则表达式的方式
   - 字面量创建
   
     - 使用`//`包裹，推荐，但它不能在其中使用变量
   
     - `/u/.test(hd)`
   
     - ```javascript
       //下面尝试使用 a 变量时将不可以查询
       let hd = "houdunren.com";
       let a = "u";
       console.log(/a/.test(hd)); //false
       ```
   
     - 可以使用 `eval` 转换为`js`语法来实现将变量解析到正则中，但是比较麻烦
   
     - ```javascript
       let hd = "buptzhanggenshuo"
       let a = "bupt"
       console.log(eval(`/${a}/`)).test(hd) //true
       ```
   
   - 对象创建
   
     - 当正则需要`动态创建`时使用对象方式 `new RegExp()`
   
     - ```javascript
       let hd = "buptzhanggenshuo"
       let a = "bupt"
       let reg = new RegExp(a)
       console.log(reg.test(hd)) //true
       ```
   
3. 特殊符号

   - `|` 选择符，左右两侧有一个匹配到就可以。

   - `\` 转义符，例如 https://  需要转为 --> ` https:\/\/`  

   - 使用 `RegExp` 构建正则时在转义上会有些区别

     - ```javascript
       let price = 12.23;
       //含义1: . 除换行外任何字符 	含义2: .普通点
       //含义1: d 字母d   			含义2: \d 数字 0~9
       console.log(/\d+\.\d+/.test(price));
       
       //字符串中 \d 与 d 是一样的，所以在 new RegExp 时\d 即为 d
       console.log("\d" == "d");
       
       //使用对象定义正则时，可以先把字符串打印一样，结果是字面量一样的定义就对了
       console.log("\\d+\\.\\d+");
       let reg = new RegExp("\\d+\\.\\d+");
       console.log(reg.test(price));
       ```

   - 字符边界

     - `^` --> 匹配字符串的开始 ：匹配内容必须以`www`开始 `console.log(/^www/.test(hd));`
     - `&` --> 匹配字符串的结束 ：匹配内容必须以`.com`结束 `console.log(/\.com$/.test(hd)); `

4. 元子字符

   - 字符列表

     | 元字符 | 说明                                                 | 示例          |
     | ------ | ---------------------------------------------------- | ------------- |
     | \d     | 匹配任意一个数字                                     | [0-9]         |
     | \D     | 与除了数字以外的任何一个字符匹配                     | [^0-9]        |
     | \w     | 与任意一个英文字母,数字或下划线匹配                  | [a-zA-Z_]     |
     | \W     | 除了字母,数字或下划线外与任何字符匹配                | [^a-zA-Z_]    |
     | \s     | 任意一个空白字符匹配，如空格，制表符`\t`，换行符`\n` | [\n\f\r\t\v]  |
     | \S     | 除了空白符外任意一个字符匹配                         | [^\n\f\r\t\v] |
     | .      | 匹配除换行符外的任意字符                             |               |

   - 使用`/s`视为单行模式（忽略换行）时，`.` 可以匹配所有 
     - `let res = hd.match(/.+/s);`
   - 所有字符
     - 可以使用 `[\s\S]` 或 `[\d\D]` 来匹配所有字符

5. 模式修饰

   - | 修饰符 | 说明                                         |
     | ------ | -------------------------------------------- |
     | i      | 不区分大小写字母的匹配                       |
     | g      | 全局搜索所有匹配内容                         |
     | m      | 视为多行                                     |
     | s      | 视为单行忽略换行符，使用`.` 可以匹配所有字符 |
     | y      | 从 `regexp.lastIndex` 开始匹配               |
     | u      | 正确处理四个字符的 UTF-16 编码               |
   
     - 每个字符都有属性，如`L`属性表示是字母，`P` 表示标点符号，需要结合 `u` 模式才有效。其他属性简写可以访问[属性的别名](https://www.unicode.org/Public/UCD/latest/ucd/PropertyValueAliases.txt) 查看
   
     ```javascript
     //使用\p{L}属性匹配字母
     let hd = "houdunren2010.不断发布教程，加油！";
     console.log(hd.match(/\p{L}+/u));
     
     //使用\p{P}属性匹配标点
     console.log(hd.match(/\p{P}+/gu))
     ```
   
     - 字符也有unicode文字系统属性 `Script=文字系统`，下面是使用 `\p{sc=Han}` 获取中文字符 `han`为中文系统，其他语言请查看: [文字语言表](http://www.unicode.org/standard/supported.html) 
   
     - ```javascript
       let hd = `
       张三:010-99999999,李四:020-88888888`;
       let res = hd.match(/\p{sc=Han}+/gu);
       console.log(res);
       ```
   
   - `lastIndex` 
   
     - RegExp对象`lastIndex` 属性可以返回或者设置正则表达式开始匹配的位置
   
       - 必须结合 `g` 修饰符使用
       - 对 `exec` 方法有效
       - 匹配完成时，`lastIndex` 会被重置为0
   
     - ```javascript
       let hd = `后盾人不断分享视频教程，后盾人网址是 houdunren.com`;
       let reg = /后盾人(.{2})/g;
       reg.lastIndex = 10; //从索引10开始搜索
       console.log(reg.exec(hd));
       console.log(reg.lastIndex);
       
       reg = /\p{sc=Han}/gu;
       while ((res = reg.exec(hd))) {
         console.log(res[0]);
       }
       ```
   
6. 原子表

   - 只匹配一个，`[]+` 这样用可以匹配多个

   - | 原子表 | **说明**                           |
     | :----- | :--------------------------------- |
     | []     | 只匹配其中的一个原子               |
     | [^]    | 只匹配"除了"其中字符的任意一个原子 |
     | [0-9]  | 匹配0-9任何一个数字                |
     | [a-z]  | 匹配小写a-z任何一个字母            |
     | [A-Z]  | 匹配大写A-Z任何一个字母            |

   - 实例

     - 日期的匹配

       ```javascript
       let tel = "2022-02-23";
       console.log(tel.match(/\d{4}([-\/])\d{2}\1\d{2}/)); // \1 --> 用原子表1
       ```

7.  原子组

   - 如果一次要匹配多个元子，可以通过元子组完成

   - 原子组与原子表的差别在于原子组一次匹配**多个**元子，而原子表则是匹配任意**一个**字符

   - 元字符组用 `()` 包裹

   - 基本使用：

     - | 变量    | 说明             |
       | ------- | ---------------- |
       | 0       | 匹配到的完整内容 |
       | 1,2.... | 匹配到的原子组   |
       | index   | 原字符串中的位置 |
       | input   | 原字符串         |
       | groups  | 命名分组         |

     - 在`match`中使用原子组匹配，会将每个组数据返回到结果中
     
   - 引用分组
   
     - `\n` 在匹配时引用原子组， `$n` 指在替换时使用匹配的组数据
   
     - 下面将标签替换为`p`标签
   
     - ```javascript
       let hd = `
         <h1>houdunren</h1>
         <span>后盾人</span>
         <h2>hdcms</h2>
       `;
       
       let reg = /<(h[1-6])>([\s\S]*)<\/\1>/gi;
       console.log(hd.replace(reg, `<p>$2</p>`));
       ```
   
     - 如果只希望组参与匹配，便不希望返回到结果中使用 `(?:` 处理。下面是获取所有域名的示例
   
     - ```javascript
       let hd = `
         https://www.houdunren.com
         http://houdunwang.com
         https://hdcms.com
       `;
       
       let reg = /https?:\/\/((?:\w+\.)?\w+\.(?:com|org|cn))/gi;
       while ((v = reg.exec(hd))) {
         console.dir(v);
       }
       ```
   
     - 分组别名
   
   - 重复匹配
   
     - | 符号  | 说明             |
       | ----- | ---------------- |
       | *     | 重复零次或更多次 |
       | +     | 重复一次或更多次 |
       | ?     | 重复零次或一次   |
       | {n}   | 重复n次          |
       | {n,}  | 重复n次或更多次  |
       | {n,m} | 重复n到m次       |
   
     - 
