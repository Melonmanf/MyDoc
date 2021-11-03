# 计算机组成原理

**计算机组成原理(Principles Of Computer Organization)**

## 1. 概论



## 2. 数据表示、运算与校验

### 进制转换



低	-->	高	除 辗转相除（按基底相除，取余）

高	-->	低	乘 **按权相加**



**分组转化** 8421、421



### 带符号数

二进制数的码制：原码、反码、补码、移码

####  原码

二进制真值+符号位

如  十进制 12， 转二进制为 1010，原码为11010

#### 反码

正数正反码一直，除了符号位以外，皆取反。

#### 补码

正数反码补码一致，负数符号位不变，将原码变反码后加1。

求变补（即变号补码，-x）, 连同符号位一起变反，加1。

#### 移码

移码=阶码=浮点数的整数，也可称为增码。

移码表示范围与补码一致，0只有一个移码。

正数：将原码符号位变反，得到移码；

负数：将原码连同符号位一起变反，末尾再加1，得到移码。（与变补等效）

补码和移码：符号相反、数值位相同。