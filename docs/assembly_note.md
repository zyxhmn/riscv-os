# riscv 汇编

## RISC-V汇编语言入门

参考：

- riscv-spec-20191213.pdf
- https://sourceware.org/binutils/docs/as

### 汇编语言概念简介

- 汇编语言(Assembly Language)是一种低级的语言。

- 一个完整的RISC-V汇编程序有多条语句(statement)组成

- 一条典型的RISC-V汇编语句由三部分组成：[label :] [operation] [comment]

  --方括号表示可选项

  -- label 标号：GNU汇编中， 任何以冒号结尾的标识符被视为标号

  --operatioin 

     -- instruction(指令)：直接对应二进制机器指令的字符串

     -- pseudo-instruction(伪指令)：为了提高编写效率，使用一条伪指令指示汇编器产生多条实际指令(instructions).

  ​    -- dierctive(指示/伪操作) ：通过类似指令的形式(以"."开头)，通知汇编器如何控制代码的产生，不对应具体的指令

  ​    --macro :采用 .macro/.endm自定义的宏

``` assembly
# First RISC-V Assemble Sample

.macro do_nothing	# directive
	nop		# pseudo-instruction
	nop		# pseudo-instruction
.endm			# directive

	.text		# directive
	.global _start	# directive
_start: 		# Label
	li x6, 5	# pseudo-instruction
	li x7, 4	# pseudo-instruction
	add x5, x6, x7	# instruction
	do_nothing	# Calling macro
stop:	j stop		# statement in one line

	.end		# End of file

```

汇编语言的扩展名含义

- .S 包含预处理指令
- .s 纯汇编指令

## 汇编指令总览

### RISC-V汇编指令操作对象

####  寄存器

- 32个通用寄存器，x0~x31(RV32I通用寄存器组)
- 在RISC-V中，Hart在执行算数逻辑运算时操作的数据必须直接来自寄存器

#### 内存

- Hart 可以执行在寄存器和内存之间的数据读写操作；
- 读写操作使用字节（Byte）为基本单位进行寻址；
- RV32 可以访问最多 2^32 个字节的内存空间。

(**HART**（Hardware Thread）是指硬件线程的缩写，它代表了处理器的一个执行单元。HART用于描述处理器中可以独立执行指令流的最小硬件单元。每个HART可以独立执行指令，可以是一个处理器核心，也可以是同一个核心中的多个执行线程，取决于硬件实现。)

### RISC-V汇编指令编码格式

![指令格式](/home/taihang/project/riscv-os/resources/images/learn/r1.png)

- 指令长度：ILEN1=32bits(RV32I)
- 指令对齐：IALIGN = 32 bits (RV32I)
- 32 个 bit 划分成不同的 “域（field）
- funct3/funct7 和 opcode 一起决定最终的指令类型
- 指令在内存中按照小端序排列

####  六种指令格式

rs2,rs1,rd为寄存器代号，rd用于存放计算结果

- R-type:（Register），每条指令中有三个 fields，用于指定 3 个 寄存器参数
- I-type: Immediate），每条指令除了带有两个寄存器参数外，还带有一个立即数参数
  （宽度为 12 bits）。
- S-type: （Store），每条指令除了带有两个寄存器参数外，还带有一个立即数参数（宽
  度为 12 bits，但 fields 的组织方式不同于 I-type）
- B-type: (Branch)，每条指令除了带有两个寄存器参数外，还带有一个立即数参数（宽度
  为 12 bits，但取值为 2 的倍数）。
- U-type: （Upper），每条指令含有一个寄存器参数再加上一个立即数参数（宽度为 20
  bits，用于表示一个立即数的高 20 位）
- J-type: （Jump），每条指令含有一个寄存器参数再加上一个立即数参数（宽度为 20
  bits）

## RISC-V汇编指令详解

### 算数运算指令

#### ADD 

``` assembly
语法： ADD RD,RS1,RS2
例子： add x5,x6,x7
```

-   opcode (7): 0110011（OP）
- funct3 取值 000；funct7 取值 0000000
- rs1 (5): 第一个 operand (“source register 1”)
-  rs2 (5): 第二个 operand (“source register 2”)
-  rd (5): “destination register” 用于存放求和的结果

  

​      
