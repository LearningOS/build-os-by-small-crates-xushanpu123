﻿# 模块化的 rCore-Tutorial

- 只需要维护一个分支！
- 支持 Windows 直接调试！

  > - 需要安装 Rust 环境和 Windows Qemu
  > - 当然 Linux 也支持，其实就是跨平台

- 点击[模块化的 rCore-Tutorial-v3: GitHub Classroom](https://classroom.github.com/a/Oc792plB)，寻找共同兴趣的小伙伴，一起学习训练！

  > 如果想找学习小组，请联系助教 @许善朴 (微信id: bitmeet520) 加入微信群。
  > 如果有问题，鼓励在 https://github.com/LearningOS/rCore-Tutorial-in-single-workspace/issues 上提交。

## 目录

- [模块化的 rCore-Tutorial](#模块化的-rcore-tutorial)
  - [目录](#目录)
  - [宗旨](#宗旨)
    - [史书的另一种形态](#史书的另一种形态)
    - [协作政策](#协作政策)
  - [命令参考](#命令参考)
  - [设计说明](#设计说明)
  - [进度计划](#进度计划)

## 宗旨

本项目目标是实现一个存在于单独仓库、单独分支的完整操作系统教程。

“一个操作系统教程”定义为由一系列章节组成的，用于学习操作系统的文本和代码资料。用户可按照自然的顺序阅读这些章节，逐步了解操作系统的发展史和原理性的技术细节。因此，在本文的后续部分，将用户称为学生，将使用过程称为学习。

我假设学生在学习本教程前对 Rust 语言有充分的了解，因为我希望在编写时尽量使用 Rust 带来的方便，不必受受众的掣肘。

我希望，学生在学习本教程时可以专注于 Rust 和操作系统本身。因此，教程不要求用户精通操作 git，`git clone` 只是获取教程的手段之一，与下载 zip 或从他人处拷贝没有本质区别（当然，版本管理总是有益的）；并且会尽量跨平台，为此，可能需要在构建系统中增加一些平台相关的操作。

本教程**并不期望在章节设置或教学内容上有所创新**，也不是为了实验任何新的内核技术，rCore-Tutorial-v3 足够优秀。本项目仅关注**教程的构建过程和学生的学习体验**（换句话说，如果为了这两点，修改章节设置和教学内容也可以接受）。在此基础之上，我希望尽量使用 Rust 的机制减少“构造一个操作系统”的代码量，以及使得库构建规范化、可迁移。

### 史书的另一种形态

rCore-Tutorial-v3 的教材部分别出心裁地为每一章取了一种古生物的名字，将操作系统的发展史和动物的演化史联系起来，为常常枯燥的教材带来了新奇的趣味。随着每个章节，学生可以了解到一个现代内核，是如何从蒙昧年代一步步发展来的。

阅读教材会带来快乐——收获知识、进步的快乐；阅读历史也会带来快乐——带入那些天才人物、风云故事的快乐。然而，一旦将教材与一部真正的史书相比，就会发现我们似乎缺了什么。从古至今、由简至繁——这是一部编年史。编年史直白、清晰，利于建立概念，然而却不利于理解一个复杂的人物。对于操作系统来说，就是不利于理解一个真正的现代内核子系统的细节。子系统的功能常常限于相关其他子系统尚不存在而无法完整介绍；而增加一个子系统时，又需要修改以前的子系统来适应新部件的功能。复杂的系统，要么不得不多次修改，要么只能放在后面的章节，这导致教程书可以是极好的入门，但却无法常读常新。

幸好，历史已经告诉我们另外的选择。编年史是历史研究的重要工具，但堪称“史家之绝唱”的还是纪传体。每次将视角聚焦到一个重要的人物，将他的经历作为线索，往往使历史更丰满。或许我们需要另一部纪传体作品，通过无序的介绍内核的各个子模块，为离开了蹒跚学步的内核研究者带来一部更有效的工具书。这样一本工具书，必然天然需要模块化——模块就是章节本身。

本项目仍然聚焦一个更好的 Tutorial。但其模块化的经验有助于另外的作品。如果有这样一个项目，可以称为 rCore-Manual，它是一本手册，供操作系统研究者随时翻阅。正如 rCore-Tutorial 不需要介绍原始的 rCore，它们是 rCore 的精神续作。

### 协作政策

本项目**不是、也无意成为相关工作的典范**。如果你：

- 认为项目的操作非常合适，欢迎共同开发；
- 你不喜欢某些更新，请发起讨论，或从任何提交分叉；
- 认同项目的目标但不认同其设计，可以创建己的仓库；

因此，本项目使用 [WTFPL](LICENSE) 开源，如果你不喜欢，可以改名字或在你的分叉里直接删除它，这都是它本身支持的。

另外，本项目具有实验性。这意味着它随时可能发生设计变更、破坏性重构、移动、废弃或删除。

## 命令参考

> 下列命令在本文档所在目录及其所有子目录中运行都产生一样的效果，因此没必要 cd 到某处

- **cargo qemu --ch `n`**

  在 qemu 运行第 `n` 章的操作系统。

- **cargo qemu --ch `n` --lab**

  在 qemu 运行第 `n` 章的操作系统实验。

## 设计说明

我对 [workspace](Cargo.toml) 中的 crate 做以下分类：

- **程序**
  - **内核**

    能编译出内核。显然教程每章应该编译出一个内核，但所有章节都在一个 bin crate，或者每章/实验都在独立的 bin crate，或者分成一些组？还不确定。

  - **测例**

    教程中出现不编译出内核的 bin crate 的唯一理由就是需要对内核进行测试。

    - **内核测例**

      运行在内核态，测试某些组件，或者说是不能带用户程序的特殊内核（不一定最后会有）。

    - **用户测例**

      能运行在某些章节内核中的用户程序。

  - **工具**

    运行在开发环境的应用程序，比如 [xtask](xtask)。

- **库**
  - **规范**

    根据某种成文规范实现的库，类似 [riscv](https://github.com/rust-embedded/riscv)。规范库可能是接口库也可能是实现库，以正确反映规范文本为佳。这类库应该在自述文件中给出依据的规范文本（的链接），尤其是要指定规范版本以及当前实现的完整性。

    这类库具有最大的迁移性，仅取决于其参照的规范文本的迁移性。如果完善到一定程度，可以独立到专用仓库并发布。

  - **接口**

    这种库用于规范实现，方便在不同内核之间迁移。一旦最终决定有多个编译出内核的 bin crate，必然存在使实现在它们之间迁移的接口库。

  - **实现**

    普通的 lib crate。如果对同一功能可能有多种实现，可能使用 cfg feature 或抽象出接口库。

## 进度计划

- [x] 完成第一章的crated os
- [ ] 阅读[Theseus OS Intro Doc](https://www.theseus-os.com/Theseus/book/index.html)，了解Theseus OS的基本想法和设计/实践方法
- [ ] 阅读[Theseus OS API Doc](https://www.theseus-os.com/Theseus/doc/___Theseus_Crates___/index.html)，了解一个OS中的各个crates基本功能和接口
- [ ] 完成第一章的crated os
- [ ] 完成第二章的crated os
- [ ] 完成第三章的crated os
- [ ] 完成第四章的crated os
- [ ] 完成第五章的crated os
- [ ] 完成第六章的crated os
- [ ] 完成第七章的crated os
- [ ] 完成第八章的crated os
- [ ] 完成第九章的crated os

注：鼓励分析其他同学的代码，提出自己的想法，相互学习讨论。