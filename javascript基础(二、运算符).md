## javascript基础(二.运算符)

#### 1. 一元运算符

- +、-、*、/ 以及涉及到的隐式转换

#### 2.自增和自减

- a++、++a 的区别：
  - a++先返回值，再自加  --> ++a先自加，再返回值 

#### 3.逻辑运算符

- ！非
  - 非boolean --> 先转换成boolean 再取反
  - 取反两次 == Boolean
  - 
- && 与 
  - --> 短路操作 --> 第一个操作数的值是false时，直接返回第一个操作数的值，不再对第二个操作数进行计算
- || 或
  -  --> 短路操作 -->第一个操作数的值是true时，直接返回返回第一个操作数的值，不再对第二个操作数进行计算
- 非Boolean值的情况 && ||
  - 先转换为Boolean值，再运算
  - && --> 按照短路理解，完成任务-->收工，||同理
    - 如果 true1 && true2 --> 返回true2
    - 如果false1 && false2 -->返回false1

#### 4.赋值运算符

- =、+=、*=、/=、%=

#### 5.关系运算符

- 返回boolean
- 任何值和NaN作比较都是false -->先转化成Number，再比较
- 如果两个字符串比较--> 不转成Number，而是分别比较字符串中字符的Unicode编码，一位一位进行比较-->英文排序
- 因此-->比较字符串时，一定要进行转型

#### 6.相等运算符

- == 与 ===

- 区别

  - == 存在类型转换

    1. 如果x的数据类型和y的数据类型相同，则返回以x===y的结果

    2. null 与undefined -->true

    3. Number 与 String --> toNumber转为Number比

    4. Boolean 与 Number --> 转为Number比

    5. String/Number/Symbol 与 Object --> toPrimitive(y)将Object转为原始类型

       - toNumber -->

         Undefined --> NaN ; Null --> 0 ; Booleam --> 1/0 ; Symbol --> TypeError ;

         String --> 空串为0/数字的正常转,忽略前导0,可以0x十六/其他字符NaN

         Object --> toPrimitive(obj,preferredType) ; 第二个参数为空，则String ;其他情况 Number

         - preferredType = String ; 

         ​	--> 01* 如果obj为原始值，直接返回 --> 02* 否则调用obj.toString()，如果执行结果是原始值，返回之 --> 03* 否则调用 obj.valueOf()，如果执行结果是原始值，返回之 --> 04* 否则抛异常

         - preferredType = Number ; 

           --> 01* 如果obj为原始值，直接返回 --> 02* 否则调用 obj.valueOf()，如果执行结果是原始值，返回之 --> 03* 否则调用obj.toString()，如果执行结果是原始值，返回之 --> 04* 否则抛异常

  - === 必须类型、值都相等才返回true