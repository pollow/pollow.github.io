title: [Meeting C++ 2015] Compiler Optimization
date: 2016-04-22
tags: cpp, compiler

看到有人分享了一个 Cpp Conference 的[视频](https://www.youtube.com/watch?v=FnGCDLhaxKU)，Chandler Carruth 的分享会议，
讲解了LLVM对Cpp的优化操作。其实内容不仅Cpp，而是Optimizer的泛用策略，记一点笔记。

## LLVM IR

LLVM的中间表示语言。大部分的中间表示语言都是[SSA](https://www.wikiwand.com/en/Static_single_assignment_form)。LLVM IR
是纯SSA，而GCC则支持一些非SSA的语法。使用这种语义主要为前端提供方便。

由于使用了SSA，所以在处理分支和迭代的时候需要特殊的指令支持，比如phi指令。

## Clean Up

SSA带来的语言十分clutter，所以需要进一步 clean up 方便后面的phase做分析，以及频繁的内存读取操作也可以被简化。

## Concatulation

同样的简单语义可以有不同的表达法（比如比较大小等），但是优化器不能针对每一种不同的写法设置优化，所以需要先将不同的表达归化
为同样的结构，方便分析优化。

## Abstraction Collapse

### inlining function call

This is the single most important optimizaiton in modern computer.

A lot of variaty function (adapter) 

Bottom-up 

#### Complexity 

measure the complexity of functions.

### Memory Abstraction Collapse

Partition things. 当在内存中声明一个新的Struct，并且使用了其中的一些变量的时候，可以通过分析确定需要的变量的信息，
从而避免声明这个Struct而是直接操作这些变量。（isolating）

## Loops

没听懂，下次再听一下







Context Available



