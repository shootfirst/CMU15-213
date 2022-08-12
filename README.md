# CMU15-213
卡内基梅隆2021年春计算机系统实验(CSAPP)

终于开始写大名鼎鼎的csapp实验了。这10个实验我都会给出自己的实验思路

## datalab
datalab主要是让我们熟悉位运算和数据存储在计算机中的形式

### bitXor

使用 ~ 和 & 实现异或。通过德摩根律和异或的定义，我们可以得出以下推导：x ^ y = (~ x & y) | (x & ~ y) = ~ ~ ((~ x & y) | (x & ~ y)) = ~ (~ (~ x & y) & ~ (x & ~ y))。

### tmin

获取32位补码最小值。32位补码最小值是0x80000000，直接return 1 << 31 完事。

### isTmax

获取32位补码最大值。32位补码最大值x是0x7fffffff，直接我们可以看到，x+x+2值会溢出，溢出后值为0.但是-1也满足这个条件，所以只需x+x+2为0且x不为-1即可，判断是否为0不能使用==，这里
可以使用异或。

### allOddBits

判断奇数位是否全部为1。可以通过判断x&0xaaaaaaaa是否还为0xaaaaaaaa。这里我们需要先构造出0xaaaaaaaa，因为我们只能直接使用0-0xff。通过耗费四个运算符，我们可以做到这点：a = 
(0xAA << 8) | 0xAA， int b = (a << 16) | a。b就是0xaaaaaaaa。然后后面就很简单了。

### negate

返回负数，没话可说，每位取反，再加1.

### isAsciiDigit

判断是否满足0x30 <= x <= 0x39。我们分三个小部分：x >= 0x30，x < 0x39， x == 0x39。判断是否大于小于是这样：和相关数据的补码相加然后判断最高位是否为1。为1则是小于，不为1则是
大于等于。三个小部分分别可以这样判断：a = (x + ~0x2f) & j 为0， b = (x + ~0x38) & j不为0，c = !(x ^ 0x39)为1。其中j = 0x80 << 24。最后return !a&!!b|c。因为b = (x + 
~0x38) & j不为0只能得出x < 0x39而不能得出x == 0x39，故需要多出!(x ^ 0x39)为1来判断。其中为了节约运算符，我使用x + ~0x2f来替代x + ~0x30 + 1，因为我现在刚好是14个运算符嘻
嘻。

### isLessOrEqual

判断是否x<=y。由于溢出的存在，我们不能直接通过相加补码后右移31位判断是否为1来判断是否小于。溢出会使原来应该是小于等于的变成大于。如0x10000000和0x00000001和。本来应该是小于
的，结果会被判断为大于。这是我的核心思路：(x < 0 && y >= 0 || x - y > 0 || x == y) && !(x >= 0 && y < 0)
首先需要明确的是只有符号相异才会溢出。所以我们先判断若x >= 0 && y < 0，直接宣布大于。然后判断若x < 0 && y >= 0则不论后面结果如何，直接判断是小于，若这条不满足，后面结果是
不可能溢出的，则可以放心判断x - y > 0 或者 x == y。式子从左到右分别是abcd，可表示为a = !!(x >> 31) & !(y >> 31)，b = !!((x + (~y) + 1) >> 31)，c = !(x ^ y)，d = !(!(x >>
31) & !!(y >> 31))。返回(a | b | c) & d即可。注意千万是不能删除"!!"的，这个是将非0值转化为1的关键，如果没有这个，后面的|和&判断会出错！

### logicalNeg

使用~ & ^ | + << >>表示!。可以通过使用0的相反数符号位不变，是0。那么可以通过获取x和x相反数最高位来判断。有一点需要注意，这个数（以8位为例）10000000的相反数还是自己，但是符
号位也是相同。所以我们不能通过判断两个符号位异或是否为1，而是两个符号位或是否为1.

### howManyBits

太难了，我参考别人的哭泣😂。

### floatScale2

首先需要说明的是这个实验以及下面两个都需要熟悉iee754标准，如图：

![](ieee754.png)

在深入了解这个标准之后，后面的就简单了。首先如果是NaN或者是infinite，则直接返回，否则，如果是非规格化数，乘以2，否则将阶码加1。


















