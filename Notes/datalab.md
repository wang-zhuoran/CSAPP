# Data Lab
这个lab主要涉及位运算，补码和浮点数等内容。完成 lab 不仅要实现函数的功能，还要求仅用规定的操作符，操作符数目也在限定范围内，这一点比较坑，因为这样代码可读性不高，当然难度也大了。所有题目都限定在32位系统中。
## 1. 位操作
### (1) bitXor  

```C
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y)
{
  return ~(~x & ~y) & ~(x&y);
}
```  
|x|y|x&y|x^y|x\|y|~(x&y)|
|--|--|--|--|--|--|
|0|0|0|0|0|1|
|0|1|0|1|1|1|
|1|0|0|1|1|1|
|1|1|1|0|1|0|

很明显，```x^y = (x|y) & ~(x&y)```  
由于题目对操作符有限制```&```和```~```  
```x|y```的部分还需要进一步转化  
考虑到[德摩根律](https://zh.wikipedia.org/wiki/%E5%BE%B7%E6%91%A9%E6%A0%B9%E5%AE%9A%E5%BE%8B)可以引入```&```和```~```操作  
```x|y = ~~(x|y) = ~(~x & ~y)```  
因此  
```x^y = ~(~x & ~y) & ~(x&y)``` 
### (2) tmin
```C
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void)
{
  //return 0x80000000;
  return 1 << 31;
}
```
这个比较简单。最小的二进制补码数，就是符号位为1，其余位全部为0
## 2. 补码
### (1) isTmax
```C
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x)
{
  /*

  */
  int res = (x + 1) ^ ~x;
  return !res;
}
```
最大的二进制补码：```0x7fffffff```  
0111...1111, 符号位为0，后面都是1  
这个数字```+1```后就成为：  
1000...0000, 与直接按位取反(```~```)结果相同  
而判断两结果是否相同的操作是异或(```XOR```)  
但是! 要注意```0 ^ 1 = 1, 0 ^ 0 = 0```  
也就是最后还需要在逻辑上取反一次(```!```操作符)  
### (2) allOddBits
```C
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x)
{
	int mask = 0xAA + (0xAA << 8) + (0xAA << 16) + (0xAA << 24);  
	return !((x & mask) ^ mask);
}
```
要比较奇数位是否为1，采取构建位掩码```mask```的方法提取全部奇数位的数值。  
最后采用XOR异或```^```来判断是否奇数位为1
### (3) negate
```C
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x)
{
	return 2;
}
```
