# Groth16

从性能上，Groth16 的 Verifier 性能是所有 zkSNARK 中**最快**的，其证明字符串也是最短的。

而 Groth16 的最大缺点就是它需要对**每个电路都执行一次可信初始化**。

## 计算验证问题

**一句话概括计算验证问题：Verifier 能否在不知道秘密输入的情况下，高效地验证计算结果？**

![](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201102720.webp)

计算验证问题是指，如果一个验证者——不妨叫做 Verifier——只拿到了电路的一组输入和输出，如这个例子中的 (1,2,3,4,5) 和 (-27,14,80,171) ，它该如何验证这是一对合法的输入和输出呢？最简单粗暴的方法就是把这个输入重新扔进电路算一遍。如果电路很大的话，这个验证方法最大的缺点就是**效率问题**。

有些场景下，效率还不是唯一的问题。例如，输入可能包含 Verifier 不能知道的秘密信息。假设上例中的 (3,4,5) 是秘密输入，Verifier 只能看到 (1,2)  。此时 Verifier 要验证的问题就变成了“是否存在 (?,?,?) 使得电路在输入 (1,2,?,?,?) 的时候的输出是 (-27,14,80,171) "。这个场景下，即使是简单粗暴的重新计算也不再可行。

## 从电路到 R1CS 问题

zkSNARK 就是对上述问题的一个解决方案。使用 zkSNARK，一个证明者，叫做 Prover，可以为计算过程生成一个简短的证明字符串。Verifier 读取这个字符串，就可以判断给定的公开输入和输出是否合法。

我们把值已经公开的空格标记为绿色，待求的空格标记为蓝色：

![](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201102725.png)

然后，我们为每一个要满足的条件列一个方程。

![](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201102730.png)

![image-20210201095333446](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201102734.png)

![image-20210201095515486](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201102739.png)

![image-20210201123113686](https://raw.githubusercontent.com/Whisker17/ImageStoreService/master/img/20210201123115.png)

## 从 R1CS 问题到 QAP 问题

大部分 zkSNARK 不会直接对 R1CS 下手，而是把 R1CS 问题继续转化，**得到一个等价的多项式问题**，再对这个多项式问题设计证明方案。

Groth16 选择的是一个叫做 **Quadratic Arithmetic Programming (QAP)** 的问题。

![image-20210201151855735](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170056.png)

## QAP 问题

现在，我们直接把 R1CS 矩阵中的列向量换成它们的多项式插值，得到的结果如下图所示。

![图片](https://mmbiz.qpic.cn/mmbiz_png/SibzTfiakrIr2YebgzzZJibqf4NzjjicU593qr7nusCmicqWw8qkD4B8ibw1ictCXmTFq0TiapQmXKdMSzCkicOiaEz1q0FA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

![image-20210201154047709](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203165855.png)

我们用一个表格总结一下上文中提到的所有问题。

![image-20210201154221725](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170126.png)

为什么要越搞越复杂，把电路问题转化为 QAP 问题呢？一个简单的回答：就是为了引入多项式！多项式是一个强大的工具。多项式的作用，可以理解为一个“杠杆”，或者叫“误差放大器”。如果我们要检查两个长度为 10000 的向量是否相等，一定需要检查 10000 次，哪怕检查过了 9999 个点都是一样的，也不能保证最后一点是相同的。而两个 10000 次的多项式，哪怕非常接近，比如说它们的系数有 9999 个都相同，或者它们在 1,2,...,999这些点上的取值都相等，但只要有一个点不同，这两个多项式就截然不同。这意味着，如果在一个很大的范围内，例如 1 到 100000000 当中均匀随机选一个点，两个不同的多项式在这个点上相等的机会只有 1/10000 。检查两个多项式是否相等，比检查同样规模的向量要快得多，**这几乎是所有 zkSNARK 提高 Verifier 效率的根本原理**。

## 为 QAP 问题构造 zkSNARK

椭圆曲线群中的元素可以用来表示多项式，并限制 Prover 必须使用给定的多项式来进行线性组合。这正是 QAP 所需要用到的特性。

### KoE 假设

![image-20210201163153364](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170134.png)

### 双线性配对

![image-20210201163338668](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170142.png)

## Groth16

![image-20210201174125020](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170148.png)

### Setup

![image-20210201174301307](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170155.png)

### Prove

![image-20210201174340708](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170202.png)

### Verify

![image-20210201174435083](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170213.png)

### 解析

![image-20210201181235859](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210203170219.png)

