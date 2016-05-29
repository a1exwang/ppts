title: Alex-Machine工具链
speaker: 王奥丞
url: http://soappt.a1ex.wang
transition: cards
files: /js/demo.js,/css/demo.css

[slide]
# 基于LLVM的Alex-Machine工具链
## - 王奥丞

[slide]
## 1. 需求/目标
------
1. 在Alex-Machine这个平台能够编译标准C/C++, 汇编语言程序 {:&.rollIn}
1. 生成ELF文件
1. 能生成调试符号

[slide]
## 2. 工具链
------
- 使用LLVM框架 {:&.rollIn}
- 用clang将C/C++编译到LLVM IR bitcode文件
- 编写LLVM后端, 将LLVM IR翻译成Alex-Machine汇编代码/机器码, 得到目标文件(llc)
- 编写Assembler, 支持内联汇编和汇编语言文件(llvm-mc)
- 利用lld框架编写链接器(lld)
- elf分析工具(llvm-objdump)

[slide]
## 3. llc
1. LLVM IR是如何变成汇编文件? {:&.rollIn}
    - 多趟遍历IR {:&.rollIn}
1. LLVM IR是如何表示的?
    - SSA: static single assignment, 静态单赋值形式, llvm中表示为以下形式 {:&.rollIn}
        ```
          %vreg1 = CopyFromReg %a
          %vreg2 = CopyFromReg %b
          %vreg3 = add %a, %b
          %vreg4 = CopyFromReg %c
          %vreg5 = sub %vreg3, %vreg4
        ```
    - DAG: directed acyclic graph, 有向无环图, 在llvm中表示为以下形式
        ```
        (set %vreg1, (sub (add %a, %b), %c))
        ```
    - 在clang生成的bitcode文件中是SSA

[slide]
## 3. llc
- 有几趟遍历? 分别是什么? {:&.rollIn}
 - ![passes](/assets/passes.png) {:&.rollIn}
  1. LLVM IR SSA -> LLVM IR DAG {:&.rollIn}
  1. LLVM IR DAG Lowering
  1. LLVM IR DAG -> Alex DAG
  1. Alex DAG -> Alex SSA
  1. Alex SSA Lowering
  1. Alex SSA -> Asm text

[slide]
## 3.1 LLVM IR SSA -> LLVM IR DAG
- 为什么要LLVM IR SSA -> LLVM IR DAG?
- LLVM IR bitcode文件中是以SSA形式保存的, 转化成DAG方便优化和翻译(见tblgen部分). {:&.rollIn}

[slide]
## 3.2 LLVM IR DAG Lowering
为什么要LLVM IR DAG Lowering?
  - LLVM IR是比较高级的表示形式, 该指令集为了在实际中运行效率, 跨平台更容易等等的考虑, 做的比较冗余. 我们需要去掉其中一些指令, 简化后面的翻译. {:&.rollIn}
  - 展开函数调用, 符号引用这些高级概念.

[slide]
## 3.3 LLVM IR DAG -> Alex DAG
1. 为什么LLVM IR DAG -> Alex DAG? {:&.rollIn}
    - 为什么: 显然, 后端的主要功能就是LLVM IR翻译为Alex机器码/汇编代码, {:&.rollIn}
    - 在DAG这一层面上进行转换主要是为了方便匹配(见tblgen部分)
    s
1. 具体做了什么? {:&.rollIn}
  - 用tblgen语言匹配IR DAG

[slide]
## 3.3 用tblgen翻译DAG(IR DAG -> Alex DAG)
- tblgen语言是类似C++模板的语言, 是LLVM内部用于简化对目标平台描述的DSL {:&.rollIn}
- tblgen语言不规定语义, 只规定语法, tblgen backend规定语义.
- 通常tblgen是通过生成C++代码片段来使用的.
- tblgen语言的一个具体例子
    ```
    // LI指令是加载16bit立即数到寄存器
    // e.g. LI $ra, 0xffff
    // 以下定义的例子是说, opcode = 0x31,
    // (outs Int32Regs:$ra), 有一个输出寄存器$ra(未定)
    // (ins simm16:$imm16), 有一个输入参数是16bit有符号立即数
    // asmstr是"li\t$ra, $imm16", 其中$ra, $imm16是引用前面的变量
    // [(set Int32Regs:$ra, (i32 immSExt16:$imm16))], 这部分是说匹配
    // 这样的LLVM IR DAG node, 将这样的node替换成LI node
    def LI      : InstrRI<0x31, (outs Int32Regs:$ra), (ins simm16:$imm16),
               "li\t$ra, $imm16",
               [(set Int32Regs:$ra, (i32 immSExt16:$imm16))]>;

    // 未命名definition, 仅仅用于模式匹配, 含义和上一个类似
    def : Pat<(brcc SETLT, Int32Regs:$ra, Int32Regs:$rb, bb:$imm16),
              (BNEZ (LT Int32Regs:$ra, Int32Regs:$rb), brtarget16:$imm16)>;
    ```

[slide]
## 3.4 Alex SSA遍历
1. 展开CALLSEQ_START, CALLSEQ_END, 这些伪指令; {:&.rollIn}
1. 插入Prologue, Epilogue;
1. 将FrameIndex变成具体的访存操作(load/store)
1. 展开%a = %b这样的指令(load/store)
1. 分配物理寄存器
1. 插入寄存器保存/恢复指令, caller/callee saved

[slide]
## 4. lld
1. 目标: obj文件中操作数是内存地址的指令操作数没有确定, 回填这些地址. {:&.rollIn}
2. 实现: 遍历编译器生成的符号, 有一下四种
    - PCREL16: 相对跳转指令中的相对偏移量
    - ALEX_LO16/ALEX_HI16: 加载32bit地址的两个load指令中的绝对地址
    - ALEX_32: 跳转表中的32bit绝对地址

[slide]
## Repositories
------
- https://github.com/a1exwang/llvm
- https://github.com/a1exwang/lld
- https://github.com/paulzfm/alex-machine
- https://github.com/a1exwang/alex-cpu-test

[slide]

# That's all, thank you!
