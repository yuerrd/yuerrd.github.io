# Python-Base

1. #### 运算符

   ```python
   a,b = 10,20
   #交换
   a,b = b,a
   ```

2. #### 数据结构

   - 列表  []
     - 切片[开始位置:结束位置:步长]
   - 字典 {"name":"张三"}  
     - zip([],[])
   - 元组 ()  
     - ('python',)
   - 集合{1,2,3,4,4,6}
     - 不能重复
     - set(rang(6))
     -  集合关系

3. #### 字符串

   - 驻留机制(共享内存)
     - 共享内存
     - 特殊字符没有驻留
     - [-5,256] 整数数字
   - 特殊的字符驻留

4. #### 函数

   - 可变的位置行参

     ```python
     def fun(*args):
       print(args)
     fun(1,2)
     ```

   - 可变的关键字行参

     ```python
     def fun(**args):
       print(args)
     fun(a=1,b=2)
     ```

5. #### 生成器

   保存生产元素的算法，记录游标的位置

   ```python
   g  = (x for x in range(10))
   next(g)
   # 通过函数创建生成器
   yield b
   ```

6. #### 高阶函数

   - ##### 匿名函数

     ```python
     lambda [list] : 表达式
     ```

     ###### 参数详解：

      ```
      [list]:表示参数列表
      表达式 :计算逻辑
      返回值 :返回函数计算的结果 
      ```

     ###### 代码示例:

     ```python
     sum = lambda x,y : x+y
     print(sum(1,2))
     ```

   - ##### map 函数

     ```python
      map(func, *iterables) --> map objec
     ```

     ###### 参数详解：

     ```
     func: 传入参数为函数
     *iterables: 可迭代对象 
     返回值: 返回函数计算的结果 
     ```

     ###### 代码示例:

     ```python
     list1 = [1, 2, 3, 4, 5]
     print(list(map(lambda x: x ** 2, list1)))
     ```

   - ##### reduce函数

     ```python
     from functools import reduce 
     reduce(function, sequence[, initial]) -> value
     ```

     ###### 参数详解:

     ```
     function: 两个参数的函数 
     sequence: 可迭代对象
     initial: 可选，初始参数 
     返回值:   返回函数计算的结果 
     ```

     ###### 代码示例:

     ```python
     from functools import reduce
     
     list1 = [1, 2, 3, 4, 5]
     print(reduce(lambda x, y: x + y, list1))
     ```

   - ##### filter函数

     ```python
     filter(function, iterable)
     ```
     
     ###### 参数详解:
     
     ```
     function：判断函数。
     iterable：可迭代对象。 
     返回值：返回True的元素新列表值
     ```
     
     ###### 代码示例:
     
     ```python
     def not_odd(num):
       return num % 2 == 0
     newlist = filter(not_odd, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
     print(list(newlist)) 
     # 运算结果 [2, 4, 6, 8, 10]
     ```
     
     

7. #### 装饰器

   ###### 代码示例

   ```python
   # 装饰器
   from functools import wraps
   def decor_a(fun):
       @wraps(fun)
       def decor_b():
           print('I am decor_b')
           fun()
           print('I am fun')
       return decor_b
   @decor_a
   def decor_c():
       print('I am decor_c')
   
   decor_c()
   ```

8. #### 类信息

   1. 属性限制

      ```python
      __slots__ = ["name"]
      ```

   2. 枚举类

      ```python
      from enum import Enum Month = Enum('Month', ('Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'))
      ```

   3. 元类

      - type创建一个class

        ```
        type(name, bases, dict) 
        name -- 类的名称。 
        bases -- 基类的元组。
        dict -- 字典，类内定义的命名空间变量。
        ```

        ```python
        if __name__ == '__main__':
            def fn(self, name='world'):
                print(f'Hello,{name}')
        
            Hello = type('Hello', (object,), dict(hello=fn))
            hello = Hello()
            hello.hello()
        ```

      - metaclass创建类模版

        ```python
        class HelloMetaclass(type):
            def __new__(mcs, name, bases, attrs):
                def fn(self, name='world'):
                    print(f'Hello,{name}')
        
                attrs['hello'] = fn
                return type.__new__(mcs, name, bases, attrs)
        
        
        if __name__ == '__main__':
            class Hello(object, metaclass=HelloMetaclass):
                pass
        
        
            hello = Hello()
            hello.hello()
        ```
