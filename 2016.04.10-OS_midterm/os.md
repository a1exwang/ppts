title: V9Cpu的工具链
speaker: 朱俸民, 王奥丞
url: http://soappt.a1ex.wang
transition: cards
files: /js/demo.js,/css/demo.css

[slide]
# V9Cpu的工具链
## - 朱俸民, 王奥丞

[slide]
## 需求/目标
------
- 编译器 {:&.rollIn}
- 汇编器
- 链接器

[slide]
## 指令集特点
------
- 基于原v9指令集
- 增加通用寄存器
- 合并功能类似的指令
- 访存仅能通过 Load/Store
- 除了一些类似库函数功能的指令外，属于RISC
- 可归约到含有几十条指令的完备集

[slide]
## 指令集介绍
------
- [文档](https://github.com/paulzfm/v9-cpu/blob/master/doc/is.md)
- 除了系统指令外，其余指令原则上不再变更

[slide]
## 整体结构
------
- 使用LLVM框架 {:&.rollIn}
- 用clang将C/C++编译到LLVM IR
- 编写LLVM后端, 将LLVM IR翻译成V9Cpu汇编代码
- 生成目标文件(elf格式)
- 利用lld框架编写链接器
- objdump, disasm

[slide]
## LLVM后端(LLVM IR翻译)1
------
- 能添加一个后端到LLVM框架中能编译, 运行不崩溃, 而是提示出错信息(完成) {:&.rollIn}
  - 继承TargetMachine, Subtarget(提供目标机基本信息), FrameLowering(函数调用帧生成) {:&.rollIn}
    ISeqDAGToDAG(DAG匹配和翻译), RegisterInfo, InstPrinter等十几个类
- 算数/逻辑指令(完成)
  - +-*/ << >> \& \| ~ {:&.rollIn}
  - LLVM IR DAG的匹配(编写td)
  - 寄存器分配(编写td, 实现C++ RegisterInfo类)

[slide]
## LLVM后端(LLVM IR翻译)2
------
- 目标文件生成 {:&.rollIn}
  - ELFObjectWriter {:&.rollIn}
- 全局变量
- 转移指令(完成)
  - 跳转指令, 条件转移指令 {:&.rollIn}

[slide]
## LLVM后端(LLVM IR翻译)3
------
- 函数调用 {:&.rollIn}
  - LowerFormal(), LowerReturn(), 参数和返回值信息 {:&.rollIn}
  - LowerCall(), 生成函数调用
- 结构体/数组支持
- 汇编器
  - 继承AsmParser类, 覆盖MatchAndEmitInstruction函数

[slide]
## 编译结果
```
  # BB#0:
  	addiu	$sp, $sp, -8
  $tmp0:
  	.cfi_def_cfa_offset 8
  	st	$r0, 4($sp)             # 4-byte Folded Spill
  $tmp1:
  	.cfi_offset 0, -4
  	addiu	$r0, $zero, 1
  	ld	$r0, 4($sp)             # 4-byte Folded Reload
  	addiu	$sp, $sp, 8
  	ret	$r0
  	.set	macro
  	.set	reorder
  	.end	func
  $func_end0:
  	.size	func, ($func_end0)-func
  	.cfi_endproc
```

[slide]
## 链接器
------
- 可以用llvm-ld将LLVM IR链接成一个大文件, {:&.rollIn}
  最后编译成V9Cpu的目标文件(elf),
  最后修改目标文件中的地址,
  添加静态数据段
- 也可以用lld写一个全功能的链接器

[slide]
## 总结
------
- 工作量主要在LLVM IR翻译这一部分 {:&.rollIn}
- Sparc, 3种CPU, ~14K行, 500KiB
- ![file structure](/assets/cpp.png)
- 好处
  - 可以让V9Cpu支持更多语言
  - LLVM比较新, 文档多, 代码易读一些

[slide]
## 阶段目标
------
- 函数内部能编译 {:&.rollIn}
- 支持全局变量, 结构体
- 支持uCore所需的全部c语言子集(对齐, 内联汇编等)
- 链接器
- 调试符号生成, 调试器
- \*反汇编, objdump
- \*C++支持

[slide]

# That's all, thank you!
