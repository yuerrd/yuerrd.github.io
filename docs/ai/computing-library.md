# 科学计算库

#### 1. NumPy

 ##### 1. 数组创建

   ```python
   import numpy as np
   
   print(np.ones(shape=10))
   print(np.zeros(shape=10))
   print(np.full(shape=10, fill_value=2))
   print(np.random.randint(0, 100, size=10))
   print(np.random.randn(10))  # 正太分，布平均值0，标准差1
   print(np.linspace(1, 99, 50))  # 等差数列
   print(np.arange(start=0, stop=10, step=2))
   print(np.logspace(1, 10, base=2, num=10))  # 等比数列
   print(np.random.randint(0, 100, size=(3, 3)))  # 二维数组
   ```

##### 2. 数组的保存读取

   1. 保存

      ```python
      import numpy as np
      
      x = np.random.randn(5)
      y = np.arange(0,10,1) 
      #save方法可以存一个
      ndarray np.save("x_arr",x) 
      #如果要存多个数组，要是用savez方法
      np.savez("some_array.npz",xarr = x,yarr=y)
      ```

   2. 读取

      ```python
      import numpy as np
      
      np.load('x_arr.npy')  
      # 通过key加载保存的数组数据 
      np.load('some_array.npz')['yarr']
      ```

   3. 读写csv文件

       ```python
       import numpy as np
       
       arr = np.random.randint(0, 10, size=(3, 3))
       np.savetxt("arr.csv", arr, delimiter=',')
       np.loadtxt("arr.csv", delimiter=',', dtype=np.int32)
       
       ```

3. ##### 数组运算

   1. 加减乘除运算

      ```python
      import numpy as np 
      
      arr1 = np.random.randint(0, 100, size=10)
      arr2 = np.random.randint(0, 100, size=10)
      arr1 - arr2 # 减法
      arr1 * arr2 # 乘法 
      arr1 / arr2 # 除法 
      arr1**arr2  # 幂运算
      ```

   2. 逻辑运算

      ```python
      import numpy as np 
      
      arr1 = np.array([1,2,3,4,5,6,7,8,9,10]) 
      arr2 = np.array([1,3,5,6,2,8,2,10,9,4]) 
      arr1 < 10 
      arr1 >= 10 
      arr1 == 10
      arr1 == arr2 
      arr1 > arr2
      ```

   3. *=、+=、-= 操作

      ```python
      import numpy as np 
      
      arr1 = np.arange(10) 
      arr1 +=10
      arr1 -=10
      arr1 *=10
      # arr1 /=10 不支持运算
      ```

4. ##### 切片索引

   ```python
   import numpy as np 
   
   arr = np.array([0,1,2,3,4,5,6,7,8,9])
   arr[2]   #索引 输出 2 
   arr[1:4] #切片输出:array([1, 2, 3])
   
   arr1 = np.array([[1,2,3],
                    [4,5,6],
                    [7,8,9]])
   arr1[0,-1]  #索引 等于3
   arr1[:2,:2] 
   #[[1 2]
   #	[4 5]]
   
   
   arr1 = np.array([[1, 2, 3],
                    [4, 5, 6],
                    [7, 8, 9]])
   
   index = np.ix_([0, 1], [0, 2])
   arr1[index]
   #[[1 3]
   # [4 6]]
   ```

   

5. ##### 数据形状改变

   1. reshape

       ```python
       import numpy as np
       
       arr1 = np.random.randint(0, 100, size=(3, 4))
       arr2 = arr1.reshape(4, 3)  # 形状改变，返回新数组
       arr3 = arr1.reshape(-1, 6) # 自动整形，自动计算
       
       ```

   2. 叠加

       ```python
       import numpy as np
       
       arr1 = np.array([[1, 2, 3]])
       arr2 = np.array([[4, 5, 6]])
       
       arr3 = np.concatenate([arr1, arr2], axis=0)  # 行合并 默认值
       arr3 = np.vstack((arr1, arr2))
       
       arr4 = np.concatenate([arr1, arr2], axis=1)  # 列合并
       arr4 = np.hstack((arr1, arr2))
       
       ```

   3. 拆分

      ```python
      import numpy as np
      
      arr1 = np.random.randint(0, 10, size=(6, 5)) 
      arr2 = np.split(arr1, indices_or_sections=[2, 3], axis=0)  # 行的拆分
      arr2 = np.hsplit(arr1,indices_or_sections=[2, 3]) #
      arr3 = np.split(arr1, indices_or_sections=[2, 3], axis=1)  # 列的拆分
      np.vsplit(arr,indices_or_sections=3)
      ```

   4. 转置

       ```python
       import numpy as np
       
       arr1 = np.random.randint(0, 10, size=(3, 5))  # shape(3,5)
       arr2 = arr1.T  # shape(5,3)
       
       arr3 = np.random.randint(0, 10, size=(3, 4, 5))  # shape(3,4,5)
       arr4 = np.transpose(arr3, axes=(2, 0, 1))  # shape(5,3,4)
       ```

6. #### 广播机制

   ```python
   import numpy as np
   
   arr1 = np.random.randint(0, 10, size=(3, 5))  # shape(3,5)
   arr2 = np.random.randint(0, 10, 5)
   arr3 = arr1 + arr2 # 广播
   # 打印结果
   #[[2 3 5 4 6]
   # [8 1 3 1 9]
   # [4 0 5 5 2]]
   #----------------------
   #[2 3 3 1 0]
   #----------------------
   #[[ 4  6  8  5  6]
   # [10  4  6  2  9]
   # [ 6  3  8  6  2]]
   
   arr1 = np.random.randint(0, 10, size=(3, 5))  # shape(3,5)
   arr2 = np.random.randint(0, 10, 3).reshape(3, 1)
   arr3 = arr1 + arr2
   # 打印结果
   #[[5 0 2 5 5]
   # [9 0 8 5 9]
   # [5 4 7 1 8]]
   #----------------------
   #[[9]
   # [6]
   # [7]]
   ----------------------
   #[[14  9 11 14 14]
   # [15  6 14 11 15]
   # [12 11 14  8 15]]
   
   ```

#### 2. Pandas

  1. ##### 数据结构

     -  **Series(**一维数据)

       ```python
       import pandas as pd
       import numpy as np
       
       data = np.arange(2, 10, 1)
       
       s1 = pd.Series(data=data)
       s2 = pd.Series(data=data, index=list('abcdefhi'))
       s3 = pd.Series(data={'a': 2, 'b': 3, 'c': 4})
       ```

     -  **DataFrame** (二维数据)

       ```python
       import numpy as np
       import pandas as pd
       
       df = pd.DataFrame(data=np.random.randint(0, 100, size=(10, 2)),
                         columns=['column1', 'column2'])
       ```

  2. ##### 数据的存储

     ```python
     import numpy as np
     import pandas as pd
     
     df = pd.DataFrame(data=np.random.randint(0, 100, size=(10, 2)),
                       columns=['column1', 'column2'])
     # 保存
     df.to_csv('./data.csv', header=True, index=True)
     # 加载
     data = pd.read_csv('./data.csv', header=[0], index_col=0)
     ```

  3. ##### 数据清洗

     ```python
     # 1、重复数据过滤 
     df.duplicated() # 判断是否存在重复数据 
     df.drop_duplicates() # 删除重复数据
     
     # 2、空数据过滤 
     df.isnull() # 判断是否存在空数据，存在返回True，否则返回False 
     df.dropna(how = 'any') # 删除空数据 
     df.fillna(value=-1) # 填充空数据
     
     # 3、指定行或者列过滤 
     del df['color'] # 直接删除某列 d
     f.drop(labels = ['price'],axis = 1)# 删除指定列 
     df.drop(labels = [0,1,5],axis = 0) # 删除指定行
     ```

#### 3. Matplotlib

 1.  图像绘制

    ```python
    import numpy as np
    import matplotlib.pyplot as plt
    
    # 图形绘制
    x = np.linspace(0, 2 * np.pi)  # x轴
    y = np.sin(x)  # y轴
    
    plt.figure(figsize=(9, 6))  # 调整尺寸
    plt.plot(x, y)  # 绘制线形图
    plt.show()
    ```

#### 4. 多元线性方程

 1. 正规方程

    $J(θ)=\frac{1}{2}\sum_{i=0}^n(h_{θ(x_i)}-y_i)^2$

    $J(θ)=\frac{1}{2}\sum_{i=0}^n(h_{θ(x_i)}-y_i)(h_{θ(x_i)}-y_i)$

    $J(θ)=\frac{1}{2}(hθ-y)^T(hθ-y)$

    $W = (X^TX)^{-1}X^Ty$ 

    ```python
    w = np.linalg.inv(X.T.dot(X)).dot(X.T).dot(y)
    ```

    

 2. 





