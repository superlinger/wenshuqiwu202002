![](1!83d9FrJ1uYxKDhT1p0pn2w.jpeg)
# 为什么这对您有用？

好吧，因为我们大多数人都倾向于忘记（对于已经实施了ML算法的人而言），各种库函数最终会使用纯粹的逻辑为预先存在的函数编写代码，这在这种情况下既浪费时间又浪费精力 如果人们了解有效利用图书馆的细微差别，就变得至关重要。 因此，Numpy作为机器学习必不可少的库之一，需要自己撰写一篇文章。
# 谁是本文的读者？

由于了解Numpy是数据预处理的起点，也是后来实施ML算法的起点，因此您可以成为即将在不久的将来学习机器学习或刚刚开始并希望获得更多实践经验的人 ML的Numpy。
# 热门AI文章：

1. 2019年人工智能（AI）的十大趋势

2.打破行话泡沫—深度学习

3.我们如何改善数据质量？

4.使用Python和代码进行逻辑回归的机器学习

但是，在撰写本文时，我的主要重点是使它成为Numpy的快速入门，以供那些有使用该库经验但需要快速回顾的人参考。
# 我们还等什么呢？让我们开始吧！！

undefined
![](1!00pL0zLnfI7y8d5G1aQrHA.jpeg)

此外，Numpy构成了机器学习堆栈的基础。 在本文中，我们介绍了最常用的Numpy操作。
## 1）创建向量

在这里，我们使用Numpy创建一维数组，然后将其称为向量。
```
#Load Libraryimport numpy as np#Create a vector as a Rowvector_row = np.array([1,2,3])#Create vector as a Columnvector_column = np.array([[1],[2],[3]])
```
## 2）建立矩阵

我们在Numpy中创建一个二维数组，并将其称为矩阵。 它包含2行3列。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6]])print(matrix)
```
## 3）创建稀疏矩阵

给定具有很少非零值的数据，您想有效地表示它。

机器学习中的一种常见情况是拥有大量数据。 但是数据中的大多数元素都是零。 例如，假设有一个矩阵，其中的列是Amazon上的所有产品，而行则表示给定的用户是否曾经购买过该商品。 就像您可能已经猜到的那样，到现在为止，甚至还有一次都没有购买过许多产品，因此绝大多数元素都为零。

稀疏矩阵仅存储非零元素，并假定所有其他值将为零，从而节省了大量计算量。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[0,0],[0,1],[3,0]])print(matrix)#Create Compressed Sparse Row(CSR) matrixmatrix_sparse = sparse.csr_matrix(matrix)print(matrix_sparse)
```
## 4）选择元素

当您需要选择向量或矩阵中的一个或多个元素时
```
#Load Libraryimport numpy as np#Create a vector as a Rowvector_row = np.array([ 1,2,3,4,5,6 ])#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Select 3rd element of Vectorprint(vector_row[2])#Select 2nd row 2nd columnprint(matrix[1,1])#Select all elements of a vectorprint(vector_row[:])#Select everything up to and including the 3rd elementprint(vector_row[:3])#Select the everything after the 3rd elementprint(vector_row[3:])#Select the last elementprint(vector[-1])#Select the first 2 rows and all the columns of the matrixprint(matrix[:2,:])#Select all rows and the 2nd column of the matrixprint(matrix[:,1:2])
```
## 5）描述矩阵

当您想了解矩阵的形状大小和尺寸时。
```
import numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])#View the Number of Rows and Columnsprint(matrix.shape)#View the number of elements (rows*columns)print(matrix.size)#View the number of Dimensions(2 in this case)print(matrix.ndim)
```
## 6）将操作应用于元素

您想将某些函数应用于数组中的多个元素。

Numpy的vectorize类将函数转换为可以应用于数组或数组切片中的多个元素的函数。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Create a function that adds 100 to somethingadd_100 =lambda i: i+100#Convert it into a vectorized functionvectorized_add_100= np.vectorize(add_100)#Apply function to all elements in matrixprint(vectorized_add_100(matrix))
```
## 7）找到最大值和最小值

我们使用Numpy的max和min函数：
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Return the max elementprint(np.max(matrix))#Return the min elementprint(np.min(matrix))#To find the max element in each columnprint(np.max(matrix,axis=0))#To find the max element in each rowprint(np.max(matrix,axis=1))
```
## 8）计算平均值，方差和标准偏差

当您要计算有关数组的描述性统计信息时。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Meanprint(np.mean(matrix))#Standard Dev.print(np.std(matrix))#Varianceprint(np.var(matrix))
```
## 9）重塑数组

当您想改变数组的形状（更改行数和列数）而不更改元素时。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Reshapeprint(matrix.reshape(9,1))#Here -1 says as many columns as needed and 1 rowprint(matrix.reshape(1,-1))#If we provide only 1 value Reshape would return a 1-d array of that lengthprint(marix.reshape(9))#We can also use the Flatten method to convert a matrix to 1-d arrayprint(matrix.flatten())
```
## 10）转置向量或矩阵

通过转置，可以交换矩阵的行和列
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Transpose the matrixprint(matrix.T)
```
## 11）查找矩阵的行列式和秩

矩阵的秩是其行或列所跨越的向量空间的维数。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Calculate the Determinantprint(np.linalg.det(matrix))#Calculate the Rankprint(np.linalg.matrix_rank(matrix))
```
## 12）得到矩阵的对角线

当您只需要提取矩阵的对角元素时
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Print the Principal diagonalprint(matrix.diagonal())#Print the diagonal one above the Principal diagonalprint(matrix.diagonal(offset=1))#Print the diagonal one below Principal diagonalprint(matrix.diagonal(offset=-1))
```
## 13）计算矩阵的迹线

矩阵的迹线是矩阵主对角线上元素的总和。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Print the Traceprint(matrix.trace())
```
## 14）查找特征值和特征向量

特征向量在机器学习库中被广泛使用。 直观地给定一个由矩阵A表示的线性变换，特征向量是在应用该变换时仅在比例（而不是方向）上发生变化的向量。

Av = Kv

这里A是一个方矩阵，K包含特征值，v包含特征向量。
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)# Calculate the Eigenvalues and Eigenvectors of that Matrixeigenvalues ,eigenvectors=np.linalg.eig(matrix)print(eigenvalues)print(eigenvectors)
```
## 15）计算点积
```
#Load Libraryimport numpy as np#Create vector-1vector_1 = np.array([ 1,2,3 ])#Create vector-2vector_1 = np.array([ 4,5,6 ])#Calculate Dot Productprint(np.dot(vector_1,vector_2))#Alternatively you can use @ to calculate dot productsprint(vector_1 @ vector_2)
```
## 16）加，减和乘矩阵
```
#Load Libraryimport numpy as np#Create Matrix-1matrix_1 = np.array([[1,2,3],[4,5,6],[7,8,9]])#Create Matrix-2matrix_2 = np.array([[7,8,9],[4,5,6],[1,2,3]])#Add the 2 Matricesprint(np.add(matrix_1,matrix_2))#Subtractionprint(np.subtract(matrix_1,matrix_2))#Multiplication(Element wise, not Dot Product)print(matrix_1*matrix_2)
```
## 17）求逆矩阵

当您要计算平方矩阵的逆时使用
```
#Load Libraryimport numpy as np#Create a Matrixmatrix = np.array([[1,2,3],[4,5,6],[7,8,9]])print(matrix)#Calculate its inverseprint(np.linalg.inv(matrix))
```
## 18）产生随机值

Numpy提供了多种生成随机数的方法。

此外，有时返回相同的随机数以得到可预测的，可重复的结果可能会很有用。 我们可以通过设置伪随机数生成器的“种子”（整数）来实现。 具有相同种子的随机过程将始终产生相同的结果。
```
#Load Libraryimport numpy as np#Set seednp.random.seed(1)#Generate 3 random integers b/w 1 and 10print(np.random.randint(0,11,3))#Draw 3 numbers from a normal distribution with mean 1.0 and std 2.0print(np.random.normal(1.0,2.0,3))
```

因此，这几乎涵盖了您使用Python启动机器学习之旅所需的所有标准Numpy操作。 对于其他人，我希望这对您在该领域中已有的知识有所帮助。
# 别忘了给我们您的👏！
![](0!8YFw0BlqLD9cJm5i)
![](1!2f7OqE2AJK1KSrhkmD9ZMw.png)
![](1!v-PpfkSWHbvlWWamSVHHWg.png)
![](1!Wt2auqISiEAOZxJ-I7brDQ.png)
# Python中机器学习的Numpy基本指南
## ML的精华库！
```
(本文翻译自Siddharth Dixit的文章《An Essential Guide to Numpy for Machine Learning in Python》，参考：https://becominghuman.ai/an-essential-guide-to-numpy-for-machine-learning-in-python-5615e1758301)
```
