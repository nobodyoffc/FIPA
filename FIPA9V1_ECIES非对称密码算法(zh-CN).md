
# FIPA9V1_ECIES非对称密码算法(zh-CN)

## 目录

[摘要](#摘要)

[FIPA共识](#FIPA共识)

[ECC算法说明](#ECC算法说明)

[ECIES算法说明](#ECIES算法说明)

[密钥生成](#密钥生成)

[加密数据](#加密数据)

[解密数据](#解密数据)

[示例](#示例)

## 摘要
```
Protocol type: FIPA
Serial number: 9
Protocol name: ECIES非对称密码算法
Version number: 1
Description : ECIES非对称密码算法。
Author: No1_NrC7，天豪_1FbF
Language: en-US
Created date: 2023-04-08
Update date：2023-04-9
```

## 关于FIPA

FIPA（Freecash Improvement Protocols for Assistance) 用于改进Freecash生态应用，不需要对Freecash主网进行硬分叉升级，也不需要在链上写入信息。

## ECC算法说明

ECC(Elliptic Curve Cryptography，椭圆曲线密码学)，基于有限域的椭圆曲线和复杂的椭圆曲线离散对数，实现的一种非对称加密系统。

在本算法库中的椭圆曲线加密算法是基于secp256k1实现的。 大多数常用的曲线都具有随机结构，但 secp256k1 是以特殊的非随机方式构建的，因此可以实现特别高效的计算。因此，如果实现充分优化，它通常比其他曲线快 30% 以上。 

## ECIES算法说明
本算法库所实现的是基于椭圆曲线的一种集成加密方案，ECIES(elliptic curve integrate encrypt scheme)。其中密钥派生函数、校验码的生成算法和对称加密方案是根据需要设计的，本算法库选择的密钥派生函数为SHA512哈希函数，校验码生成算法为SHA256哈希函数，对称加密方案选择的是AES/CBC/PKCS7Padding。

## 密钥生成

在椭圆曲线$Ep(a,b)$中选择两个点$G,K$，满足$K=kG$，$n$为$G$的阶$(nG=O_{\infty})$，$k$为小于$n$的整数。则给定$k$和$G$，计算$K$是很容易的，而给定$K$和$G$计算$k$是非常困难的，同时由于$n,p$的取值是非常大的，因此想要把各个点逐一算出是不可能的，基于此构建非对称密码算法。

1. 在椭圆曲线上随机选取一点$G$，称为基点。
2. $k(k<n)$为私钥。
3. $K$为公钥。

在使用椭圆曲线进行加密时，用户需要公开自己的公钥$K$，其他人可以通过公钥加密明文数据得到密文发送给用户，而用户可以通过自己的私钥$k$进行解密。而其他人在不知道私钥的情况下是无法解密的。

## 加密数据

为加密信息$m$，需要以下步骤：

1. 生成一个随机数$r\in[1,n-1]$，并计算$R=rG$。
1. 将秘密$S$映射到椭圆曲线上，令$S=P_x$，其中$P=(P_x, P_y)=rK(P\neq O_{\infty})$。
1. 使用哈希算法SHA512，对秘密$S$进行哈希运算，得到的值分别做为对称加密的密钥以及校验密文是否正确的密钥，$k_E||k_M=\mathsf{SHA512}(S)$。
1. 对秘密进行加密$c=E(k_E;m)$
1. 计算加密信息的校验码$d=\mathsf{SHA256}(k_M;c)$
1. 输出$R||c||d$。

## 解密数据

当获得$R||c||d$后执行解密：

1. 求出$P=(P_x,P_y)=kR$，因为$P=kR=krG=rkG=rK$，其中$S=P_x$。
2. 生成加密密钥和校验密钥$k_E||k_M=\mathsf{SHA512}(S)$。
3. 计算校验码是否正确，即$d$与$\mathsf{SHA256}(k_M;c)$。
4. 使用对称密钥进行解密$m=D(k_E;c)$。

## 示例
```
priKey(hex) = a048f6c843f92bfe036057f7fc2bf2c27353c624cf7ad97e98ed41432f700575
message = {"data":"test"}
ciphertext(Base64) = yC8rQtc6FugEwJVoiDGYVB5I2N6qZjDYlw+TREWl9WRBeCF3y+S3ka0GijIKjZFMDn7k5in03lbUJ1lnXi8XA8/R5iqQ8jgKZnEfl6m+BOuyPsWxF2Quvay9hz9CENI8q2NahkQX6IBKrLTJ8SBVOg==
```
