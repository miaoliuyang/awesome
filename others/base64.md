https://baike.baidu.com/item/base64/8545775?fr=aladdin

Base64就是一种基于64个可打印字符来表示二进制数据的方法。

0~63 依次为字符A~Z a~z 0~9 + /

### 转换规则
```
以s13为例子
先转成ascii    115 49 51
对应二进制为  01110011 00110001 00110011
6个一组(4组)  011100 110011 000100 110011
高位补0       00011100 00110011 00000100 00110011
转为十进制    28 51 4 51
查对下照表    c z E z

可在浏览器中执行window.btoa('s13')查看结果
```

```
以s1为例子
先转成ascii  115 49
对应二进制为  01110011 00110001
6个一组(不足3组,第3组最后补充两个0)  011100   110011 000100
高位补0       00011100 00110011  00000100
转为十进制    28 51 4 
查对下照表(不足4组，最后一位补为=)    c z E =
```

```
以s为例子
先转成ascii  115
对应二进制为  01110011
6个一组(不足2组,第2组最后补充两个0)  011100   110000
高位补0       00011100 00110000
转为十进制    28 48
查对下照表(不足4组，最后两位补为=)    c z = =
```

Base64要求把每三个8Bit的字节转换为四个6Bit的字节（3`*`8 = 4`*`6 = 24），然后把6Bit再添两位高位0，组成四个8Bit的字节，也就是说，转换后的字符串理论上将要比原来的长1/3。

规则：1 把3个字符变成4个字符。 2. 每76个字符加一个换行符。 3.最后的结束符也要处理

原文剩余的字节根据编码规则继续单独转(1变2,2变3;不够的位数用0补全)，再用=号补满4个字节。这就是为什么有些Base64编码会以一个或两个等号结束的原因，但等号最多只有两个。因为：

一个原字节至少会变成两个目标字节

所以余数任何情况下都只可能是0，1，2这三个数中的一个。如果余数是0的话，就表示原文字节数正好是3的倍数（最理想的情况）。如果是1的话，转成2个Base64编码字符，为了让Base64编码是4的倍数，就要补2个等号；同理，如果是2的话，就要补1个等号。

标准的Base64并不适合直接放在URL里传输，因为URL编码器会把标准Base64中的“/”和“+”字符变为形如“%XX”的形式，而这些“%”号在存入数据库时还需要再进行转换，因为ANSI SQL中已将“%”号用作通配符。

为解决此问题，可采用一种用于URL的改进Base64编码，它在末尾填充'='号，并将标准Base64中的“+”和“/”分别改成了“-”和“_”，这样就免去了在URL编解码和数据库存储时所要作的转换，避免了编码信息长度在此过程中的增加，并统一了数据库、表单等处对象标识符的格式。

另有一种用于正则表达式的改进Base64变种，它将“+”和“/”改成了“!”和“-”，因为“+”,“*”以及前面在IRCu中用到的“[”和“]”在正则表达式中都可能具有特殊含义。

此外还有一些变种，它们将“+/”改为“_-”或“._”（用作编程语言中的标识符名称）或“.-”（用于XML中的Nmtoken）甚至“_:”（用于XML中的Name）。