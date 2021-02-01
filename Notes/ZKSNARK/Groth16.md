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