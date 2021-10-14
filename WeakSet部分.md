- WeakSet() 
- -->WeakSet结构同样不会存储重复的值，它的成员必须只能是**对象类型**的**值**。
  - 垃圾回收不考虑WeakSet，即被WeakSet引用时引用计数器不+1，所以对象不被引用时不管WeakSet是否在使用都将删除
  - 因为WeakSet 是弱引用，由于其他地方操作成员可能会不存在，所以不可以进行`forEach( )`遍历等操作
  - 也是因为弱引用，WeakSet 结构没有keys( )，values( )，entries( )等方法和size属性,只有add、delete、has三个方法
  - 因为是弱引用所以当外部引用删除时，希望自动删除数据时使用 `WeakSet`

