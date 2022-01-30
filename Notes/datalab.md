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
	return ~x + 1;
}
```
这个就是公式了。认真读课本就好。
## 3.
###  (1) isAsciiDigit
```C
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x)
{
	return 2;
}
```
### (2) conditional
```C
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z)
{
    int temp = !!x;
	return (temp & y) | (~temp & z);
}
```
这里感觉有点问题...以后再回来更新吧...
### (3) isLessOrEqual
```C
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y)
{
	int sx = (x >> 31) & 0x1;  // sign bit of x
 	int sy = (y >> 31) & 0x1;  // sign bit of y
  	int sdiff = ((y + (~x + 1)) >> 31) & 0x1;  // sign bit of y-x
  	return (((sx ^ sy) & sx) | !sdiff & !(sx ^ sy));
}
```
基本的思路就是，将不等式转化为```y - x >= 0```  
但是这样做，会产生溢出的问题。  
可能有人会直接认为，```return !sdiff;```就好了啊，但是并不是这样。x和y有4种情况：
```
check 4 cases:
1. x < 0, y >= 0 => x <= y => return 1
2. x >= 0, y < 0 => x >= y => return 0
3. x < 0, y < 0 => y - x >= 0 iff. y >= x, there will be no overflow
4. x >= 0, y >= 0 => same case as 3
```
尤其是第二种case, 取```x = 1<<31, y = -1```时，```return !sdiff;```返回1  
因此需要特别排除这种溢出情况
