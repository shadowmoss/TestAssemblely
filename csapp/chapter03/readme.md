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
movsbw、movsbl、movswl、movsbq、movswq、movslq、cltq(只作用于%rax(作为目的操作数)、%eax(作为源操作数)这两个寄存器,没有操作数的指令。)。  

* ##### 压入和弹出栈数据
%rsp 寄存器存放栈顶元素地址。栈顶地址永远是栈内最小地址。入栈时,将四字值压入栈中。栈指针减8。  
pushq %rbx  

popq %rdx。先从内存中读取0x123,再写到寄存器%rdx中，然后寄存器%rsp的值将增加回到0x108,值0x123仍然会保持在内存0x100中,直到被覆盖。
* ##### 算术和逻辑操作
leaq  S D       加载有效地址指令
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
### 加载有效地址
leaq &S,D 实际含义是将一个有效地址表达式计算出的值，赋值给目的操作数，目的操作数必须为寄存器。