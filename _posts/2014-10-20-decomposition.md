---
published: true
title: Bias-Variance Decomposition
layout: post
---
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
	  tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}
});
</script>

<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>


＜script type="text/x-mathjax-config"＞ MathJax.Hub.Config({ TeX: {equationNumbers: { autoNumber: ["AMS"], useLabelIds: true}}, "HTML-CSS": {linebreaks: {automatic: true}}, SVG: {linebreaks: {automatic: true}} }); ＜/script＞

##1. 偏置 与 方差

所谓偏置与方差是统计学和机器学习中两种类型错误的来源：
  

 - 偏置：学习到的模型的预测值与数据的真实值之间的差异，也就是说偏置衡量了学习到的模型的准确程度。
 - 方差：是衡量模型对于不同数据预测的稳定性。如果模型方差较高，也就是说，由于这个样本数据noise较少，模型能给出很好的预测结果 。但如果对于另一组噪声较多的样本数据，模型的预测结果就可能比较差。

理想情况下，我们会选择一个即能对训练数据有很好的预测，又能对新的数据有很好的泛化能力。即同时使最小化偏置和方差，但这是很难同时做到的。高方差（High-vairance）往往导致模型过拟合(overfitting), 也就是模型能很好的拟合训练集，但是对于测试集却不能很好的拟合。而低偏置（low-bias）的学习算法往往是一个简单的模型，以至于简单到不能fit训练集，也就是underfit。**Bias-Vairance tradeoff** 就是解决这个问题的一种方法。它将一个学习算法的expected generalization error解为三部分之和： 偏置， 方差 和由于噪声引起的错误 。

##2. 偏置-方差 分解
假设有训练集$\{(x_1, y_1), (x_2, y_2), ..., (x_n, y_n)\}$， 假设$y_i = f(x_i) + \epsilon$,其中$\epsilon$ 是数据噪声，其均值为0,方差为$\delta^{2}$. 我们目标是从训练集中学习一个$\hat{y} = \hat{f} (x)$, 该函数近似于数据的真实函数$y = f(x)$。
用最小化平方差均值[（Mean Squared  Error）](http://en.wikipedia.org/wiki/Mean_squared_error)的方法求得目标函数, 即 最小化  $E[(y-\hat{f}(x))^{2}] $。由于数据$y$包含了噪声， 所以我们得到的目标函数总会有不可避免的错误。因此，方差均值可以分解为如下： 



$$E[(y-\hat{f}(x))^{2}  = E[(f(x)- E(\hat{f}(x)))^2] + E[(\hat{f}(x) - E(\hat{f}
(x)))^2] + E(\epsilon^2)   \\ 
 = Bias(\hat{f}(x))^2 + Var(\hat{f}(x)) + Var(\epsilon) \\ = Bias(\hat{f}(x))^2 + Var(\hat{f}(x)) + \delta^2   $$ .

为了简单，现另$f = f(x), { }\hat{f} = \hat{f}(x)$, 证明如下：

$$
\begin{displaymath}


\end{displaymath}
$$
