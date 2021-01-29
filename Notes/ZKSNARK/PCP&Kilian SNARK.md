# PCP 与 Kilian SNARK

## PCP 

**PCP，全称Probabilistically Checkable Proof**，意思就是，**所有的NP问题，都可以在多项式时间内通过概率验证的方法被证明**。

什么叫概率验证呢？其实我们可以把它理解为**随机抽查**：如果A有一个很复杂的问题的解，但是这个解很长很长，那么B就可以通过随机抽查这个解的某几位来大概确定A的解是否准确。**因为A不知道B会抽查哪一位，所以A没有任何有利的方法作弊**，B每抽查验证成功一次，A的解是错误的解的可能性就下降一次。

因为**所有的NP问题都可以有效地转换为数学运算电路**，PCP定理指出，对于所有的电路![[公式]](https://www.zhihu.com/equation?tex=C)，我们都可以构造一套概率验证体系![[公式]](https://www.zhihu.com/equation?tex=%28S%2C+P%2C+V%29)，其工作方式如下：

![[公式]](https://www.zhihu.com/equation?tex=%5Cforall+C%3A+%5Cexists+%5Ctext%7B+Proof+System+%7D+%28S%2C+P%2C+V%29%3A+%5C%5C+S%28C%29+%5Crightarrow+%28C%2C+S_v%29+%5C%5C)

生成算法Setup还是和以前一样，把电路![[公式]](https://www.zhihu.com/equation?tex=C)转换成后续会用到的随机参数。

![img](https://pic3.zhimg.com/80/v2-266dc0e8b93d9aa8ce2b3e7d5174d94e_1440w.jpg)

**验证方并不能看到所有的![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)，只能通过一个query机制来检查这个证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)随机的![[公式]](https://www.zhihu.com/equation?tex=k)位数**。通过看完这![[公式]](https://www.zhihu.com/equation?tex=k)位数之后，验证方就需要输出验证结果。

这套随机抽查的系统也需要满足两个要求：

1. **完整性（Completeness）**：就像之前提到的一样，如果证明方是诚实的，那么![[公式]](https://www.zhihu.com/equation?tex=C%28x%2C+w%29+%3D+0)，所以验证方一定会验证通过。
2. **健全性（Soundness）**：如果证明方不诚实，作假自己的答案，那么也就是说![[公式]](https://www.zhihu.com/equation?tex=%5Cnexists+w%3AC%28x%2C+w%29+%3D+0)， 验证方验证通过的可能性会小于一个由安全指数![[公式]](https://www.zhihu.com/equation?tex=k)决定的概率区间：![[公式]](https://www.zhihu.com/equation?tex=Pr%5BV%3DYes%5D+%5Cle+%28%5Cfrac%7B1%7D%7B2%7D%29%5E%7Bk%7D)。

## 从经典PCP到Kilian SNARK

但是我们会发现，**简单的PCP系统很难满足简短证明的要求**。

**难以避免这个庞大的证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)可以储存在哪里的问题**。

Kilian SNARK其实就是对PCP证明系统做出了一个小小的改进。与其我们把证明全部导出来让A后续的时候无法篡改，我们可以使用**Merkle承诺和Merkle证明**的方式来确保A不会篡改她自己的数据。这样一来，我们就不需要一个中间方来存储A的大量证明数据了。

![img](https://pic2.zhimg.com/80/v2-1a60d49c1f7302bfe0241cf94a31b7f9_1440w.jpg)

改进过的Kilian SNARK的具体证明步骤如下：

1. 首先，证明方生成一系列需要的证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)，并且向验证方提供![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)的Merkle承诺![[公式]](https://www.zhihu.com/equation?tex=C_%5Cpi)。
2. 随后，验证方随机的抽查证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)其中的某几项。
3. 证明方向验证方展示抽查的那几项的值，并且**附加一个Merkle证明来证明这几个值真的是在原有的证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)当中**。验证方通过对比Merkle证明得到的Merkle承诺是否与最早的时候得到的承诺相等来确保证明方没有作假。
4. 同样的协议进行![[公式]](https://www.zhihu.com/equation?tex=k)轮之后，验证方输出验证结果，结束证明。

**Kilian SNARK通过使用Merkle树的结构，巧妙的把证明的大小从原有的![[公式]](https://www.zhihu.com/equation?tex=O%28%7CC%7C%29)降低到了![[公式]](https://www.zhihu.com/equation?tex=O%28log%7CC%7C%29)。**

## 从交互式到非交互式（Fiat-Shamir Heuristic）

此时我们的证明系统仍然是交互式的，在证明的过程中，一直需要验证方在线，用于提供随机抽查的随机参数。

**Fiat-Shamir Heuristic**是一个1988年被提出的算法，**可以把任何交互式的随机验证协议(Public-coin Protocol)转换为非交互式的协议**。

首先，我们需要随便定义一个交互式的随机验证协议![[公式]](https://www.zhihu.com/equation?tex=I)：

![img](https://pic4.zhimg.com/80/v2-c844d33c4fd307bbf4781f93cf35878b_1440w.jpg)

图上表示的是非常笼统的交互式证明![[公式]](https://www.zhihu.com/equation?tex=I)，**这个流程图同样也适配于我们之前讨论的交互式Kilian SNARK**。总的来说就是证明方会根据验证方提供的随机字串![[公式]](https://www.zhihu.com/equation?tex=rand_i)来返回对应的消息![[公式]](https://www.zhihu.com/equation?tex=m_i)。

接下来，我们用Fiat-Shamir Heuristic把![[公式]](https://www.zhihu.com/equation?tex=I)转换成非交互协议。

首先，我们需要一个**安全的哈希函数![[公式]](https://www.zhihu.com/equation?tex=H)（cryptographic hash function）**。我们同时也需要假设这一类哈希函数是一个**随机预言机**，也就是说，无论输入的是什么，输出的值我们都可以看作是一个和输入没有关联的随机数。

当我们有了这个假设之后，我们就可以把交互式协议![[公式]](https://www.zhihu.com/equation?tex=I)压缩成非交互式协议![[公式]](https://www.zhihu.com/equation?tex=I%27)了：

1. 首先，证明方需要生成消息![[公式]](https://www.zhihu.com/equation?tex=m_1)。然后根据![[公式]](https://www.zhihu.com/equation?tex=m_1)的值来生成原本由验证方来生成的![[公式]](https://www.zhihu.com/equation?tex=rand_1+%5Cleftarrow+H%28x%2C+m_1%29)。
2. 随后，证明方根据![[公式]](https://www.zhihu.com/equation?tex=rand_1)的值来生成消息![[公式]](https://www.zhihu.com/equation?tex=m_2)，然后同理得到![[公式]](https://www.zhihu.com/equation?tex=rand_2+%5Cleftarrow+H%28x%2C+m_1%2C+m_2%29)。
3. 最后，证明方生成![[公式]](https://www.zhihu.com/equation?tex=m_3)，并且把![[公式]](https://www.zhihu.com/equation?tex=%5Cpi+%3D+%28m_1%2C+m_2%2C+m_3%29)一并发给验证方。

当验证方收到证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)之后，他**只需要根据这几个数字和哈希函数![[公式]](https://www.zhihu.com/equation?tex=H)重新生成![[公式]](https://www.zhihu.com/equation?tex=rand_1%2C+rand_2)就可以验证证明是否准确了。**

通过巧妙运用哈希函数，我们把整个协议变成了非交互式的：证明方可以一口气生成所有的内容，发给验证方之后就可以走人了。验证方则可以在任何时候打开证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)，计算出随机数![[公式]](https://www.zhihu.com/equation?tex=rand_1%2C+rand_2)并且验证证明。**这样一来，证明方甚至可以把证明直接发到网上，然后验证方可以定期下载这些证明并且验证它们。**

到这里， 我们基本上就看到**Kilian SNARK的全貌**了：

首先，证明方向验证方展示自己证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)的Merkle承诺，然后根据这一系列输出的哈希值来得到一个随机数![[公式]](https://www.zhihu.com/equation?tex=rand_1)。随后证明方揭露![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)在![[公式]](https://www.zhihu.com/equation?tex=rand_1)位置上的值，并且提供对应的Merkle证明。然后再根据前一步得到的值来计算出新的哈希值![[公式]](https://www.zhihu.com/equation?tex=rand_2)，然后以此类推，揭露在![[公式]](https://www.zhihu.com/equation?tex=rand_2)上的值。如此往复这个过程，直到揭露![[公式]](https://www.zhihu.com/equation?tex=k)个位置结束。

当整个过程结束之后，证明方只需要把每个对应位置的值和Merkle证明发给验证方，验证方就可以通过![[公式]](https://www.zhihu.com/equation?tex=H)来重新构造出随机数，并且验证证明了！

## 从PCP到LPCP

纯粹依赖于PCP猜数字抽查的Kilian SNARK其实**效率非常低**，生成一个问题的证明，可能就要花费半天时间。这对于我们在网络上的应用场景来说非常不现实。为了更加高效率的生成简短证明，我们要对PCP问题进行**进一步的约束**。

如果说PCP形容的是所有NP范围内的问题都可以通过简短的随机抽验来验证，LPCP则是说**任意一个![[公式]](https://www.zhihu.com/equation?tex=d)阶的多项式![[公式]](https://www.zhihu.com/equation?tex=P)，都可以通过随机验证多项式在几个点上取值来确定这个多项式的每一项系数是否满足特定的要求**。

![[公式]](https://www.zhihu.com/equation?tex=P%28x%29+%3D+c_0+%2B+c_1+%5Ccdot+x+%2B+c_2+%5Ccdot+x%5E2+%2B+...+%2B+c_k+%5Ccdot+x%5Ed+%5C%5C)

假如取值在![[公式]](https://www.zhihu.com/equation?tex=%5Cmathbb%7BF%7D)域内的多项式![[公式]](https://www.zhihu.com/equation?tex=P%28x%29)拥有![[公式]](https://www.zhihu.com/equation?tex=%28c_0%2C+c_1%2C+...+c_d%29)的系数，那么这个![[公式]](https://www.zhihu.com/equation?tex=d)阶多项式**在每个点上的取值一定是特殊的**。换句话说，如果我们还有一个![[公式]](https://www.zhihu.com/equation?tex=d)阶的多项式![[公式]](https://www.zhihu.com/equation?tex=Q%28x%29)，哪怕![[公式]](https://www.zhihu.com/equation?tex=Q)的系数稍微变了一点点，整个多项式的取值马上会发生巨大的改变。**如果两个多项式![[公式]](https://www.zhihu.com/equation?tex=P%2C+Q)的系数不一样，那么如果我们随机验证一个点![[公式]](https://www.zhihu.com/equation?tex=r)的取值![[公式]](https://www.zhihu.com/equation?tex=P%28r%29%2C+Q%28r%29)，这两个值相等的几率会非常非常小**：

![[公式]](https://www.zhihu.com/equation?tex=P+%5Cne+Q%2C+r+%5Cstackrel%7BR%7D%7B%5Cleftarrow%7D+%5Cmathbb%7BF%7D+%3A+Pr%5BP%28r%29+%3D+Q%28r%29%5D+%5Cle+%5Cfrac%7Bd%7D%7B%7C%5Cmathbb%7BF%7D%7C%7D+%5C%5C)

精确的来说的话，**两个![[公式]](https://www.zhihu.com/equation?tex=d)阶的不同系数的多项式，在所有整数范围![[公式]](https://www.zhihu.com/equation?tex=%5Cmathbb%7BF%7D)内，最多也只会有![[公式]](https://www.zhihu.com/equation?tex=d)个点重合**，其他的点因为系数不同，是不会重合的。

如果我们把这101个数**不是放在一个数组里让B抽查，而是作为一个100阶多项式![[公式]](https://www.zhihu.com/equation?tex=P)的系数![[公式]](https://www.zhihu.com/equation?tex=%28c_0%2C+...+c_%7B101%7D%29)**，那么B只需要检查这个多项式在任意随机数![[公式]](https://www.zhihu.com/equation?tex=r)上的取值![[公式]](https://www.zhihu.com/equation?tex=P%28r%29)，就可以马上知道A是不是真的拥有这101个数字。

![[公式]](https://www.zhihu.com/equation?tex=P%28x%29+%3D+c_0+%2B+c_1+%5Ccdot+x+%2B+...+%2B+c_d+%5Ccdot+x%5Ed+%5C%5C+Q%28x%29+%3D+0+%2B+1+%5Ccdot+x+%2B+...+%2B+100+%5Ccdot+x%5Ed+%5C%5C+P%28r%29+%3D+Q%28r%29+%3A+Pr%5BP+%3D+Q%5D+%5Cge+1+-+%5Cfrac%7Bd%7D%7B%7C%5Cmathbb%7BF%7D%7C%7D+%5C%5C)

我们可以看一下上面的公式，A把她的101个数字组成了多项式![[公式]](https://www.zhihu.com/equation?tex=P)，B在自己心中根据自己期待的检查结果（0到100升序）组成了多项式![[公式]](https://www.zhihu.com/equation?tex=Q)。**此时如果B随机抽查了一个点![[公式]](https://www.zhihu.com/equation?tex=r)并且发现![[公式]](https://www.zhihu.com/equation?tex=P%28r%29+%3D+Q%28r%29)，那么B基本上已经可以完全相信P和Q是相等的，也就代表了A的101个数字和B想要的数字是相同的了**。

这种通过多项式系数来检查数字关系的方法效率很高，就仿佛像我们不是一个一个数字抽查，而是一次性的抽查了所有数字之间的关系一样。**通过简短的几次随机取值验证，验证方就可以立刻验证证明方是否真的拥有私密的![[公式]](https://www.zhihu.com/equation?tex=w)，证明方作假的几率也几乎为零**。

## 高效率简短证明体系（Efficient SNARK）

当我们知道**LPCP可以使用多项式来大大提高随机抽查验证的效率**之后，我们就可以开始搭建简短证明体系了。整个证明体系将会由以下几步来完成：

1. 将数学运算电路![[公式]](https://www.zhihu.com/equation?tex=C)转化为到R1CS程序矩阵。
2. 从R1CS程序矩阵还原成多项式。
3. 用多项式表达式定义LPCP。
4. 从多项式LPCP到可信初始化（trusted setup）的简短零知识证明（zkSNARK）。

### 从电路到R1CS

一般来说，电路所代表的逻辑关系非常复杂，是很难直接推导出多项式来的。

我们往往需要借助一个中间媒介，把电路先转换为一组矩阵关系。这种矩阵关系，我们称之为**R1CS程序矩阵**。

R1CS，全名为**Rank-1 Constraint System**，其实讲白了就是三个矩阵![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C+%5Cin+%5Cmathbb%7BF%7D%5E%7Bm+%5Ctimes+%28n%2B1%29%7D)，这三个矩阵之间可以找到一组解![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)，从而满足![[公式]](https://www.zhihu.com/equation?tex=A+%5Ccdot+%5Cvec%7Bz%7D)和![[公式]](https://www.zhihu.com/equation?tex=B+%5Ccdot+%5Cvec%7Bz%7D)的逐项积等于![[公式]](https://www.zhihu.com/equation?tex=C+%5Ccdot+%5Cvec%7Bz%7D)。

![[公式]](https://www.zhihu.com/equation?tex=%5Cexists+%5Cvec%7Bz%7D+%3A+%28A+%5Ccdot+%5Cvec%7Bz%7D%29+%5Ccirc+%28B+%5Ccdot+%5Cvec%7Bz%7D%29+%3D+C+%5Ccdot+%5Cvec%7Bz%7D+%5C%5C)

**逐项积**的意思就是把两个一样大小的矩阵![[公式]](https://www.zhihu.com/equation?tex=A%2C+B)的每个元素乘起来，最后得到一个同样大小的矩阵![[公式]](https://www.zhihu.com/equation?tex=C)。

![[公式]](https://www.zhihu.com/equation?tex=A+%3D+%5Cbegin%7Bbmatrix%7Da_%7B11%7D+%26+a_%7B12%7D+%5C%5C+a_%7B21%7D+%26+a_%7B22%7D+%5C%5C+a_%7B31%7D+%26+a_%7B32%7D%5Cend%7Bbmatrix%7D%2C+%5C++B+%3D+%5Cbegin%7Bbmatrix%7Db_%7B11%7D+%26+b_%7B12%7D+%5C%5C+b_%7B21%7D+%26+b_%7B22%7D+%5C%5C+b_%7B31%7D+%26+b_%7B32%7D%5Cend%7Bbmatrix%7D%2C+%5C+C+%3D+A+%5Ccirc+B+%3D+%5Cbegin%7Bbmatrix%7Da_%7B11%7D+%5Ccdot+b_%7B11%7D+%26+a_%7B12%7D+%5Ccdot+b_%7B12%7D+%5C%5C+a_%7B21%7D+%5Ccdot+b_%7B21%7D+%26+a_%7B22%7D+%5Ccdot+b_%7B22%7D+%5C%5C+a_%7B31%7D+%5Ccdot+b_%7B31%7D+%26+a_%7B32%7D+%5Ccdot+b_%7B32%7D%5Cend%7Bbmatrix%7D+%5C%5C)

在我们想要证明的电路问题![[公式]](https://www.zhihu.com/equation?tex=C)里，我们可以**把电路每个逻辑门之间的关系用矩阵![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C)来表达，然后把电路的公有与私密输入![[公式]](https://www.zhihu.com/equation?tex=x%2C+w)用![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D+%3D+%5Cbegin%7Bbmatrix%7D1%5C%5Cx%5C%5Cw%5Cend%7Bbmatrix%7D)来表达**。这样一来，只要R1CS矩阵![[公式]](https://www.zhihu.com/equation?tex=A%2CB%2CC)之间的逐项积关系成立，那么也就代表了这个电路的输入合法，即![[公式]](https://www.zhihu.com/equation?tex=C%28x%2C+w%29+%3D+0)。

举个例子。

既然讲到了区间证明，我们现在来尝试把一个简单的区间证明电路![[公式]](https://www.zhihu.com/equation?tex=C)转换为R1CS矩阵![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C)。为了方便在文章中表述，我们假设要**证明一下某个电路的私密输入![[公式]](https://www.zhihu.com/equation?tex=w)的值在0到15之间**。这样的话，我们的数学运算电路如下：

![[公式]](https://www.zhihu.com/equation?tex=2%5E0+%5Ccdot+w_0+%2B+2%5E1+%5Ccdot+w_1+%2B+2%5E2+%5Ccdot+w_2+%2B+2%5E3+%5Ccdot+w_3+-+w+%3D+0+%5C%5C+w_0+%5Ccdot+%28w_0+-+1%29+%3D+0+%5C%5C+w_1+%5Ccdot+%28w_1+-+1%29+%3D+0+%5C%5C+w_2+%5Ccdot+%28w_2+-+1%29+%3D+0+%5C%5C+w_3+%5Ccdot+%28w_3+-+1%29+%3D+0+%5C%5C)

具体电路的构造原理我们在上一篇文章中有详细的介绍。大概来说就是**我们先把私密输入![[公式]](https://www.zhihu.com/equation?tex=w)拆分为![[公式]](https://www.zhihu.com/equation?tex=w_0%2C+...%2C+w_3)的四个二进制位，然后分别约束这四位的取值只能是0或1。**这里我们就来尝试把这一组逻辑表达式转化为R1CS程序矩阵。首先我们先根据电路的要求，找到公有输入![[公式]](https://www.zhihu.com/equation?tex=x)和私密输入![[公式]](https://www.zhihu.com/equation?tex=w)分别是哪些。因为这个问题不涉及到任何公有输入，所以我们的解![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)就是：

![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D+%3D++%5Cbegin%7Bbmatrix%7D+1+%5C%5C+w+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D+%5C%5C)

然后，我们根据之前得到的5个表达式，可以依次转换为5行R1CS矩阵：

![[公式]](https://www.zhihu.com/equation?tex=A+%3D++%5Cbegin%7Bbmatrix%7D+0+%26+-1+%26+1+%26+2+%26+4+%26+8+%5C%5C+0+%26+0+%26+1+%26+0+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+1+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+1+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+0+%26+1+%5Cend%7Bbmatrix%7D%2C+%5C++B+%3D++%5Cbegin%7Bbmatrix%7D+1+%26+0+%26+0+%26+0+%26+0+%26+0+%5C%5C+-1+%26+0+%26+1+%26+0+%26+0+%26+0+%5C%5C+-1+%26+0+%26+0+%26+1+%26+0+%26+0+%5C%5C+-1+%26+0+%26+0+%26+0+%26+1+%26+0+%5C%5C+-1+%26+0+%26+0+%26+0+%26+0+%26+1+%5Cend%7Bbmatrix%7D%2C+%5C++C+%3D++%5Cbegin%7Bbmatrix%7D+0+%26+0+%26+0+%26+0+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+0+%26+0+%5C%5C+%5Cend%7Bbmatrix%7D+%5C%5C)

我们把![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)代入之后，发现![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C)三个矩阵之间的逐项积正好还原了我们之前所推导出来的5条逻辑电路关系。也就是说，如果![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)满足了R1CS程序，那也代表了![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)当中的私密输入![[公式]](https://www.zhihu.com/equation?tex=w)满足了数学运算电路![[公式]](https://www.zhihu.com/equation?tex=C)。

![[公式]](https://www.zhihu.com/equation?tex=%28A+%5Ccdot+%5Cbegin%7Bbmatrix%7D+1+%5C%5C+w+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D%29+%5Ccirc++%28B+%5Ccdot+%5Cbegin%7Bbmatrix%7D+1+%5C%5C+w+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D%29+%3D+C+%5Ccdot+%5Cbegin%7Bbmatrix%7D+1+%5C%5C+w+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D+%5Ciff+%5Cmathbb%7BC%7D%28x%2C+w%29+%3D+0+%5C%5C)

这样一来，我们就得到了可以构成R1CS程序矩阵的![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C)了。得到R1CS的三个矩阵之后，我们不妨先观察一下矩阵的大小。

我们会发现，**矩阵的长和辅助变量的数量![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)中的辅助变量数量有关**。因为最开始的输入通过数学运算门之后我们需要用辅助变量把这些中间值存起来，所以说辅助变量越多，电路里的数学运算门也越多。然而，比较有趣的是，**矩阵的高其实只和电路中的乘法门的数量有关**。乘法做的越多，矩阵就有越多行。原因其实很简单，我们如果把![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C)的关系每一行拆开来看，我们可以发现其实每一行都表达了![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)中的两组元素的线性组合相乘等于另一个![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)中的元素的线性组合。这也就是说加法门表达的相加的关系可以压缩起来，然而乘法门代表的相乘的关系则每一个都要占据一行。我们把矩阵的每一行称之为**约束（Constraint）**。越复杂的电路，所需要的约束也越多。

熟悉**布尔可满足性问题**的朋友可能会对约束这个概念比较熟悉。只是在布尔问题当中，每一条约束都是逻辑表达式，而这里的约束则是数学表达式。如果我们找到了一组值![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)使得R1CS的关系被满足了，那也就代表![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)真的就是R1CS矩阵里描述的问题的解。

### 从R1CS到多项式

**多项式插值问题（Polynomial Interpolation）**。这个问题是这样的：给定任意一个![[公式]](https://www.zhihu.com/equation?tex=d)阶的多项式：

![[公式]](https://www.zhihu.com/equation?tex=f%28x%29+%3D+f_0+%2B+f_1+%5Ccdot+x+%2B+f_2+%5Ccdot+x%5E2+%2B+...+%2B+f_d+%5Ccdot+x%5Ed+%5C%5C)

假如我们不知道这个多项式的系数![[公式]](https://www.zhihu.com/equation?tex=f_0%2C+f_1%2C+...%2C+f_d)是什么，但是我们可以获得这个多项式![[公式]](https://www.zhihu.com/equation?tex=f)在随机选择的几个点上的取值![[公式]](https://www.zhihu.com/equation?tex=f%28a_0%29%2C+f%28a_1%29%2C+...+%2C+f%28a_d%29)，多项式插值定理证明了**如果我们获得了![[公式]](https://www.zhihu.com/equation?tex=d%2B1)个不重复的取值，那么可以直接还原出整个多项式![[公式]](https://www.zhihu.com/equation?tex=f)的所有系数。**

还是用上面的例子。

首先，我们观察发现，R1CS矩阵表达式中，**![[公式]](https://www.zhihu.com/equation?tex=%28A+%5Ccdot+%5Cvec%7Bz%7D%29)得到的矩阵的大小正好是5x1**：

![[公式]](https://www.zhihu.com/equation?tex=A+%5Ccdot+%5Cvec%7Bz%7D+%3D++%5Cbegin%7Bbmatrix%7D+0+%26+-1+%26+1+%26+2+%26+4+%26+8+%5C%5C+0+%26+0+%26+1+%26+0+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+1+%26+0+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+1+%26+0+%5C%5C+0+%26+0+%26+0+%26+0+%26+0+%26+1+%5Cend%7Bbmatrix%7D+%5Ccdot+%5Cbegin%7Bbmatrix%7D+1+%5C%5C+w+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D+%3D+%5Cbegin%7Bbmatrix%7D+-w+%2B+w_0+%2B+2w_1+%2B+4w_2+%2B+8w_3+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D+%5C%5C)

既然如此，那么我们可以**把这个5x1的矩阵作为一个多项式![[公式]](https://www.zhihu.com/equation?tex=P)在5个点上的取值结果**。我们假设有一个4阶的多项式![[公式]](https://www.zhihu.com/equation?tex=P)，然后这个多项式在从1到5的5个点上取值的时候，恰巧满足：

![[公式]](https://www.zhihu.com/equation?tex=%5Cexists+P%28x%29+%3D+f_0+%2B+f_1+%5Ccdot+x+%2B+f_2+%5Ccdot+x%5E2+%2B+f_3+%5Ccdot+x%5E3+%2B+f_4+%5Ccdot+x%5E4+%3A+%5C%5C+P%281%29+%3D+-w+%2B+w_0+%2B+2w_1+%2B+4w_2+%2B+8w_3+%5C%5C+P%282%29+%3D+w_0+%5C%5C+P%283%29+%3D+w_1+%5C%5C+P%284%29+%3D+w_2+%5C%5C+P%285%29+%3D+w_3+%5C%5C)

如果我们计算出对应的范德蒙逆矩阵的话，我们就可以**反推出这个多项式![[公式]](https://www.zhihu.com/equation?tex=P)的每一项系数**：

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bbmatrix%7D+1+%26+1+%26+1%5E2+%26+1%5E3+%26+1%5E4+%26+1%5E5+%5C%5C+1+%26+2+%26+2%5E2+%26+2%5E3+%26+2%5E4+%26+2%5E5+%5C%5C+1+%26+3+%26+3%5E2+%26+3%5E3+%26+3%5E4+%26+3%5E5+%5C%5C+1+%26+4+%26+4%5E2+%26+4%5E3+%26+4%5E4+%26+4%5E5+%5C%5C+1+%26+5+%26+5%5E2+%26+5%5E3+%26+5%5E4+%26+5%5E5+%5Cend%7Bbmatrix%7D%5E%7B-1%7D+%5Ctimes+%5Cbegin%7Bbmatrix%7D+-w+%2B+w_0+%2B+2w_1+%2B+4w_2+%2B+8w_3+%5C%5C+w_0+%5C%5C+w_1+%5C%5C+w_2+%5C%5C+w_3+%5Cend%7Bbmatrix%7D+%3D+%5Cbegin%7Bbmatrix%7D+f_0+%5C%5C+f_1+%5C%5C+f_2+%5C%5C+f_3+%5C%5C+f_4+%5Cend%7Bbmatrix%7D+%5C%5C)

**得到系数之后，我们也就还原了多项式![[公式]](https://www.zhihu.com/equation?tex=P)**。同理可得，我们也可以用![[公式]](https://www.zhihu.com/equation?tex=B)矩阵来还原出多项式![[公式]](https://www.zhihu.com/equation?tex=Q)：

![[公式]](https://www.zhihu.com/equation?tex=%5Cexists+Q%28x%29+%3D+g_0+%2B+g_1+%5Ccdot+x+%2B+g_2+%5Ccdot+x%5E2+%2B+g_3+%5Ccdot+x%5E3+%2B+g_4+%5Ccdot+x%5E4+%3A+%5C%5C+Q%281%29+%3D+1+%5C%5C+Q%282%29+%3D+w_0+-+1+%5C%5C+Q%283%29+%3D+w_1+-+1+%5C%5C+Q%284%29+%3D+w_2+-+1+%5C%5C+Q%285%29+%3D+w_3+-+1+%5C%5C+%5C+%5C%5C+%5Cbegin%7Bbmatrix%7D+1+%26+1+%26+1%5E2+%26+1%5E3+%26+1%5E4+%26+1%5E5+%5C%5C+1+%26+2+%26+2%5E2+%26+2%5E3+%26+2%5E4+%26+2%5E5+%5C%5C+1+%26+3+%26+3%5E2+%26+3%5E3+%26+3%5E4+%26+3%5E5+%5C%5C+1+%26+4+%26+4%5E2+%26+4%5E3+%26+4%5E4+%26+4%5E5+%5C%5C+1+%26+5+%26+5%5E2+%26+5%5E3+%26+5%5E4+%26+5%5E5+%5Cend%7Bbmatrix%7D%5E%7B-1%7D+%5Ctimes+%5Cbegin%7Bbmatrix%7D+1+%5C%5C+w_0+-+1+%5C%5C+w_1+-+1+%5C%5C+w_2+-+1+%5C%5C+w_3+-+1+%5Cend%7Bbmatrix%7D+%3D+%5Cbegin%7Bbmatrix%7D+g_0+%5C%5C+g_1+%5C%5C+g_2+%5C%5C+g_3+%5C%5C+g_4+%5Cend%7Bbmatrix%7D+%5C%5C)

还原出![[公式]](https://www.zhihu.com/equation?tex=P%2C+Q)之后，我们再接再厉，用![[公式]](https://www.zhihu.com/equation?tex=C)矩阵还原出多项式![[公式]](https://www.zhihu.com/equation?tex=R)。

![[公式]](https://www.zhihu.com/equation?tex=R)和前两个不同的地方在于，因为![[公式]](https://www.zhihu.com/equation?tex=C+%5Ccdot+%5Cvec%7Bz%7D)的结果是![[公式]](https://www.zhihu.com/equation?tex=A%2C+B)两个矩阵的结果相乘得到的，所以如果![[公式]](https://www.zhihu.com/equation?tex=A)和![[公式]](https://www.zhihu.com/equation?tex=B)还原出了一个4阶的多项式的话，那么![[公式]](https://www.zhihu.com/equation?tex=C)由于是多项式相乘，所以**应当可以还原出一个8阶的多项式**。

如果需要成功还原8阶的多项式那么需要9个取值点，但是我们只能从![[公式]](https://www.zhihu.com/equation?tex=C)矩阵中得到5个取值。解决的方法其实很简单，我们只需要先得到![[公式]](https://www.zhihu.com/equation?tex=P)和![[公式]](https://www.zhihu.com/equation?tex=Q)之后，再在![[公式]](https://www.zhihu.com/equation?tex=R)的范德蒙矩阵后面不够的地方手动多填上几个用![[公式]](https://www.zhihu.com/equation?tex=P)和![[公式]](https://www.zhihu.com/equation?tex=Q)算出来的数字就行了。

![[公式]](https://www.zhihu.com/equation?tex=%5Cexists+R%28x%29+%3D+h_0+%2B+h_1+%5Ccdot+x+%2B+h_2+%5Ccdot+x%5E2+%2B+%5Cdots+%2B+h_8+%5Ccdot+x%5E8+%3A+%5C%5C+Q%281%29+%3D+0+%5C%5C+Q%282%29+%3D+0+%5C%5C+Q%283%29+%3D+0+%5C%5C+Q%284%29+%3D+0+%5C%5C+Q%285%29+%3D+0+%5C%5C+Q%286%29+%3D+P%286%29+%5Ccdot+Q%286%29+%5C%5C+Q%287%29+%3D+P%287%29+%5Ccdot+Q%287%29+%5C%5C+Q%288%29+%3D+P%288%29+%5Ccdot+Q%288%29+%5C%5C+Q%289%29+%3D+P%289%29+%5Ccdot+Q%289%29+%5C%5C+%5C+%5C%5C+%5Cbegin%7Bbmatrix%7D+1+%26+1+%26+1%5E2+%26+1%5E3+%26+1%5E4+%26+1%5E5+%5C%5C+1+%26+2+%26+2%5E2+%26+2%5E3+%26+2%5E4+%26+2%5E5+%5C%5C+1+%26+3+%26+3%5E2+%26+3%5E3+%26+3%5E4+%26+3%5E5+%5C%5C+%5Cvdots+%26+%5Cvdots+%26+%5Cvdots+%26+%5Cvdots+%26+%5Cddots+%26+%5Cvdots+%5C%5C+1+%26+9+%26+9%5E2+%26+9%5E3+%26+9%5E4+%26+9%5E5+%5Cend%7Bbmatrix%7D%5E%7B-1%7D+%5Ctimes+%5Cbegin%7Bbmatrix%7D+0+%5C%5C+0+%5C%5C+0+%5C%5C+0+%5C%5C+0+%5C%5C+P%286%29+%5Ccdot+Q%286%29+%5C%5C+P%287%29+%5Ccdot+Q%287%29+%5C%5C+P%288%29+%5Ccdot+Q%288%29+%5C%5C+P%289%29+%5Ccdot+Q%289%29+%5Cend%7Bbmatrix%7D+%3D+%5Cbegin%7Bbmatrix%7D+h_0+%5C%5C+h_1+%5C%5C+h_2+%5C%5C+%5Cvdots+%5C%5C+h_8+%5Cend%7Bbmatrix%7D+%5C%5C)

当我们得到![[公式]](https://www.zhihu.com/equation?tex=P%2C+Q%2C+R)三个多项式之后，只要R1CS给我们的约束正确，并且私密输入矢量![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)也是正确的解，那么我们就可以保证![[公式]](https://www.zhihu.com/equation?tex=P+%5Ctimes+Q+%3D+R)。如果我们想单独验证R1CS中的第一条约束（![[公式]](https://www.zhihu.com/equation?tex=w)由![[公式]](https://www.zhihu.com/equation?tex=w_0%2C+w_1%2C+w_2%2C+w_3)组成），我们只需要看![[公式]](https://www.zhihu.com/equation?tex=P%281%29+%5Ccdot+Q%281%29)是否等于![[公式]](https://www.zhihu.com/equation?tex=R%281%29)，如果他们想等，那就代表这一组多项式满足了R1CS的第一条约束。同理，我们也可以代入2看第二条约束，以此类推。

到这一步的时候，相信很多朋友已经能隐约感觉到**距离之前说过的LPCP非常接近了**。如果证明方已经拥有了一个合法的私密输入![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D)，并且根据这个私密输入和事先约定好的电路![[公式]](https://www.zhihu.com/equation?tex=C)（即R1CS矩阵）成功还原出了多项式![[公式]](https://www.zhihu.com/equation?tex=P%2C+Q%2C+R)，这个时候，验证方需要做的事情，无疑就是**随机的让证明方验证![[公式]](https://www.zhihu.com/equation?tex=P%2C+Q%2C+R)上的一个取值![[公式]](https://www.zhihu.com/equation?tex=r)，然后判断![[公式]](https://www.zhihu.com/equation?tex=P%28r%29+%5Ccdot+Q%28r%29+%5Cstackrel%7B%3F%7D%7B%3D%7D+R%28r%29)。如果判断成立，那就代表证明方拥有的私密输入是合法的了。**

当然，完整的LPCP协议并没有那么简单，我们还需要定义很多证明方和验证方要做的步骤，然后通过query的形式让验证方逐步看到多项式![[公式]](https://www.zhihu.com/equation?tex=P%2C+Q%2C+R)在某些点![[公式]](https://www.zhihu.com/equation?tex=r)上的取值。

在实际应用当中，因为大型电路（比如SHA256）的约束非常之多，我们往往不会使用范德蒙逆矩阵来还原多项式，因为计算一个几万行的逆矩阵会非常缓慢。一般来说，我们会用**快速傅立叶变换（FFT）**之类的动态算法来快速的找到一个可以代表约束关系的多项式。

**总结一下整个从 R1CS 到多项式的转换过程。**

1. 首先，我们使用![[公式]](https://www.zhihu.com/equation?tex=m%2C+n)来定义R1CS的三个矩阵![[公式]](https://www.zhihu.com/equation?tex=A%2C+B%2C+C+%5Cin+%5Cmathbb%7BF%7D%5E%7Bm+%5Ctimes+%28n%2B1%29%7D)的维度。对于R1CS程序对应的公有和私密输入，我们使用![[公式]](https://www.zhihu.com/equation?tex=%5Cvec%7Bz%7D+%3D+%5Cbegin%7Bbmatrix%7D1%5C%5Cx%5C%5Cw%5Cend%7Bbmatrix%7D+%5Cin+%5Cmathbb%7BF%7D%5E%7Bn+%2B+1%7D)来表达。这里![[公式]](https://www.zhihu.com/equation?tex=m)代表的就是R1CS电路中有多少条约束（constraint），而![[公式]](https://www.zhihu.com/equation?tex=n)代表了一共有多少个输入变量（![[公式]](https://www.zhihu.com/equation?tex=x+%2B+w)）。因为我们还需要用到常数1，所以矩阵的维度是![[公式]](https://www.zhihu.com/equation?tex=m+%5Ctimes+%28n+%2B+1%29)。
2. 随后，我们将![[公式]](https://www.zhihu.com/equation?tex=A)**还原为多项式**![[公式]](https://www.zhihu.com/equation?tex=f%28x%29)。就像前一篇所述，我们找到多项式![[公式]](https://www.zhihu.com/equation?tex=f%28x%29)的多个取值点：![[公式]](https://www.zhihu.com/equation?tex=f%28i%29+%3D+%28A+%5Ccdot+%5Cvec%7Bz%7D%29_i+%5Ctext%7B+for+%7D+i+%3D+1%2C+...%2C+m)。我们一共会得到![[公式]](https://www.zhihu.com/equation?tex=m)个取值点，然后就可以使用**范德蒙反矩阵**把这些取值点还原成一个![[公式]](https://www.zhihu.com/equation?tex=m-1)阶的多项式。
3. 对于![[公式]](https://www.zhihu.com/equation?tex=B)，我们也进行同样的操作，还原![[公式]](https://www.zhihu.com/equation?tex=g%28x%29)。
4. 最后对于![[公式]](https://www.zhihu.com/equation?tex=C)，我们首先设定![[公式]](https://www.zhihu.com/equation?tex=h%28i%29+%3D+%28C+%5Ccdot+%5Cvec%7Bz%7D%29_i+%5Ctext%7B+for+%7D+i+%3D+1%2C+...%2C+m)，然后由于![[公式]](https://www.zhihu.com/equation?tex=h)的阶度会是![[公式]](https://www.zhihu.com/equation?tex=f%2C+g)的阶度之和（即![[公式]](https://www.zhihu.com/equation?tex=2m+-+2)），我们需要**额外定义![[公式]](https://www.zhihu.com/equation?tex=m+-+1)个点的取值**（这样我们就有![[公式]](https://www.zhihu.com/equation?tex=2m+-+1)个取值点，可以还原![[公式]](https://www.zhihu.com/equation?tex=2m+-+2)阶的多项式）。我们额外设定![[公式]](https://www.zhihu.com/equation?tex=h%28i%29+%3D+f%28i%29+%5Ccdot+g%28i%29+%5Ctext%7B+for+%7D+i+%3D+m%2B1%2C+...%2C+2m-1)，随后就可以还原出多项式![[公式]](https://www.zhihu.com/equation?tex=h%28x%29)。

**下面我们来介绍多项式转化为LPCP的过程。**

我们也可以用一样的**矩阵相乘**来表达一个多项式![[公式]](https://www.zhihu.com/equation?tex=f)在![[公式]](https://www.zhihu.com/equation?tex=r)这个点上的取值：

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Balign%2A%7D+f%28r%29+%26%3D+%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7Bm-1%7D+%5Cend%7Bbmatrix%7D+%5Cbegin%7Bbmatrix%7D+f_0+%5C%5C+f_1+%5C%5C+%5Cvdots+%5C%5C+f_%7Bm-1%7D+%5Cend%7Bbmatrix%7D%5C%5C+%26%3D++%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7Bm-1%7D+%5Cend%7Bbmatrix%7D+V%5E%7B-1%7D+%5Cbegin%7Bbmatrix%7D+f%280%29+%5C%5C+f%281%29%5C%5C+%5Cvdots%5C%5C+f%28m-1%29+%5Cend%7Bbmatrix%7D+%5C%5C+%26%3D+%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7Bm-1%7D+%5Cend%7Bbmatrix%7D+V%5E%7B-1%7D+%28A+%5Ccdot+%5Cvec%7Bz%7D%29+%5Cend%7Balign%2A%7D+%5C%5C)

一旦转换成最后形态的表达式之后，我们马上可以发现，由于![[公式]](https://www.zhihu.com/equation?tex=V)与R1CS矩阵![[公式]](https://www.zhihu.com/equation?tex=A)是证明方和验证方都公开的内容，![[公式]](https://www.zhihu.com/equation?tex=r)是验证方选择的随机抽验的取值点，所以我们可以把整个表达式分成**两个部分**：

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Balign%2A%7D+f%28r%29+%26%3D+%28%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7Bm-1%7D+%5Cend%7Bbmatrix%7D+V%5E%7B-1%7D+A%29+%5Ccdot+%5Cvec%7Bz%7D+%3D+%5Clangle+q_1%2C+z+%5Crangle%5C%5C+q_1+%26%3D+%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7Bm-1%7D+%5Cend%7Bbmatrix%7D+V%5E%7B-1%7D+A+%5Cend%7Balign%2A%7D+%5C%5C)

左边部分我们用一个向量![[公式]](https://www.zhihu.com/equation?tex=q)来表示，代表证明方想要验证的**query**。右边部分就是包含了证明电路中公有和私密输入的矩阵![[公式]](https://www.zhihu.com/equation?tex=z)。对于多项式![[公式]](https://www.zhihu.com/equation?tex=g)，我们也如法炮制，得到另一对**内积组合**![[公式]](https://www.zhihu.com/equation?tex=%5Clangle+q_2%2C+z+%5Crangle)。

最后对于多项式![[公式]](https://www.zhihu.com/equation?tex=h)，我们进行类似的操作。为了让整体的维度保持一致，我们需要**额外加上一组单位矩阵**：

![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Balign%2A%7D+h%28r%29+%26%3D+%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7B2m-1%7D+%5Cend%7Bbmatrix%7D+V%5E%7B-1%7D+%5Cbegin%7Bbmatrix%7D+C%5C%5C+I_%7Bm-1%7D+%5Cend%7Bbmatrix%7D+%5Ccdot++%5Cbegin%7Bbmatrix%7D+%5Cvec%7Bz%7D%5C%5C+h%28m%2B1%29%5C%5C+%5Cvdots%5C%5C+h%282m-1%29+%5Cend%7Bbmatrix%7D+%3D+%5Clangle+q_3%2C+%5Bz%2C+h%28m%2B1%29%2C+...%2C+h%282m-1%29%5D+%5Crangle%5C%5C+q_3+%26%3D+%5Cbegin%7Bbmatrix%7D+1+%26+r%5E2+%26+...+%26+r%5E%7B2m-1%7D+%5Cend%7Bbmatrix%7D+V%5E%7B-1%7D+%5Cbegin%7Bbmatrix%7D+C%5C%5C+I_%7Bm-1%7D+%5Cend%7Bbmatrix%7D+%5Cend%7Balign%2A%7D+%5C%5C)

具体的数学公式看似比较复杂，但是其实理解起来很简单：我们只需要把三个多项式![[公式]](https://www.zhihu.com/equation?tex=f%2C+g%2C+h)在![[公式]](https://www.zhihu.com/equation?tex=r)上的取值转换为了两个向量的内积![[公式]](https://www.zhihu.com/equation?tex=%5Clangle+q_i%2C+z+%5Crangle)。

如果我们细心观察的话， **我们发现![[公式]](https://www.zhihu.com/equation?tex=q_1%2C+q_2%2C+q_3)这三个向量可以由验证方生成，而证明方只需要准备好电路的输入![[公式]](https://www.zhihu.com/equation?tex=z)就好了**。这样一来，我们**变相的把所有的计算难度放在了验证方的一侧**，而证明方则不需要太多的运算。

当我们成功的把多项式随机取值问题分解为三个query向量![[公式]](https://www.zhihu.com/equation?tex=q_1%2C+q_2%2C+q_3)之后，我们就可以**正式地进入真正的LPCP验证协议了**。

1. 首先，**证明方Prover**事先计算好证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi+%3D+%5Bw%2C+h%28m%2B1%29%2C+...%2C+h%282m-1%29%5D)，并且把证明保存起来，不许修改它。
2. **验证方Verifier**随机的抽选一个验证点![[公式]](https://www.zhihu.com/equation?tex=r+%5Cstackrel%7BR%7D%7B%5Cleftarrow%7D+%5Cmathbb%7BF%7D)，并且根据![[公式]](https://www.zhihu.com/equation?tex=r)的取值计算得到三个query的向量![[公式]](https://www.zhihu.com/equation?tex=q_1%2C+q_2%2C+q_3)。
3. 这三个Query需要和我们原本的输入向量![[公式]](https://www.zhihu.com/equation?tex=z+%3D+%5Cbegin%7Bbmatrix%7D1%5C%5C+x%5C%5C+w+%5Cend%7Bbmatrix%7D)相乘。我们观察这个向量之后发现，验证方事先已经可以得知向量的上半部分![[公式]](https://www.zhihu.com/equation?tex=%5Cbegin%7Bbmatrix%7D1%5C%5C+x%5Cend%7Bbmatrix%7D)。所以验证方可以把Query向量![[公式]](https://www.zhihu.com/equation?tex=q_i)进行切割，变成![[公式]](https://www.zhihu.com/equation?tex=%5Bq_i%5EL%2C+q_i%5ER%5D)两部分。这样的话，原本的计算也可以一分为二，然后把两部分的内积分别对叠起来：

![[公式]](https://www.zhihu.com/equation?tex=%5Clangle+q_i%2C+z+%5Crangle+%3D+%5Cbegin%7Bbmatrix%7D+%5Clangle+q_i%5EL%2C+%5Cbegin%7Bbmatrix%7D1%5C%5Cx%5Cend%7Bbmatrix%7D+%5Crangle%5C%5C+%5Clangle+q_i%5ER%2C+%5Cbegin%7Bbmatrix%7Dw%5Cend%7Bbmatrix%7D+%5Crangle%5Cend%7Bbmatrix%7D+%5C%5C)

1. 通过这一步切割，我们发现对于![[公式]](https://www.zhihu.com/equation?tex=q_i%5EL)部分的计算，验证方**已经全部知道了**，所以我们可以**进一步的优化证明方需要做的计算**，只把![[公式]](https://www.zhihu.com/equation?tex=q_i%5ER)发送给证明方，让证明方仅仅与私密输入![[公式]](https://www.zhihu.com/equation?tex=w)相乘。
2. 综上所述，验证方现在可以发送![[公式]](https://www.zhihu.com/equation?tex=q_1%5ER%2C+q_2%5ER%2C+q_3%5ER)三个query向量给证明方。
3. 证明方收到query向量之后，**只需要把每个值和自己的证明向量![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)相乘**。

![[公式]](https://www.zhihu.com/equation?tex=a+%3D+%5Clangle+%5Bq_1%5ER+0%5E%7Bm-1%7D%5D%2C+%5Cpi+%5Crangle%5C%5C+b+%3D+%5Clangle+%5Bq_2%5ER+0%5E%7Bm-1%7D%5D%2C+%5Cpi+%5Crangle%5C%5C+c+%3D+%5Clangle+q_3%5ER%2C+%5Cpi+%5Crangle%5C%5C+%5C%5C)

由于证明向量![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)后面还带了多项式![[公式]](https://www.zhihu.com/equation?tex=h)的额外![[公式]](https://www.zhihu.com/equation?tex=m-1)个取值点，所以我们需要在![[公式]](https://www.zhihu.com/equation?tex=q_1%5ER%2C+q_2%5ER)的背后**补上一个空白矩阵**，才可以适配矩阵相乘的维度。

随后，**证明方把![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)三个值发回给验证方**。

1. 最后，验证方只需要**把左侧query的内积补上**，最后检查等式是否相等：

![[公式]](https://www.zhihu.com/equation?tex=%28%5Clangle+q_1%5EL%2C+%5B1%2C+x%5D+%5Crangle+%2B+a%29+%5Ccdot+%28%5Clangle+q_2%5EL%2C+%5B1%2C+x%5D+%5Crangle+%2B+b%29+%5Cstackrel%7B%3F%7D%7B%3D%7D+%28%5Clangle+q_3%5EL%2C+%5B1%2C+x%5D+%5Crangle+%2B+c%29+%5C%5C)

组合起来之后，这也就是变相**检查等式![[公式]](https://www.zhihu.com/equation?tex=%5Clangle+q_1%2C+z+%5Crangle+%5Ccdot+%5Clangle+q_2%2C+z+%5Crangle+%5Cstackrel%7B%3F%7D%7B%3D%7D+%5Clangle+q_3%2C+z+%5Crangle)是否相等**。

如果得到的![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)所组成的等式的确相等，那么就代表![[公式]](https://www.zhihu.com/equation?tex=h%28r%29+%3D+f%28r%29+%5Ccdot+g%28r%29)。根据我们上文讨论的关系，这也就直接代表了![[公式]](https://www.zhihu.com/equation?tex=%28A+%5Ccdot+%5Cvec%7Bz%7D%29+%5Ccirc+%28B+%5Ccdot+%5Cvec%7Bz%7D%29+%3D+C+%5Ccdot+%5Cvec%7Bz%7D)！

这样一来，我们就得到了**完全版的LPCP协议**啦。通过抽查三个多项式在某个点上的随机取值，我们达到了简短证明的目的。只要证明方与验证方严格遵守上述描写的协议，那么就可以有效的通过**简短交互**来验证一个**R1CS矩阵所对应的多项式是否满足我们想要的关系**。

## 从LPCP到SNARK w/ Trusted Setup

我们会发现LPCP是一个交互式协议。这也就是说，证明方首先需要生成一个证明字串![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)，然后保证不会去改动这个字串（可以通过承诺来实现）。随后，验证方会随机抽选三个Query：![[公式]](https://www.zhihu.com/equation?tex=q_1%5ER%2C+q_2%5ER%2C+q_3%5ER)发送给证明方，随后证明方需要诚实的返回对应的乘积![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)三个值进行验证。

下一步，我们需要把LPCP协议进一步压缩成一个**简短无交互式的协议**。

我们可以把Fiat-Shamir运用在我们的LPCP协议里，构建如下的非交互式系统：

1. 证明方事先准备好证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)。在后面的过程中，![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)始终不能被改变。
2. 随后，证明方使用证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)为输入计算哈希函数![[公式]](https://www.zhihu.com/equation?tex=H%28%5Cpi%29)，并且根据结果得到Query ![[公式]](https://www.zhihu.com/equation?tex=q_1%2C+q_2%2C+q_3)。
3. 根据Query的值，证明方计算![[公式]](https://www.zhihu.com/equation?tex=%5Clangle+q_1%2C+%5Cpi+%5Crangle%2C+%5Clangle+q_2%2C+%5Cpi+%5Crangle%2C+%5Clangle+q_3%2C+%5Cpi+%5Crangle)，即![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)。
4. 最后，证明方把![[公式]](https://www.zhihu.com/equation?tex=%5Cpi%2C+H%28%5Cpi%29%2C+a%2C+b%2C+c)一并提交给验证方进行验证。
5. 验证方收到证明之后，只需要重新自己进行一遍计算，就可以确认证明是否正确了。

这样一来，看似我们把LPCP做成了非交互式的样子，但是随之而来的，是**更大的问题**。

首先，证明方需要把整个证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)全部发送给验证方，因为验证方随后要重复进行一模一样的计算。这一步就已经打破了我们之前的简短性要求，因为![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)的长度取决于![[公式]](https://www.zhihu.com/equation?tex=w)的大小以及多项式![[公式]](https://www.zhihu.com/equation?tex=h)的阶度，而这两个值恰好和电路的大小是成正比的。所以这样一来我们**通讯的成本就和电路大小挂钩**了，这和我们一开始想要达到的并不符合。

第二个问题在于**零知识**。虽然我们还没有说到零知识的部分，但是我们可以发现，如果证明方把![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)全部发送给验证方，那么证明方所有的私密输入![[公式]](https://www.zhihu.com/equation?tex=w)已经全部暴露无疑了。验证方其实并不想要看到这么多信息，只需要看到![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)三个乘积就够了。但是为了验证Fiat-Shamir变换是正确的，又不得不看到产生随机Query的哈希函数的输入，即证明![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)。

这么看来，在**随机预言机模型（Random Oracle Model）**下，使用Fiat-Shamir变换对于我们的LPCP协议并没有太大帮助，无法达到我们想要的目的。这个时候该怎么办呢？

其实，为了避免交互，zkSNARK使用了密码学中另有的一个假设：**公共参考字串（CRS）**。

**公共参考字串（CRS）**其实是一个相比起随机预言机更为简单的假设，具体是这样的：

> 我们假设在一个协议中，证明方与验证方都**拥有一段相同的参考字串**。这个字串可能是随机生成的，也可能是某个函数的输出。重要的是完成协议的两方并不知道这个字串具体是如何被生成的，就感觉像是**天上掉下来的**一样。

在CRS假设下，一个协议的双方可以**共享一段事先生成的，但是谁也不知道是怎么生成的参考字串**，并且基于这个参考字串完成原本的协议。

### CRS模型下的无交互LPCP

我们只需要**让别人事先生成好这些Query**，然后通通**丢进CRS里**面，这样证明方就不需要与验证方交互就可以拿到这些随机生成的Query了。

我们接着这个想法，构想一下**CRS模型下无交互LPCP**的大概步骤：

1. 首先，第三方根据LPCP用到的R1CS电路，选取随机数![[公式]](https://www.zhihu.com/equation?tex=r)，并且根据随机数生成好Query部分，即![[公式]](https://www.zhihu.com/equation?tex=q_1%2C+q_2%2C+q_3)，然后丢入CRS当中。
2. 证明方准备好自己的证明向量![[公式]](https://www.zhihu.com/equation?tex=%5Cpi+%3D+%5Bw%2C+h%28m%2B1%29%2C+...%2C+h%282m-1%29%5D)，然后从CRS中取出Query向量的右半部分，即![[公式]](https://www.zhihu.com/equation?tex=q_1%5ER%2C+q_2%5ER%2C+q_3%5ER)。随后分别和自己的证明向量相乘，得到我们要的![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)三个值。

![[公式]](https://www.zhihu.com/equation?tex=a+%3D+%5Clangle+%5Bq_1%5ER+0%5E%7Bm-1%7D%5D%2C+%5Cpi+%5Crangle%5C%5C+b+%3D+%5Clangle+%5Bq_2%5ER+0%5E%7Bm-1%7D%5D%2C+%5Cpi+%5Crangle%5C%5C+c+%3D+%5Clangle+q_3%5ER%2C+%5Cpi+%5Crangle%5C%5C+%5C%5C)

1. 证明方把![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)发送给验证方。
2. 验证方根据CRS得到![[公式]](https://www.zhihu.com/equation?tex=q_1%5EL%2C+q_2%5EL%2C+q_3%5EL)，然后结合证明方提供的![[公式]](https://www.zhihu.com/equation?tex=a%2C+b%2C+c)，检查等式是否相等:

![[公式]](https://www.zhihu.com/equation?tex=%28%5Clangle+q_1%5EL%2C+%5B1%2C+x%5D+%5Crangle+%2B+a%29+%5Ccdot+%28%5Clangle+q_2%5EL%2C+%5B1%2C+x%5D+%5Crangle+%2B+b%29+%5Cstackrel%7B%3F%7D%7B%3D%7D+%28%5Clangle+q_3%5EL%2C+%5B1%2C+x%5D+%5Crangle+%2B+c%29+%5C%5C)

1. 如果相等，即代表LPCP验证通过。

如果我们观察一下我们得到的无交互LPCP的话，我们会发现这**距离我们想要的非常接近了**！证明方与验证方的沟通非常**简短**，只需要发送三个数字即可。验证方所需要做的事情相对来说也很简单，只需要做比较短的矩阵相乘，再把结果加上比较一下就ok了。

然而，这个体系现在还有一个很大的问题：**随机生成的Query直接暴露在CRS当中**。

和上一段结尾提到的问题一样，如果证明方在生成证明向量![[公式]](https://www.zhihu.com/equation?tex=%5Cpi)之前，就已经看到了Query的值，那么他完全就可以**根据Query要取值的点来做弊**，制造出虚假的证明来。

如何保证CRS中的Query不能被用来做弊，但是仍然还可以计算后面的内积呢？需要依靠一种比较特殊的加密系统：**线性加密系统（Linear-only Encoding）**。