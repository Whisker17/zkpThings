# Groth16 详解

- **算法论述**
- **计算详解**

## 算法论述

### NILP

**定义：**

![image-20210216200742782](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210216200926.png)

**split NILP**：

![image-20210216201236377](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210216201237.png)

### QAP 的 NILP

![image-20210216204152269](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210216204153.png)

![image-20210216204217272](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210216204219.png)

![image-20210216205133740](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210216205135.png)

## 计算详解

### **R1CS**描述

![image-20210218100454085](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218100520.png)

### QAP 转化

- **拉格朗日插值以及拉格朗日basis**

![image-20210218101956053](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218101957.png)

- **domain 的选择**

  针对每个变量，已经知道N个y值。如何选择这些y值，对应的x值？这个就是domain的选择。

  **选择哪⼀种domain和输⼊个数（M）有关。**为了配合特定domain的计算，domain的阶（M）会稍稍变⼤。

  确定了domain，也就确定了domain上的⼀组元素s：

  ![image-20210218103638563](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218103639.png)

- **Setup 计算**

  ![image-20210218103720011](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218103721.png)

- **Prove 计算**

  在domain选择后，U*V=W，可以变换为如下的多项式⽅程：

  ![image-20210218103842154](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218103843.png)

  ![image-20210218103853699](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218103854.png)

- **Verify 计算**

  ![image-20210218105124216](https://raw.githubusercontent.com/Whisker17/ImageStoreService/main/img/20210218105125.png)

- **总结**

  Groth16算法的主要计算量由两部分组成：FFT/iFFT以及MultiExp。**在⽣成证明时，需要4次iFFT以及三次FFT计算。**Setup计算和⽣成证明时，需要⼤量的MultiExp。Verify计算量相对较小。