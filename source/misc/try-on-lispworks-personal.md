# 初试 LispWorks

今天在 Cookbook 上突然发现 LispWorks Personal 可以免费使用，然后就去下载尝试了下，然后
我只想说一句：LispWorks Personal Edition SUCKS。这个免费版的就是个样子货，或者说是用
其他人的说法就是 —— LispWorks 就是 Common Lisp 的商业化产物。

第一点是 Personal Edition 还是 2013 年发布的，这个其实都没什么太大的影响，毕竟可以说是
为了稳定性嘛。第二点，个人版不可以保存配置，而且 LispWorks 默认没有集成 Slime，也没有
quicklisp。这点我也是可以克服的，可以自己写个手动的 lisp 文件，对这些进行加载，顶多是启
动时需要多做一步加载那个文件。但是，最让我无语的是，他竟然对内存堆栈的使用有限制，而且限制
很大，我加载了 quicklisp 和 slime 之后，然后用 ql:quickload 一个 package 之后，就提
示我堆栈达上限，然后就要要我保存文件给退出了。这，我刚开始下载时看到了对比，知道是有限制，
万万没想到的是，限制这么严重，完全没有用户体验。

唉，果然，就不该对商业话的免费版存有什么希望，但是商业版和专业版又贵的死，好像要一千多刀。
这，反正以我现在的能力是顶不住，有钱我也先把 Cubase 给买了。所以说，之后 Lisp 开发我选择
Emacs + Slime + SBCL。
