# KEA

假设有这样一个场景，我们在解决一个值得获得图灵奖的一个问题，在最后的关头，需要大量的计算来支持我们的理论，但是由于经费问题，我们本身是无法去完成这样庞大的计算的，所以我们准备和别的实验室合作，借用对方的机器来完成这些计算，但是我们肯定是不希望把这些计算暴露出来，但是我又希望对方没有篡改我输入的数据，怎么办？这就引出了这篇文章所要讲的 KEA ，即 Knowledge-of-Exponent Assumption 。

## KEA

假设 q 是一个质数，可推出 2q+1 也是一个质数，而 g 是 $$Z_{2q+1}^{*}$$ 的阶为 q 的子集的椭圆曲线的生成元。

### KEA1

假设我们现在输入 q，g，g^a 而想要从对面返回一个配对 (C,Y) ，其中 Y=C^a 。最简单的一个方法就是对面选取 c \in Z_q ，让 $$C=g^c,Y=(g^a)^c$$ 。但是 c 这个数我们这边是没有办法知道的。而 KEA1 希望能有一个提取器，能够在相同的输入中返回 c 使得 $$g^c=C \ 或者\ (g^a)^c=C$$ 。

### KEA2

假设我们现在输入 $$q，g，g^a，g^b，g^{ab}$$ 而想要从对面返回一个配对 (C,Y) ，其中 $$Y=C^b$$ 。一种方法就是对面选取 $$c \in Z_q$$ ，让 $$C=g^c,Y=(g^b)^c$$ ；而另一种方法就是选取 $$c \in Z_q$$ ，让 $$C=(g^a)^c,Y=(g^{ab})^c$$ 。但是 c 这个数我们这边是没有办法知道的。而 KEA1 希望能有一个提取器，能够在相同的输入中返回 c 使得 $$g^c=C \ 或者\ (g^a)^c=C$$ 。

## α-变换

假设这样一个场景，Alice 有一个值 a，她想要 Bob 对其进行任意指数的求幂（这里 a 是一个有限域群的生成器），唯一的要求是只能对 a 进行求幂，为了保证这一点，她要：

- 选择一个随机数 *α*
- 计算 $$a' = a^\alpha(mod\ n)$$ 
- 提供一个元组 (a, a') 给 Bob, 然后让他对这两个值执行任意的求幂运算，返回结果元组 *(b, b')*，这里的指数 “$$\alpha$$-变换” 依然保持不变，即 $$b^α = b'(mod\ n)$$ 

因为 Bob 无法从元组 (a, a') 中提取 $$\alpha$$ 的值，通过暴力破解也难以实现，那就可以推断 Bob 生成有效元组的唯一方法就是执行下面的步骤：

- 选择一个值 c
- 计算 $$b=(a)^c(mod\ n) 和 b' = (a')^c (mod\ n)$$
- 回复 *(b,b')*

有了回复的元组和 *α*，Alice 就可以验证等式：

* $$(b)^α = b'$$ 
* $$(a^c)^α = (a')^c$$
* $$a^{c\cdotα} = (a^α)^c$$

结论是：

- Bob 在元组的两个值的计算上都用了同一个指数（即 *c*）
- Bob 只能用 Alice 原本的元组来保持 *α* 关系
- Bob 知道指数 *c*，因为构造验证值 (*b,b*′) 的唯一方式是用同一个指数
- Alice 并不知道 *c*，这和 Bob 不知道 *α* 的原因一样
- 虽然 *c* 是被加密的，但它的可能取值范围并不足够大到保持其零知识的性质。

最后这个协议提供了一个证明给 Alice ，Bob 确实是用他知道的某个值对 *a* 进行求幂的，而且他也不能做别的任何操作，例如：乘法，加法，因为这样就会破坏 α-变换关系。

## KEA 在 zk-SNARKs 中的使用

我们知道，对于一个多项式而言，其知识就是系数的知识，假设我们有一个简单的多项式 $$f(x)=c \cdot x$$ ：

* verifier 选择随机数 $$s，\alpha$$ ，然后提供当 x=s 时通过 α-变换后的配对：

  $$(g^s,g^{\alpha s})$$

* prover 代入系数 c 进行计算：

  $$((g^s)^c,(g^{\alpha s})^c)=(g^{cs},g^{\alpha cs})$$

* verifier 进行验证：

  $$(g^{cs})^\alpha=g^{\alpha cs}$$

这个结构“限制” prover 只能用 verifier 提供的加密的 s 进行计算，因而 prover 只能将系数 c 赋给 verifier 提供的多项式。现在我们可以扩展这种单项式上的方法到多项式上，因为计算是先将每项的分配分开计算然后再 “同态地” 相加在一起的。所以如果给定 prover 一个指数为 *s* 的幂以及它们的变换的加密值，他就可以计算原始的和变换后的多项式，这里也必须要满足同样的校验。对于阶数为 *d* 的多项式：

* verifier 提供加密值 $$g^{s^0},g^{s^1},...,g^{s^d}\ 和他们的变换 g^{\alpha s^0},g^{\alpha s^1},...,g^{\alpha s^d}$$

* prover 计算给定的带有 s 的幂的加密多项式
  $$
  g^{p(s)}=(g^{s^0})^{c_0} \cdot (g^{s^1})^{c_1} \cdot ...(g^{s^d})^{c_d}\\
  =g^{(c_0s^0+c_1s^1+...+c_ds^d)}
  $$
  

  以及带有 s 的幂的转换的加密“转换”多项式：
  $$
  g^{\alpha p(s)}=(g^{\alpha s^0})^{c_0} \cdot (g^{\alpha s^1})^{c_1} \cdot ...(g^{\alpha s^d})^{c_d}\\
  =g^{\alpha (c_0s^0+c_1s^1+...+c_ds^d)}
  $$
  然后将计算结果 $$g^p,g^{p^{'}}$$ 发送给 verifier

*  verifier校验：$$(g^p)^{\alpha}=g^{p^{'}}$$

  

## Reference

[The Knowledge-of-Exponent Assumptions and 3-Round Zero-Knowledge Protocols](https://eprint.iacr.org/2004/008.pdf)

[Towards practical public key systems secure against chosen ciphertext attacks](https://link.springer.com/chapter/10.1007%2F3-540-46766-1_36)

[从零开始学习 zk-SNARK（二）——多项式的非交互式零知识证明](https://mp.weixin.qq.com/s?__biz=MzIxNjkwODE5NQ==&mid=2247484247&idx=1&sn=f6ebfc82f105b6253f7d3b97ad77dc22&chksm=9780ab9ba0f7228dbd9d63a46b8aeaabc791b833ca9372a74978d0a496c4d4f4083b347b373f&scene=21#wechat_redirect)