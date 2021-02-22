# Awesome Resources

Stay tuned! 😁

## Contents

- [**Introduction**](#Introduction)
- [**History And Important Papers**](#History-And-Important-Papers)
- [**Great resource**](#Great-resource)
  - [Courses](#Courses)
  - [Books](#Books)
  - [Collection of resources](#Collection-of-resources)
- [**Social**](#Social)
  - [Blogs]()
  - [Podcasts](#Podcasts)
  - [Videos](#Videos)
  - [WeChat Official Accounts](#WeChat-Official-Accounts)
- [**Prove Systems**](#Prove-Systems)

### Introduction

- **[zk-SNARKs-Explainer](https://github.com/KevinSmall/zk-SNARKs-Explainer)**
- [**The Incredible Machine**](https://medium.com/qed-it/the-incredible-machine-4d1270d7363a)
- [**On Interactive Proofs and Zero-Knowledge: A Primer**](https://medium.com/magicofc/interactive-proofs-and-zero-knowledge-b32f6c8d66c3)
- [**Demystifying zero-knowledge proofs**](https://docs.google.com/presentation/d/1gfB6WZMvM9mmDKofFibIgsyYShdf0RV_Y8TLz3k1Ls0/edit#slide=id.p)


### History And Important Papers

#### General ZKP

- [Zero-Knowledge Proofs (GMR85)](https://groups.csail.mit.edu/cis/crypto/classes/6.876/papers/gmr-ZK.pdf)
- [Non-Interactive ZK (BFM88)](https://dl.acm.org/citation.cfm?id=62222)
- [How To Simulate It – A Tutorial on the Simulation Proof Technique](https://eprint.iacr.org/2016/046.pdf)

### Great resource

**Here are some awesome resources about ZKP**

#### Courses

- [**COSC 544 - Probabilistic Proof Systems**](http://people.cs.georgetown.edu/jthaler/COSC544.html)
- [**The 9th BIU Winter School on Cryptography: Zero Knowledge**](https://cyber.biu.ac.il/event/the-9th-biu-winter-school-on-cryptography/) **(Very Important！！！！)** 
- [**UIUC: ECE498AC/CS498AM: Applied Cryptography, Fall 2019**](http://soc1024.ece.illinois.edu/teaching/ece498ac/fall2019/)
- [**Stanford CS 251: Bitcoin and Cryptocurrencies**](https://cs251.stanford.edu/)
- [**Efficient Zero-Knowledge Proofs**](https://www.youtube.com/playlist?list=PLgKuh-lKre10OEVNLH3t0QX0rIK8kK3tu)

#### Books

- [**Proofs, Arguments, and Zero-Knowledge**](http://people.cs.georgetown.edu/jthaler/ProofsArgsAndZK.pdf)
- [**ZKProof Community Reference**](https://docs.zkproof.org/reference.pdf)

#### Collection of resources

- [**awesome-zero-knowledge-proofs**](https://github.com/matter-labs/awesome-zero-knowledge-proofs)
- **[zklangs](https://github.com/HarryR/zklangs)**
- **[learning-zkp](https://github.com/sec-bit/learning-zkp/blob/master/zkp-resource-list.md)** (Chinese)
- [**zkp.science**](https://zkp.science/)
- [**Zero-Knowledge Proofs Starter Pack**](https://ethresear.ch/t/zero-knowledge-proofs-starter-pack/4519)
- [**Awesome PLONK**](https://github.com/Fluidex/awesome-plonk)
- [**zkproof**](https://zkproof.org/)

### Social

#### Blogs

- [包罗万象](https://cloud.tencent.com/developer/column/88853)
- [MatterLabs](https://medium.com/matter-labs)
- [mutourend](https://blog.csdn.net/mutourend)
- [coders errand](https://coders-errand.com/category/cryptography/)
- [Bentham’s Gaze](https://www.benthamsgaze.org/)
- [**zero knowledge blog**](https://www.zeroknowledgeblog.com/)

#### Podcasts

- [**Zero Knowledge Podcasts**](https://www.zeroknowledge.fm/)

#### Forums

- [PLONK Café](https://www.plonk.cafe/)


#### WeChat Official Accounts

- **安比技术社区**
- **星想法**
- **包罗万想**
- **blocksight**

### Prove Systems

- [**SNARK**](https://github.com/Whisker17/zkpThings/blob/dev/AwesomeThings/AwesomeSNARK.md)
- [**STARK**](https://github.com/Whisker17/zkpThings/blob/dev/AwesomeThings/AwesomeSTARK.md)
- [**SNORK**](https://github.com/Whisker17/zkpThings/blob/dev/AwesomeThings/AwesomeSNORK.md)
- [**Bulletproof**](https://github.com/Whisker17/zkpThings/blob/dev/AwesomeThings/AwesomeBulletproof.md)
- [**MimbleWimble**](https://github.com/Whisker17/zkpThings/blob/dev/AwesomeThings/AwesomeMimbleWimble.md)

**Comparison of the most popular zkp systems**

![image-20210211132354449](C:\src\github.com\Whisker17\zkpThings\Notes\ZKSNARK\pic\image-20210211132354449.png)

|                                       | SNARKs                     | STARKs                       | Bulletproofs   |
| ------------------------------------- | -------------------------- | ---------------------------- | -------------- |
| Algorithmic complexity: prover        | O(N * log(N))              | O(N * poly-log(N))           | O(N * log(N))  |
| Algorithmic complexity: verifier      | ~O(1)                      | O(poly-log(N))               | O(N)           |
| Communication complexity (proof size) | ~O(1)                      | O(poly-log(N))               | O(log(N))      |
| - size estimate for 1 TX              | Tx: 200 bytes, Key: 50 MB  | 45 kB                        | 1.5 kb         |
| - size estimate for 10.000 TX         | Tx: 200 bytes, Key: 500 GB | 135 kb                       | 2.5 kb         |
| Ethereum/EVM verification gas cost    | ~600k (Groth16)            | ~2.5M (estimate, no impl.)   | N/A            |
| Trusted setup required?               | YES 😒                      | NO 😄                         | NO 😄           |
| Post-quantum secure                   | NO 😒                       | YES 😄                        | NO 😒           |
| Crypto assumptions                    | Strong 😒                   | Collision resistant hashes 😄 | Discrete log 😏 |

[**Back**](https://github.com/Whisker17/zkpThings/blob/master/README.md)