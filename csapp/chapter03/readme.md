如下指令表达式中，S表示源操作数，D表示目的操作数。
### 数据传送指令
* ##### movb指令
movb、movw、movl(该指令会将任意寄存器的高4字节置为0)、movq、movabsq(可以以任意64位立即数位操作数，进行数据传送)。别传输1、2、4、8字节的指令。  
movb S D 源操作数表示一个立即数，目的操作数指定一个位置。  

* ##### movz指令
将较小源值复制到较大目的寄存器。movz类中的指令，将目的中的剩余位设置为0  
movzbw、movzbl、movzwl、movzbq、movzwq。  
* ##### movs指令
将较小源值复制到较大目的寄存器。movs类中的指令，将目的中的剩余位设置为源操作数的最高位  
movsbw、movsbl、movswl、movsbq、movswq、movslq、cltq(只作用于%rax(作为目的操作数)、%eax(作为源操作数)这两个寄存器,没有操作数的指令。用于将%rax值扩展为以%rdx作为高8字节的128位值)。  

* ##### 压入和弹出栈数据
%rsp 寄存器存放栈顶元素地址。栈顶地址永远是栈内最小地址。入栈时,将四字值压入栈中。栈指针减8。  
pushq %rbx  

popq %rdx。先从内存中读取0x123,再写到寄存器%rdx中，然后寄存器%rsp的值将增加回到0x108,值0x123仍然会保持在内存0x100中,直到被覆盖。
* ##### 算术和逻辑操作
leaq  S D       加载有效地址指令    // 除这个指令外，其余指令都会设置条件码，这个指令单纯用于计算地址。
INC D           自增1           // 自增操作
DEC D           自减1
NEG D           取负
NOT D           取补

ADD S,D       加法              // 二元、一元操作
SUB S,D       减法
IMUL S,D      乘法
XOR S,D       异或
OR S,D          或
AND S,D       与

SAL k,D      左移k              // 位移操作
SHL k,D      左移(等同于SAL)
SAR k,D      算术右移
SHR k,D      逻辑右移
* ##### 加载有效地址
leaq &S,D 实际含义是将一个有效地址表达式计算出的值，赋值给目的操作数，目的操作数必须为寄存器。
* ##### 特殊的算术操作
imulq S // 有符号全乘法 生成128位结果，高位8字节存%rdx,低位8字节存%rax  
mulq S // 无符号全乘法 生成128位结果,高位8字节存%rdx,低位8字节存%rax  
clto  // 将%rax寄存器中的值扩展为128位值,高位8字节存%rdx,低位8字节存%rax,并将%rax的符号位，复制到%rdx所有位。
idvq S // 对于%rdx:%rax组成的128位值进行求模，或者取余操作。有符号除法。%rax存放商，%rdx存放余数
divq S // 对于%rdx:%rax组成的128位值进行求模，或者取余操作。无符号除法。%rax存放商，%rdx存放余数
* ##### 流程控制
CF:进位标志  
ZF:零标志  
SF:符号标志  
OF:溢出标志  
根据t=a+b的符号位判断，OF符号位判断溢出时，先判断a、b是否同号。并且,结果t和a的符号不同，表示当前有计算溢出。
CMP类指令:  
如下指令，基于S2-S1获得结果，进行符号位设置,结果为0时,ZF零标志  
1. cmpb S1,S2 
2. cmpw S1,S2 
3. cmpl S1,S2 
4. cmpq S1,S2 
如下TEST指令，基于S1&S2获得结果，进行符号位设置，常用于检查单个数是否为负数、还是正数。  
TEST 
1. testb S1,S2
2. testw S1,S2
3. testl S1,S2
4. testq S1,S2
条件码的读取不是直接进行读取的，而是通过SET指令将某个单字节寄存器。  
或者内存中的单字节设置为0或者1，进行读取。  
本质上是通过条件码的某种组合，将一个字节设置为0或者1。  
SET指令集  
1. sete D   D <- ZF     两个操作数相等
2. setne D  D <- ~ZF    两个操作数不等/非零
3. sets D   D <- SF     负数
4. setns D  D <- ~SF    非负数  
有符号比较  
5. setg D  D <- ~(SF ^ OF) & ~ZF    大于(有符号>),当cmp指令,S2大于S1时
6. setge D D <- ~(SF ^ OF)          大于等于(有符号>=),当cmp指令,S2大于等于S1时
7. setl D D <- SF ^ OF  小于(有符号<),当cmp指令,S2小于S1时
8. setle D D <- (SF ^ OF) | ZF 小于等于(有符号<=),当cmp指令,S2小于等于S1时。  
无符号比较  
9. seta D   D <- ~CF & ~ZF 超过(无符号>),当cmp指令,S2大于S1时,
10. setae D D <- ~CF    超过或相等(无符号>=),当cmp指令,S2等于S1时,
11. setb D D <- CF  低于(无符号<),当cmp指令,S2小于S1时
12. setbe D D <- CF|ZF  低于或相等(无符号<=)
* ##### 跳转指令
jmp 跳转指令  
jmp Label   直接跳转  
jmp *Operand    间接跳转  
je Label    ZF      cmp比较之后,S1,S2两个操作数相等,则进行跳转。  
jne Label   ~ZF     cmp比较之后,S1,S2两个操作数不相等,则进行跳转  
js  Label   SF      cmp执行之后,S1,S2两个操作数,S2-S1结果为负数,则进行跳转  
jns Label   ~SF     cmp执行之后,S1,S2两个操作数,S2-S1结果为非负数,则进行跳转  
有符号数比较  
jg  Label   ~(SF ^ OF) & ~ZF    cmp执行之后,S1、S2两个操作数,S2-S1结果为,S2 > S1,则进行跳转  
jge Label   ~(SF ^ OF)      cmp执行之后,S1、S2两个操作数,S2-S1结果为,S2 >= S1,则进行跳转  
jl  Label   SF ^ OF     cmp执行之后,S1、S2两个操作数,S2-S1结果为,S2 < S1,则进行跳转  
jle Label   (SF ^ OF) | ZF      cmp执行之后,S1、S2两个操作数,S2-S1结果为,S2 <= S1,则进行跳转  
无符号数比较  
ja Label    ~CF & ~ZF   cmp执行之后,S1、S2两个操作数,S2 > S1,则进行跳转  
jae Label   ~CF     cmp执行之后,S1、S2两个操作数,S2 >= S1,则进行跳转  
jb Label    CF      cmp执行之后,S1、S2两个操作数,S2 < S1,则进行跳转  
jbe Label   CF|ZF   cmp执行之后,S1、S2两个操作数,S2 <= S1,则进行跳转  

跳转指令会将目标指令的地址与紧跟在跳转指令后面的那条指令的地址之间的差作为编码。  
