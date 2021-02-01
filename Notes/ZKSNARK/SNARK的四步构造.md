# SNARK的四步构造

## 第一步：确定有限域![[公式]](https://www.zhihu.com/equation?tex=%5Cmathbb%7BF%7D)

我们需要**选一个很大很大的数字p，确保这个数字比我们要解决的问题里的所有数字都大**。

有限域就是给我们所有数字加了一个天花板，确定了整个数学系统的计算空间，类似于在电脑里如果我们想创建一个存数字的变量，我们需要思考到底是需要一个`uint32_t`（32位），还是一个`uint64_t`（64位）一样。所有超过有限域的值，都会直接溢出之后再倒回去从0开始。

## 第二步：构建数学运算电路（Arithmetic Circuit）

当我们找到一个数字空间之后，我们下一步要做的事情，就是要把我们想要证明解决的问题**用数学运算电路表示出来**。

什么是数学运算电路呢？我们先来看一看传统的逻辑电路。

![image-20210129091913829](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201102948.png)

**像AND和OR这样的基础逻辑门是逻辑电路的基础模块，通过拼加和堆叠我们可以实现任何复杂逻辑。**

而数字运算电路中，我们**选用“加法门”和“乘法门”作为我们数学运算电路的基础模块**。

![image-20210129092410564](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201103010.png)

上图的数字运算电路即为逻辑电路的转换。

但是在现实的场景中，一个运算门的输入值可能上限非常大，所以我们需要**约束我们运算电路的输入**，使其不仅能够在功能性上和逻辑电路相同，并且在输入取值上只可以取 [0,1] 这两个数字。即**创造出一个多项式，只有当一个输入满足 [0,1] 的取值范围的时候，才会输入0** 。

![[公式]](https://www.zhihu.com/equation?tex=n+%5Cin+%5B0%2C+1%5D+%5Ciff+n+%5Ccdot+%28n-1%29+%3D+0+%5C%5C)

![image-20210129095556772](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201103017.png)

## 第三步：转换为可证明数学运算电路

当我们有了数字运算电路这个概念之后，我们就可以把不同的电路模块拼接起来，生成一个可以用作证明的运算电路出来。

首先，我们先定义一下**可以用作证明的数字运算电路C(x, w)**，具体构造如下：

![image-20210129095941022](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201103026.png)

简单的来说，这个电路C有两组输入。

第一组输入标记为x，我们称之为**公有输入（public input）**，也就是说所有人都会知道x的值。这个x一般来说表达了想要证明的问题的特性和一些固定的环境变量。

第二组输入标记为w，我们称之为**私密输入（secret input）**，或者又称之为witness。这一组数据就是真正提交证明的一方的谜底，只有证明的一方才会知道，其他人是不可以得之的。

当我们有C这一个电路之后，我们的目标就是**证明C(x, w) = 0**。换句话来说，在A和B已知数学运算电路C输出为0，并且公有输入为x的情况下，A需要证明她知道能够构成这个输出的私密输入值w。

![image-20210129100242169](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201103032.png)



如果我们想知道一个数字运算电路C有多难算的时候，我们可以用里面有多少个运算门来描述它的复杂度。一般来说我们会用 ![[公式]](https://www.zhihu.com/equation?tex=%7CC%7C) 来表示。像是上面提到的NAND证明电路，![[公式]](https://www.zhihu.com/equation?tex=%7CC%7C)大概是在10左右。

## 第四步：非交互简短证明体系（SNARK）

首先，我们看看整个非交互式证明体系的**官方定义**。

一个SNARK系统，往往由三个核心算法组成：Setup，Prove和Verify。

1. **生成算法Setup**：![[公式]](https://www.zhihu.com/equation?tex=Setup%28C%29+%5Crightarrow+%28S_p%2C+S_v%29)
   Setup算法会把我们实现确定好的电路C拿进来进行一系列的预处理（preprocessing），然后生成两组参数。![[公式]](https://www.zhihu.com/equation?tex=S_p)是给证明方的参数，![[公式]](https://www.zhihu.com/equation?tex=S_v)是给验证方的。这些参数的用处就是方便双方来生成和验证简短证明。一般来说，生成算法的复杂度和电路C的复杂度是等比例的，![[公式]](https://www.zhihu.com/equation?tex=O%28%7CC%7C%29)。
2. **证明算法Prove**：![[公式]](https://www.zhihu.com/equation?tex=Prove%28S_p%2C+x%2C+w%29+%5Crightarrow+%5Cpi)
   证明算法相比不用多讲了，证明方会用Prove这个算法来生成一个证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)，然后把这个证明发送给验证方。Prove算法再生成证明的时候会用到几乎所有的数据：预处理数据![[公式]](https://www.zhihu.com/equation?tex=S_p)，公有输入x，还有私密输入w。最后生成的证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)的空间复杂度非常简短：![[公式]](https://www.zhihu.com/equation?tex=%7C%5Cpi%7C+%3D+O%28log%7CC%7C%29)。
3. **验证算法Verify**：![[公式]](https://www.zhihu.com/equation?tex=Verify%28S_v%2C+x%2C+%5Cpi%29+%5Crightarrow+Yes%2FNo)
   验证算法也非常的直白，验证方会用Verify这个算法验证我们收到的证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)。这个算法会返回一个1/0的数值，代表验证是否通过。验证的过程中除了需要对方提供的证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)，我们还需要预处理数据![[公式]](https://www.zhihu.com/equation?tex=S_v)，还有公有输入x。验证的复杂度也非常小，一般来说是 ![[公式]](https://www.zhihu.com/equation?tex=O%28%7Cx%7C+%2B+log%7CC%7C%29)。

![image-20210129100834044](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201103039.png)

**证明方提交的这个证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)可以充分说服验证方，让其相信证明方真的有这么一个秘密的w可以满足C(x, w) = 0。**