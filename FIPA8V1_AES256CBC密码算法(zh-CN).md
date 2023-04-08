# FIPA8V1_AES256CBC对称密码算法(zh-CN)

## 目录

[摘要](#摘要)

[FIPA共识](#FIPA共识)

[AES-256-CBC-PKCS7Padding概述](#AES-256-CBC-PKCS7Padding概述)

[明文分段](#明文分段)

[加密数据](#加密数据)

[解密数据](#解密数据)

[示例](#示例)

## 摘要
```
Protocol type: FIPA
Serial number: 1
Protocol name: AES256CBC对称密码算法
Version number: 8
Description : AES-256-CBC-PKCS7Padding密码算法。
Author: No1_NrC7，天豪_1FbF
Language: en-US
Created date: 2023-04-08
Update date：2023-04-9
```
## 关于FIPA
FIPA（Freecash Improvement Protocols for Assistance) 用于改进Freecash生态应用。实现此类协议不需要对Freecash主网进行硬分叉升级，也不需要在链上写入信息。

## AES-256-CBC-PKCS7Padding概述
AES(Advanced Encryption Standard，高级加密标准)，为对称分组密码体制，本算法采用AES-256-CBC-PKCS7Padding加密方式。此种方式下加解密需要一个16字节的初始化向量(Initialzation Vector, IV)，同时密钥的长度为256位，即32字节；并采用CBC模式加密，即先将明文切分成若干个小段，每一个小段与初始块或上一段的密文进行异或运算后，再与密钥进行加密；采用PKCS7Padding填充模式，保证明文分段时，对于数据不齐时，对数据按字节进行补齐，若数据本身已经对齐，则会填充一块长度为块大小的数据，每个字节都是块大小。算法具体描述如下：

## 明文分段

对于任意长度的明文$Plaintext$，首先根据PKCS7Padding进行补齐，补齐后将数据分段，每块大小为16字节。分段后得到n个明文段，即$Plaintext_1,...,Plaintext_n$。

## 加密数据

1. $Plaintext_1$与IV进行异或运算，得到$Input_1$做为AES加密算法的输入，与密钥$key$进行计算，得到密文$Ciphertext_1$。
2. $Plaintext_2$与$Ciphertext_1$进行异或运算，得到$Input_2$做为AES加密算法的输入，与密钥$key$进行计算，得到密文$Ciphertext_2$。
3. 循环往复，直至将所有的明文段加密完，最后一次得到密文$Ciphertext_n$。
4. 将$Ciphertext_1，...,Ciphertext_n$拼接在一起，即为最终的密文$Ciphertext$。

## 解密数据

1. 将$Ciphertext$按16字节，划分成n个密文段$Ciphertext_1,...,Ciphertext_n$。
2. $Ciphertext_1$与密钥$key$进行解密运算，得$Output_1$，$Output_1$与IV进行异或，得到第一段明文$Plaintext_1$。
3. $Ciphertext_2$与密钥$key$进行解密运算，得到$Output_2$，$Output_2$与$Ciphertext_1$进行异或，得到第二段明文$Plaintext_2$。
4. 循环往复，即可得到左右的明文段，最后将$Plaintext_1,...,Plaintext_n$拼接在一起，即可得到最终的明文$Plaintext$。

## 示例
```
plain text = {"data":"test"}
symKey(hex) = 7904517bd0c5646aeb861b1475bc4d7801a156b9950d0fadaa3b2196c7cd4c08
ciphertext(hex) = aa694bbb4ed465bee59f3582e05f58ae
```