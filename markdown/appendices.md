# 附录


## 附录 A：推荐学习的语言


### A.1 Lisp 之路

本节包含我认为专业 lisp 程序员应该研究哪些语言的个人偏见。 当然，学习非 lisp 语言的最佳理由是，
它会给你在 lisp 程序中带来洞察力。

如果你有幸在学其他编程语言之前学会了 lisp，那么恭喜你，你比我们大多数人都更快地学会了 lisp。
如果只学过 lisp，请不要担心：这并没有错过太多其他内容。 其他编程语言都很有吸引力，充其量是编程
知识道路上令人愉快的进站：通往 lisp 的道路。 因为 lisp 倾向于收集和整合所有其他编程思想的优点，
所以 lisp 倾向于拥有一切。 但有时最好的想法并不是首先在 lisp 中实现的。 专业的宏程序员了解
很多的语言。 一旦你成为一个 lisp 程序员，了解一门语言的意义就改变了。 虽然对于大多数程序员来说，
了解一门语言意味着记住一堆语法，但对于 lisp 程序员来说，这意味着知道该语言与 lisp 的关系。

我认为每个程序员都应该研究所有不同类型的语言，即使只是为了能够正确解释为什么 lisp 做得最好。
去吧，追寻你自己的 lisp 之路。


### A.2 C 和 Perl

在 lisp 之后，C 是最需要学习的编程语言。 C 是编程社区的通用语言。 如果不懂 C，你将无法理解
大多数有趣的程序。 当然，C 是一种 Blub 语言，但它是一种简洁明了且实现良好的 Blub 语言，值得
关注。 C 也是学习指针作用域的有用工具，并有助于了解为什么围绕机器的功能和限制设计语言是一个坏
主意。 最后，由于许多现代 Blubs 都源自 C，学习 C 意味着同时学习多种语言。

在 lisp 和 C 之后，Perl 就是最需要学习的编程语言，不仅因为它无与伦比的实用性，还因为它的哲学。
如果必须有一个 Blub 语法——消除宏的可能性——不妨尽可能地扩展它。让我们加入我们能想到的所有可能
的便利和高级用户技巧。如果说 lisp 是带走语法的结果，那么 Perl 就是一直带走语法的结果。除了
它提供的便利之外，Perl 还宣扬了一个非常简单的原则的统一版本：There's More Than One Way To Do It。
语言不应该基于风格，而应该基于一组强大的原语，一旦完全理解，这些原语就会结合在一起产生出乎意料
的惊人代码。使用 Perl 编程提供了寻找和使用编程快捷方式的直觉。普通的 Perl 程序员甚至比普通的
lisp 程序员更了解回指等宏概念，以及它们为何有用。虽然 Perl 是一个 Blub，但它是很棒的 Blub。


### A.3 Lisp 孵化器

Forth 是我最喜欢的编程语言之一。 C 采用了围绕最小公分母计算机体系结构设计语言的方法，而 forth
采用了设计理想抽象机器的方法，以及程序员可以自由地围绕真实机器创造性地实现的强大的元编程系统。
Forth 能够以只有 lisp 能超越的方式进行扩展。 forth 的发明者 Chuck Moore 实际上是 lisp[EVOLUTION-FORTH-HOPL]
的发明者 John McCarthy 的学生。 Forth 来自 lisp。

Smalltalk 是对对象系统应该如何工作的有趣研究。 它的概念重要性和丰富的历史值得研究。 Smalltalk
也可以作为对现代对象系统（如 CLOS、Common Lisp 对象系统）的一个很好的介绍，甚至可以作为如何正确
使用编程语言的一个很好的介绍。 Smalltalk 是我们通往 lisp 之路上的一个有趣且富有启发性的一站。
Smalltalk 的发明者 Alan Kay 告诉我们，lisp 是他的主要灵感之一。 Smalltalk 来自 lisp。

Haskell 是一种令人兴奋的创新语言，它正在塑造人们对编程的看法。 不学习 Haskell 意味着剥夺自己在
当今 Blub 语言中一些最有趣的研究。 但是获得 Haskell 经验的最重要的原因是让自己了解静态类型系统
实际上对高效编程的贡献有多大 —— 甚至像 Haskell 那样不显眼且可扩展的。 当然，如果要证明一元类型
的定理或研究范畴类型理论，花哨的类型系统是必不可少的。 但是对于绝大多数编程来说，这样的类型系统在
纸面上看起来比实际上要好得多。 Haskell 取得了巨大的成功。 事实证明，静态类型是失败的。 忽略静态
类型和中缀语法，Haskell 的许多创新特性值得学习：隔离副作用（isolation of side-effects）、
通过图缩减的惰性求值（lazy evaluation through graph reduction）、事务内存（transactional
memory）等。

Forth、Smalltalk、Haskell 和许多其他语言可以被视为 lisp 孵化器。 Lisp 孵化器试验编程思想，
其中一些已经成熟到足以被 lisp 宏窃取。 条条大路通 lisp。


## 附录 B：不推荐的语言


### B.1 个人观点

> There are only two industries that refer to their
> customers as "users".
> -- Edward Tufte

本附录中的所有内容，甚至整本书，都必须被视为我的意见。 然而，我个人并不认为这个附录是种个人意见，
而是从 lisp 的角度对一些非 lisp 语言哲学及其问题的简要调查。 并不是说用这些语言编写好的代码
是不可能的，也不是说没有聪明的人会用这些语言编程和工作。 对此，我无意冒犯，请多多包含。 这并
不是针对你们的价值观。


### B.2 Blub Central

Blub 语言的出现时间甚至比 lisp 的出现时间还要长。 在 lisp 语言标准化并编写出体面的
lisp 编译器之前，通常确实需要易于实现但对正规编程很差的 Blub 语言。 但是，既然我们的
软件开发能力如此之大，为什么大多数程序员使用少数几种语言，无论出于何种意图和目的，难道
他们都无法进行区分？ 我认为这可以用博弈论来解释。

就像汽车经销店、咖啡馆和大学一样，通常会出现明显的集群效应，其中机构似乎不合理地靠得很近。
如果所有这些机构都均匀分布而不是聚集在一起，那么它们很可能服务于更大的市场，从而各自获得
更大的个人利润。 但是因为这些机构无法合作，而且如果任何一个机构搬到遥远的地方，情况会比
不这样做更糟糕，没有人移动，集聚仍然存在。

编程语言中也有类似的现象——我称之为 Blub Central。 大多数主要编程语言都无法或不愿意
提供新功能，甚至无法改进其基本设计，因为它们忙于尝试像其他主要编程语言一样。 如果编程
语言的设计者和开发者能够合作，结果将是一个更加多样化、功能更加丰富的语言家族，并且找到
一种完全适合你的问题的语言的机会将会增加。 当然，大多数编程语言供应商要么是大公司，要么
是很少合作的学术委员会，所以就有了目前的情况：大多数程序员都被硬塞进了 Blub Central。
因为几乎所有程序员都学习这些 Blub Central 语言，所以从 Blub Central 转移任何一种
语言都意味着失去市场份额。

尽管对于 lisp 程序员来说听起来很奇怪，但对于 Blub Central 语言似乎有合理的经济解释。
全世界的程序员资源被浪费在每项工作上都使用相同的蹩脚工具令人震惊和沮丧。 但幸运的是，
对于世界各地的程序员来说，有一种快速、不间断、单程的离开 Blub Central 的旅程：
Common Lisp 快车。


### B.3 Niche Blub

实际上有数千种编程语言，并不是所有的语言都可以竞争 Blub Central。 当然，还有 Niche
Blubs。 其中一些甚至非常接近提供 lisp 风格的功能集，尽管没有一个拥有像 lisp 这样的宏
系统，这让他们和 Blub 区别开来。

这些语言中的许多都因缺乏可信赖的、稳定的标准以及被误导的哲学而受苦。 通常它们是围绕一个
难以捉摸的、没有明确规定的目标设计的——良好风格的目标。 没有人真正确定这些语言应该去哪里
或应该如何改变。 设计师可以并且确实决定将语言更改为被认为具有更好风格的语言，然后其他
所有人都必须四处更改他们的代码以符合要求。 应该避免强加一种真正的编程风格而不尊重用户的语言。

在 lisp 中，几乎总是有一条客观正确的路要走：如果它使语言更强大，就应该添加它。 如果它
使语言不那么强大，则应将其删除。 Common Lisp 的稳定标准不是放弃语言的结果，而是最近
关于如何使核心语言比现在更好的想法很少。 并不是说它会一直如此，但 Common Lisp 目前
代表了我们对编程语言的知识的顶点。 也就是说，Niche Blubs 当然有自己的位置。 有时，
他们甚至为 lisp 贡献了宝贵的想法。 但是，为什么不给自己一个机会，将 Niche Blubs 实现
为 Common Lisp 上的特定领域语言呢？


## 附录 C：解释器/编译器


### C.1 CMUCL/SBCL

CMUCL/SBCL 系统的历史实际上要早于 Common Lisp。 SBCL 是 CMUCL 的 fork。 在我看来，
CMUCL 是目前可用的最好的 lisp 环境——SBCL 紧随其后。 CMUCL 和 SBCL 都提供了卓越的开发
和生产环境。 这两种实现都包括了不起的 Python2 优化机器代码编译器。 经过相当简单的声明
过程后，使用 Python 编译的 Common Lisp 代码将具有与所有其他语言的编译器相媲美或超过
编译器的性能，尤其是在用于大型和复杂的应用程序时。 CMUCL 和 SBCL 之间最大的区别在于
SBCL 强制使用 Python 来处理所有事情，但 CMUCL 提供了几种编译器和解释器，每种编译器
和解释器都有自己的优点和缺点。

本书中的大多数示例和输出都来自 CMUCL，虽然有时会用 SBCL 是因为其反汇编器输出更好。
CMUCL 有更少的烦人、多余的警告和更舒适的 REPL，但 SBCL 可以有更清晰的注释并且更适合
某些 Unicode 任务。 SBCL 还做出了一些我不认同的设计决策，例如破坏 CMUCL 的 top-level
`setq`功能，对系统符号使用不太明显的名称，以及毫无意义地复制 unix 的 `getopt(3)`。

CMUCL 和 SBCL 享有丰富的库和充满活力的在线用户社区。 听到有人抱怨库的可用性是 lisp 的
一个问题，我感到很奇怪。 我觉得恰恰相反。 与其他语言相比，Common Lisp 库通常有更好的支持
和更高的质量。 CMUCL/SBCL 的外部函数接口也非常出色——比我遇到的任何其他语言都更灵活、
更稳定、更高效。 在 Common Lisp 中开发某些东西时，库来说从来都不是障碍。


### C.2 CLISP

CLISP 是另一个优秀的 Common Lisp 环境。 它比 CMUCL/SBCL 更便携，几乎可以在 C
编译器可以编译到的任何地方运行。 由于 CLISP 不编译为机器码，而是编译为字节码格式，
因此它通常比 CMUCL/SBCL 慢，有时在部署时避免使用。 但如果需要可移植性，部署到
CLISP 有时是最明智的选择。 CLISP 还支持快速的 bignum。


### C.3 Others

ECL 是用 C 语言编写的 Common Lisp 实现，用在嵌入式中。 它所占用的空间大约是
CLISP 的三分之一，但仍然相当完整。 对于内存极度受限的机器来说，这可能是一个很好
的部署环境。 得益于 GMP 库，它还具有良好的 bignum 性能。

GCL 是一个 Common Lisp 实现，它使用 GNU C 编译器通过 C 中介将 lisp 编译为
本机机器代码。 它基于有影响力的 Kyoto CL 系统[KYOTO-CL-REPORT]。 通常不如
CMUCL/SBCL 或 CLISP 好，但仍具有研究价值。

Armed Bear 是运行在 Java 虚拟机之上的 Common Lisp 实现。 它对于与现有的
Java 应用程序集成可能很有用。

Perl 的 Parrot 虚拟机有一个 Common Lisp 前端，我也一直在关注它。

Clozure CL（以前称为 OpenMCL）是 Common Lisp 的开源实现，只要它不在专有
操作系统上运行，应该可以正常工作。

Common Lisp 也有许多专有的实现。 其中一些质量很高，但都存在严重缺陷——阻止你
做事或了解事情的非技术障碍。 考虑到开源 lisps 的质量和数量，如今通常没有必要
将自己锁定在昂贵的、无源代码的实现或受限的免费试用中（可能是在 cue LispWorks）。

当今优秀的开源 Common Lisp 实现与优秀的免费操作系统（如 OpenBSD 和 GNU/Linux）
相结合，正在带来计算的黄金时代。 如今，除了自身的智力和创造力的障碍之外，没有什么
能阻止程序员。


## 附录 D：Lisp 编辑器


### D.1 emacs

> Editing is a rewording activity
> --Alan Perlis

说之前不可能不冒犯别人，所以我就直言不讳：emacs 很差劲。 尽管 emacs 是个非常
灵活且功能强大的编辑器，并具有丰富的历史，但我永远不会使用它。 当然，这是我极少
数的意见。 世界上绝大多数 lisp 程序员都爱上了 emacs 及其 lisp 可编程架构。
显然有一个名为 ILISP 的环境，让你使用编辑器中的 REPL、不通的颜色区分 lisp
语法、根据当前包的内容自动补全 lisp 结构，无论如何，我还是坚持我的看法，我永远
不会使用它。

Emacs 本身包含一个完整的 lisp 环境，称为 elisp。 Elisp 是一种非词法 lisp，
人们在其上构建了大型复杂的 lisp 应用程序。 这些应用程序允许执行所有操作，从自动
修改正在处理的源代码到浏览网络和从编辑器中查看电子邮件。 在编辑时执行任意 lisp
代码的能力非常强大，可以改变你写代码方式，但同样，我还是不用它。

这就是我所说的 emacs 陷阱。 在我刚刚描述的各个方面，emacs 听起来都很棒。 太棒了，
当聪明的程序员尝试它并发现它不是那么棒时（并没有真正帮助他们的编码），他们不知为了
错过了一个明显的结论，即这可能是一个不值得解决的问题，而是开始思考如何追求潜在的
令人敬畏的方法。 emacs 陷阱导致许多聪明的 lisp 程序员浪费了无数时间来配置和记忆
键的绑定映射，调整语法颜色，以及编写大部分无用的 elisp 脚本。

Emacs 让你思考如何编写程序来为你进行编辑，而不是如何编写程序来为你编写程序。 当
正在编辑的代码被冗余编写时，只需要编写编辑过程的脚本。 当完全专注于开发应用程序时，
编辑文件的实际过程应该是个透明的、微不足道的过程，其中的机制甚至从未进入思考过程。
Emacs 不是这样的。 Emacs 有一个又一个的插件，一个又一个的玩具，一个又一个的噱头，
让程序员无休止地玩弄、困惑和陷入困境。有时我会设想更先进的世界可能已经有了发明
emacs 的 lisp 天才，他们忽略了文本编辑这一非问题，而是将全部注意力集中在真正的
编程问题上。

什么是 Blub 风格的集成开发环境？ IDE 通常是大型的、过度设计的 emacs 克隆，甚至
不提供 lisp 定制，呈现所有可用的按钮或菜单供你使用，甚至不如 emacs 有用。 与
emacs 相比，大多数 IDE 是个糟糕概念的糟糕实现——冗余语言的冗余编辑器。

emacs 的问题在于它的倒退哲学。 它将编辑视为目的而不是手段。 编辑本身并不是一个
有趣的过程； 有趣的是我们编辑的内容。


### D.2 vi

Vi 与 emacs 的编辑器截然相反。 它有一组规范的键绑定，所有 vi 用户都普遍理解其中
的子集。 一旦了解了 vi，就可以坐在任何 unix 计算机上，以零知识开销编辑文件。
就像你在家一样。 vi 没有烦人的 emacs 键和键值映射，而是具有强大的模态设计，可透明
且高效地执行简单和复杂的文本操作。 Vi 经过精心设计，以最大限度地减少需要执行的击键
次数，并且即使在高延迟的网络连接上也能舒适地使用。

考虑到 vi 中的 % 命令可以复制/粘贴/删除/- 即移动 lisp 结构、正则表达式以及灵活
的移动和搜索命令，vi 可以降低编辑 lisp 代码的开销。 与 emacs 形成直接对比的是，
vi 键和命令背后的含义永远不会改变。 短时间使用后，vi 成为大脑的原始运动技能部分的
延伸。 能够在脑海中排列两个、三个、四个或更多编辑命令，并在继续思考应用程序时让手
指执行它们，这有点神奇。 与几次击键几乎无法进入某些选项菜单的 IDE 不同，vi 击键
高效、强大、透明，而且最重要的是，恒定不变。

在编写 lisp 代码时，我特别喜欢用 Keith Bostic 的 nvi，它是 4BSD 中 vi 的直接
继承者。 比其他编辑器更快且响应更快，不会因为无用的细节或颜色分散我的注意力，而且
从来没有崩溃过。 我不想再从编辑器那里得到任何东西了。 编辑是 unix 正确而 lisp
错误的少数事情之一——Worse is better。