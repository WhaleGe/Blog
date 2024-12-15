---
title: 如何通俗地理解Family-wise error rate(FWER)和False discovery rate(FDR)
categories:
  - Statistics
tags:
  - statistics
  - knowledge
katex: true
abbrlink: c61694ea
date: 2023-12-05 17:35:47
---

> 本文转载于网络， 原文地址：[如何通俗地理解Family-wise error rate(FWER)和False discovery rate(FDR)](https://blog.csdn.net/shengchaohua163/article/details/86738462)

### 如何通俗地理解Family-wise error rate(FWER)和False discovery rate(FDR)

Family-wise error rate (FWER)，暂时还不了解比较通俗易懂的翻译。False discovery rate(FDR)，一般翻译为错误发现率。在研究使用假设检验解决机器学习中的分类问题时，我遇到了多重假设检验问题。FWER 和 FDR 正是解决这一问题的两种方法。经过老师和师兄师姐的指导，我能够将 FDR 应用于自己的问题当中，并且实验结果也有所改善。但是，应用之后，我仍然存在一些困惑，比如 FWER 和 FDR 到底是什么，FWER 和 FDR 怎么计算，为什么 FWER 和 FDR 能够降低错误率以及如何通俗地理解二者。

为了搞清楚上述问题，我仔细研究了维基百科和网络上的一些资料，认为通俗地理解 FWER 和 FDR 不是一件容易的事。研究者首先需要有一定的统计学假设检验的基础，而且还能够将 FWER 和 FDR 应用于实际的问题。所以，我会从假设检验谈起，然后结合自己的问题进行探讨。

#### 假设检验

假设检验是用于检验统计假设的一种方法。它的基本思想是小概率思想，小概率思想是指小概率事件在一次试验中基本上不会发生。

假设检验的基本方法是提出一个空假设（null hypothesis），也叫做原假设，记作 $H_0$​；然后得出感兴趣的备择假设（alternative hypothesis），记作 $H_1$​ 或 $H_A$​。空假设和备择假设的指导原则是空假设是不感兴趣对研究不重要的结论，而备择假设是我们感兴趣想要证明的结论。举个例子，给定人的一些身体指标数据，判断其是否存在某种疾病（比如肺炎）。

 $H_0$​：某人没病（我们不感兴趣）； $H_1$​​：某人有病（我们感兴趣）。将这些身体指标数据和已确定的或健康或有病的一些人的身体数据等样本信息比较，计算 p 值，一般指定显著性水平 α = 0.05 ，如果 p 值小于 0.05，表示这是一个小概率事件。根据小概率思想，我们与其相信这个小概率事件的发生，不如认为更为合理的选择是拒绝原假设，认为该人有病；否则无法拒绝原假设，即接受原假设，表示没有足够的证据认为该人有病。

注：  

1. 统计显著性：空假设为真的情况下拒绝零假设所要承担的风险水平，又叫概率水平。  
2. p 值：假定空假设为真的情况下，得到相同样本结果或更极端结果的概率，是一个用来衡量统计显著性的重要指标。  
3. 显著性水平 α ：空假设为真时，错误地拒绝空假设的概率。另外，也可以把这种概率理解成在假设检验中决策所面临的风险。  
4. 比起计算 p 值，也可以计算统计量，根据显著性水平判断统计量是否落入拒绝域，进而决定是否拒绝原假设。统计量没有 p 值直观，所以采用 p 值进行表述。

#### 使用假设检验解决分类问题

沿用上一个例子，我们发现假设检验可以很自然地对应机器学习中的二分类问题：空假设为真对应着没病，空假设为假（备择假设为真）对应着有病。

一次假设检验只能检验一个人，如果得到的 p 值小于显著性水平 α 就拒绝空假设，相当于把该人分为有病一类，而且可以称该次检验是显著的（significant）。如果得到的 p 值大于显著性水平 α 就不能拒绝空假设，相当于把该人分为没病一类。类似地，可以称该次检验是不显著的（non-significant）。一般把备择假设中的类别称为正类或阳性（positive），另一个类别称为反类或阴性（negative）。

事实上，一个人可能没病（空假设为真）也可能有病（空假设为假，备择假设为真），一次检验可能被拒绝（显著）也可能不能被拒绝（不显著）。所以，一次检验有四种可能的结果，用下面的表格表示：

<table><thead><tr><th></th><th> H0​​ is true</th><th> H0​​ is false ( H1​​ is true)</th></tr></thead><tbody><tr><td>Reject  H0​​(Test is declared significant)</td><td>Type I error (FP) (probability= α)</td><td>Correct inference (TP) (probability=1- β)</td></tr><tr><td>Fail to reject  H0​​(Test is declared non-significant)</td><td>Correct inference (TN) (probability=1- α)</td><td>Type II error (FN) (probability= β)</td></tr></tbody></table>

*   Type I error，I 类错误，也叫做 α 错误
*   Type II error，II 类错误，也叫做 β 错误
*   FP: false positive，假正例，I 类错误
*   FN: false negative，假反例，II 类错误
*   TP: true positive，真正例
*   TN: true negative，真反例

I 类错误是指空假设为真却被我们拒绝的情况，犯这种错误的概率用 α 表示，所以也称为 α 错误或**弃真错误**；II 类错误是指空假设为假但我们没有拒绝的情况，犯这种错误的概率用 β 表示，所以也称为 β 错误或**取伪错误**。所以，空假设为真并且我们没有拒绝的概率用 1 − α 表示，空假设为假并被我们拒绝的概率用 1 − β 表示。

> 自然，人们希望犯这两类错误的概率越小越好。但对于一定的样本量 n，不能同时做到犯这两类错误的概率都很小。如果减小 α 错误，就会增大犯 β 错误的机会；若减小 β 错误，就会增大犯 α 错误的机会。当然，使 α 和 β 同时减小的办法也有，那就是增大样本量。但样本量不可能没有限制，否则就会使抽样调查失去意义。因此，在假设检验中就有一个对两类错误进行控制的问题。  
>
> 一般来说，哪一类错误所带来的后果严重，危害越大，在假设检验中就应当把哪一类错误作为首要的控制目标。但在假设检验中，大家都在执行这样一个原则，那就是首先控制 α 错误。主要原因有两点，一方面，大家都遵循一个统一的原则，讨论问题比较方便。更主要的原因是，原假设一般是比较明确的，而备择假设常常是模糊的。对于一个含义清楚的假设和一个含义模糊的假设，我们更愿意接受前者。正是在这个背景下，我们就更关心如果原假设为真，而我们却把它拒绝了，犯这种错误的概率有多大。而这正是 α 错误的内容。 
>
> ——《统计学 - 贾俊平》第六版

注：对于机器学习中的二分类问题，其分类结果也可以用类似的表格表示，一般被称为混淆矩阵，而且和上面的表格有些不同。

<table><thead><tr><th></th><th>预测为正类</th><th>预测为反类</th></tr></thead><tbody><tr><td>实际是正类</td><td>真正例（TP）</td><td>假反例（FN）</td></tr><tr><td>实际是反类</td><td>假正例（FP）</td><td>真反例（TN）</td></tr></tbody></table>

#### 多重假设检验和 FWER, FDR

顾名思义，多重假设检验就是多个假设检验。如果有 m 个人需要检查是否有病，那么就需要进行 m 个假设检验。 m 个假设检验的结果可以表示为：

<table><thead><tr><th></th><th> H0​ is true</th><th> H0​ is false ( H1​ is true)</th><th>Total</th></tr></thead><tbody><tr><td>Reject H0​(Test is declared significant)</td><td>V</td><td>S</td><td>R</td></tr><tr><td>Fail to reject H0​(Test is declared non-significant)</td><td>U</td><td>T</td><td>m−R</td></tr><tr><td>Total</td><td>m0​</td><td>m−m0​</td><td>m</td></tr></tbody></table>

*   m 表示假设检验的个数
*   m0​ 表示空假设为真的个数
*   m−m0​ 表示备择假设为真的个数
*   V  表示假正例的个数
*   S 表示真正例的个数
*   U 表示真反例的个数
*   T 表示假反例的个数
*   R = V + S 表示拒绝空假设的个数

如果某次假设检验得到的 p 值小于显著性水平 α ，则拒绝空假设，主观上认为发现了一个有病的人（无论该人实际上真有病还是假有病），这种情况记为一次发现（discovery）。所以 R = V + S 表示发现的个数， V 表示错误发现（false discovery）的个数， S 表示正确发现（true discovery）的个数。用 Q 表示发现中错误发现的比例，即 Q = V / R = V / ( V + S ) 。 FWER 定义为 V 大于等于 1 的概率，即 FWER = Pr{ V ≥ 1 } = 1 − Pr{ V = 0 } 。FDR 定义为 Q 的期望，即 FDR = E[Q]。

因为在 m 个检验中， V,S,U,T 都是随机变量，所以 FDR 需要用期望的形式来表示。另外，如果R=0，认为Q=0。为了包含这种情况，FDR=E[V/R∣R>0]⋅P{R>0}。通俗地理解，可以认为 FDR=Q=V/R=V/(V+S)。

FWER 和 FDR 表示一种概念或一种方法，FWER 定义为多重假设检验中发现至少一个 I 类错误的概率，FDR 定义为多重假设检验中错误发现占所有发现的比例。另外，对应地，还存在 FWER 校正方法和 FDR 校正方法（也称为控制方法）。两类校正方法都是用来控制多重假设检验中犯 I 类错误的概率，使其低于显著性水平 α 。FWER 校正有多种实现，其中最经典的是 Bonferroni correction；FDR 校正也有多种实现，其中最经典的就是 Benjamini–Hochberg procedure。

#### 为什么要使用 FWER 和 FDR 校正

在一次假设检验中，我们使用显著性水平 α 和 p 值得出结论。显著性水平 α 一般取 0.05 或 0.01，可以保证一次假设检验中犯 I 类错误的概率和决策错误的风险小于 α 。

但是在 m 次假设检验中，假设 m=100 和 α = 0.01 ，假设检验之间相互独立，不犯错误的概率为  $(1-0.01)^{100}$=36.6% ，而至少犯一次错误的概率高达$P=1-(1-0.01)^{100}$=1-0.366=63.4% 。举个实际的例子，假如有一种诊断艾滋病的试剂，试验验证其准确性为 99%（每 100 次诊断就有一次 false positive）。对于一个被检测的人来说（single test），这种准确性足够了。但对于医院来说（multiple test），这种准确性远远不够，因为每诊断 10000 个人，就会有 100 个非艾滋病病人被误诊为艾滋病。这显然是不能接受的。所以，对于多重检验，如果不进行任何控制，犯一类错误的概率便会随着假设检验的个数迅速增加。

FWER 和 FDR 校正都可以使多重假设检验整体犯 I 类错误的概率低于预先设定的显著性水平 α 。FWER 显得较为保守，它主要是依靠减少假阳性的个数，同时也会减少 TDR(true discovery rate)。而 FDR 方法是一种更加新颖靠谱的方法，它会对每个测试用例使用校正后的 p 值（ q  值），达到了更好的效果：在检验出尽可能多的阳性结果的同时将错误发现率控制在可以接受的范围。

#### 如何使用 FWER 和 FDR 校正

注：FWER 校正和 FDR 校正均有多种实现，这里只介绍两类方法的经典实现。

条件：在m次多重假设检验中，每一次的空假设记为$H_1$,$H_2$​,...,$H_m$​，对应 p 值记为$p_1$,$p_2$​,...,$p_m$​，设定显著性水平α。

Bonferroni correction，一般翻译为邦费罗尼校正。只要  $p_i$ ≤ $\frac{α}{m}$，就拒绝 $H_i$。可以看出，Bonferroni correction 通过把显著性水平降低到$\frac{α}{m}$。FWER 为发现至少一个 I 类错误的概率，即FWER=Pr{V≥1}，可证:

$$
FWER=Pr\{\bigcup_{i=1}^{m_0}(p_i\leq\frac{\alpha}{m})\}\leq\sum_{i=1}^{m_0}\{Pr(p_i\leq\frac{\alpha}{m})\}=m_0\frac{\alpha}{m}\leq m\frac{\alpha}{m}=\alpha.
$$
这样就能控制多重假设检验整体犯 I 类错误的概率低于预先设定的显著性水平 α 。另外，FWER 控制不需要假设所有假设彼此之间相互独立，也不需要对空假设为真的个数有要求。

Benjamini–Hochberg procedure，简称为 BH。首先对所有的 p 值从小到大排序，并记作$p_1$,$p_2$,...,$p_m$，其对应的空假设为$H_1$,$H_2$,...,$H_m$。若想控制 FDR 不超过 α ，需要找到最大的正整数 k，使得$P_{(k)}≤\frac{k \ast \alpha}{m}$【公式 1】。然后，拒绝 1≤i≤k 时的所有空假设$H_1$,$H_2$,...,$H_k$。可以看出，BH 相当于对排序后的假设提供了不同的显著性水平。FDR 为 Q 的期望，即 FDR=E[Q]，可证：
$$FDR=E[Q]\leq\frac{m_0}{m}α\leqα$$
这样就能从统计学上保证 FDR 不超过 α ，保证多重假设检验整体犯 I 类错误的概率低于预先设定的显著性水平 α 。另外，BH 是有效的条件是要求 m 个检验是相互独立的。

最后再说一下 q  值 ( q -value)， $q=\frac{P_{(k)} \ast m}{K}$，相当于对【公式 1】进行变换，一般把 q  值叫做校正后的 p 值。至于 q  值有什么好处，我还没太搞明白。我自己研究的东西已经不需要更深入了，所以写到这里就结束了。

参考资料：

*   [False discovery rate - Wikipedia](https://en.wikipedia.org/wiki/False_discovery_rate)
*   [Family-wise error rate - Wikipedia](https://en.wikipedia.org/wiki/Family-wise_error_rate)
*   [如何通俗地解释错误发现率 FDR(false discovery rate)？](https://www.zhihu.com/question/35606197)
*   [多重检验中的 FDR(false discovery rate, 错误发现率) 是什么？](http://sofasofa.io/forum_main_post.php?postid=1000461&)
*   [生物信息学中多重检验问题的一个例子（FDR、p 值、q 值）](http://wap.sciencenet.cn/home.php?mod=space&uid=2827057&do=blog&id=968037)
*   [p-value&FPR 以及 q-value&FDR](https://blog.csdn.net/sixu_9days/article/details/78992642)
*   [多重假设检验校正为什么有效？](https://blog.csdn.net/a353833082/article/details/45530061)
*   [《统计学 - 贾俊平》第六版](https://book.douban.com/subject/26375999/)
