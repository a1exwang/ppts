title: Alex-Machine指令集, 虚拟机, 工具链, 和uCore的移植
speaker: 朱俸民, 王奥丞
url: http://soappt.a1ex.wang
transition: cards
files: /js/demo.js,/css/demo.css

[slide]
# Alex-Machine指令集, 虚拟机, 工具连, 和uCore的移植
## - 朱俸民, 王奥丞

[slide]
## 需求/目标
------
- 设计一个简单, 但足够实现一个现代操作系统的CPU指令集 {:&.rollIn}
- 并将uCore移植上去
- 指令集
- 模拟器
- 工具链(compiler, assembler, linker, other tools(objdump)...)
- 调试器

[slide]
## 1. 指令集和模拟器
------

[slide]
## 2. 工具链
------
- 使用LLVM框架 {:&.rollIn}
- 用clang将C/C++编译到LLVM IR
- 编写LLVM后端, 将LLVM IR翻译成Alex-Machine汇编代码/机器码
- 利用lld框架编写链接器
- objdump, disasm
- 全部完成

[slide]
## 2.1 工具链(CodeGen)
- CodeGen
  - LLVM编译过程添加了几个pass, 主要如下
  - 1. LLVM IR DAG -> LLVM IR DAG, 展开一些LLVM IR中很强的指令(比如funcall)
  - 2. LLVM IR DAG -> Alex-Machine DAG
      用llvm-tblgen语言, 按照一定模式匹配LLVM IR的DAG, 替换成Alex-Machine的DAG
  - 3. Alex-Machine DAG伪指令展开
  - 4. 生成符号信息(包括符号类型, 位宽, 属性, 比如PCRel)供linker使用

[slide]
## 2.2 工具链(Assembler/Linker)
- Assembler
    - 简单的基于行的LL(1)文法, 编译汇编代码到和CodeGen统一的内部表示,
      从而复用CodeGen来生成目标文件
- Linker
    - 此时已经知道所有符号的VA, 根据CodeGen生成的符号信息, 回填代码中的地址, 偏移量等

[slide]
## 3. 调试器
------
- 利用clang生成的DWARF格式调试符号 {:&.rollIn}
- 写了一个脚本, 从ELF中提取DWARF树, 保存成JSON, 在模拟器中读取JSON
- 这些信息具体包括(全局符号值, 函数的PC范围, 局部变量的FP偏移, 类型信息等)
- 已完成功能:
    - Debugger shell, 反汇编, 单步调试(汇编, 源码)
    - 查看全局符号和局部变量, 设置PC断点
    - 查看寄存器, 堆栈, 查看内存
- 将来实现的功能: 源码断点, 查看结构体, 查看函数调用栈,
  查看函数调用栈底部函数的局部变量, 修改内存, 寄存器

[slide]
## 4. uCore移植
------
- 目前移植了部分lab1, 包括cprintf
- 中断和内存管理没有实现

[slide]
## 5. 总结, 收获
------
- 了解了编译器后端的实现 {:&.rollIn}
- 在庞大项目上二次开发的经验, 比如如何在几十兆的代码中找没有文档的API用法
- 编译器和调试器如何协调工作
- LLVM框架的设计, 结构

[slide]

# That's all, thank you!
