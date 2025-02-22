---
layout: post
title:  "Foundationsof theTheory of Probability 中文翻译"
date:   2025-02-22 23:05:53 +0800
categories: 翻译
---

<!-- PDF显示区域 -->
<div id="pdf-container"></div>

<!-- 翻页按钮 -->
<button id="prev-page">上一页</button>
<button id="next-page">下一页</button>

<!-- 引入 PDF.js -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/pdf.js/2.10.377/pdf.min.js"></script>

<script>
  const url = '/assets/pdf/foundations_of_probability_trans.pdf'; // PDF 文件路径

  let pdfDoc = null;
  let currentPage = 1; // 当前页
  let totalPages = 0;

  // PDF.js 加载文档
  const loadingTask = pdfjsLib.getDocument(url);
  loadingTask.promise.then(pdf => {
    pdfDoc = pdf;
    totalPages = pdf.numPages;
    console.log(`PDF loaded. Total pages: ${totalPages}`);

    // 初始渲染第一页
    renderPage(currentPage);
  });

  // 渲染指定页
  function renderPage(pageNum) {
    pdfDoc.getPage(pageNum).then(page => {
      const scale = 1.5; // 缩放比例
      const viewport = page.getViewport({ scale: scale });

      // 创建 canvas 元素来渲染页面
      const canvas = document.createElement('canvas');
      canvas.id = `pdf-canvas-page-${pageNum}`;
      const context = canvas.getContext('2d');
      canvas.width = viewport.width;
      canvas.height = viewport.height;

      // 渲染 PDF 页面到 canvas
      const renderContext = {
        canvasContext: context,
        viewport: viewport
      };

      page.render(renderContext).promise.then(() => {
        // 页面渲染完成后，将 canvas 插入到容器中
        document.getElementById('pdf-container').innerHTML = ''; // 清空容器
        document.getElementById('pdf-container').appendChild(canvas);
      });
    });
  }

  // 翻到上一页
  document.getElementById('prev-page').addEventListener('click', () => {
    if (currentPage > 1) {
      currentPage--;
      renderPage(currentPage);
    }
  });

  // 翻到下一页
  document.getElementById('next-page').addEventListener('click', () => {
    if (currentPage < totalPages) {
      currentPage++;
      renderPage(currentPage);
    }
  });
</script>


# 概率理论基础

根据英文第二版翻译。

作者：Andrey Nikolaevich Kolmogorov

英文版本译者：Nathan Morrison

作者小传：A.T. Bharucha-Reid

ISBN：9780486821597

[TOC]



## 编者注

在准备Kolmogorov（柯尔莫戈洛夫）教授这项基础工作的英文翻译稿的过程中，我们参考了1933年发表在《Ergebnisse Der Mathematik》上的德文原版专题论文**Grundbegriffe der Wahrscheinlichkeitrechnung**（概率论的基本概念），以及1936年出版的俄文译本（由G. M. Bavli翻译）。

编者感谢他两位朋友和他侄女的帮助。

编者感谢 Roy Kuebler 提供的自己由德文翻译的英文版本。

## 前言

本专题论文的目的在于，给概率理论以公理化的基础。作者给自己设定的任务是，将概率论的基本概念（直到最近[^0.1]这些概念还被认为是相当奇怪的概念）置于现代数学的一般概念之中。

如果不先介绍 Lebesgue（勒贝格）关于测度和积分的理论，以上任务几乎是无法完成的。在 Lebesgue 发表他的研究成果之后，集合的度量与事件的概率之间的联系，以及函数的积分与随机变量的数学期望之间的联系，变得显而易见。这些联系为进一步的推广提供了可能，例如，独立随机变量的各种性质被看作与正交函数的相应性质完全类似。但如果要将概率论置于以上的可类比的联系的基础之上，仍然有必要使度量和积分理论独立于勒贝格所突出的几何元素。这项工作由 Fréchet 完成[^2]。

虽然基于上述一般观点的概率论理论在某些数学家中已经存在了一段时间，但缺乏一部完整的、不依赖外部复杂概念的系统性阐述。（参考文献中的 Fréchet 的著作 [^0.2]，给出了一个比较完整的论述。）

我想请大家注意那些超出了专家们早已熟知的以上提到的内容，包括：

1.   有限维空间中的概率分布（第三章，第 4 节）；
2.   对某个参数的数学期望的微分和积分（第四章，第 5 节）；
3.   条件概率和条件期望（第五章）；

这些新问题是随着一些完全具体的物理问题产生的[^0.3]。

第六章包含了对 A. Khinchine 和作者关于普通和强大数定律适用性限制的一些结果的概述，但没有提供证明。参考文献中列举了一些近期的作品。

作者对 Khinchine 的致谢，后者通读全文后给予了一些改进建议。

Kljasma near Moscow

1933年复活节

[^0.1]: 原始德文版本发表于1933年。
[^0.2]: 暂不确定此人是谁。
[^0.3]: 原文脚注1，Zur Statistik der kontinuierlichen Systeme und des zeitlichen Verlaufes der physikalischen Vorgange. Phys. Jour, of the USSR, Vol. 3, 1933, pp. 35-63. 标题英文为 *On the statistics of continuous systems and the temporal evolution of physical processes*.



## 初等概率论

初等概率理论，定义为：

>   研究有限个（随机）事件的概率的理论。

当然，在加入一些必要的新原则之后，这一理论也可用于研究无限个随机变量的问题。第二章将会介绍研究无限个随机变量概率的数学理论时用到的一个公理（公理六）。

作为一门数学分支的概率理论，可以，并且应该像几何和代数一样由一些基本公理构建起来。这意味着，在我们定义好研究的基本要素和它们的基本关系，并且定义好规定这些关系的公理之后，所有进一步的阐述都必须完全基于这些公理，而与这些基本要素及其关系的具体的现实含义无关。

在第一节中，概率域（field of probabilities）定义为满足一些特定条件的集合系统。在纯数学角度发展概率理论的过程中，集合中元素的具体含义是什么，一点都不重要（参考希尔伯特（Hilbert）的 *Foundations of Geometry* 一书中对于基本几何概念的介绍，或者抽象代数中对于群、环和域的定义）。

众所周知，每一个公理化（抽象）理论，除了直接产生它的现实世界的物理域景，都还可以有无数个现实世界中的其他的物理域景（应用）。因此，我们发现了概率论在科学领域中的一些应用，这些应用与**随机事件**和**概率**这两个词的严格含义没有关系。

根据选择的公理不同，以及基本概念和概念之间关联的不同，概率理论的公设基础可以采用不同的方式构建。但如果我们的目标是在公理系统和概率理论未来发展这两方面都达到最大程度的简洁，那么**随机变量**及**概率**这两个基本公设性质的概念似乎是最佳的选择。概率论也有其他的公设系统，尤其在一些公设系统中，**概率**并不被当作基本概念，而是在其他概念的基础上得出的[^1.1]。构建这类公设系统的目的是把数学理论与概率理论的经验发展（empirical development）尽可能紧密地结合起来。

>   empirical 用来描述基于观察、实验或经验得出的事实或结论。它强调了通过实证研究或实践经验获得的知识，而不是仅仅依靠理论推导或逻辑推理。在科学、社会科学和哲学等领域中，“empirical”通常指的是基于实际观察和实验的数据或现象，以区别于理论或假设。



### 公理 [^1.2]

假设 $E$ 是一组元素 $\xi$, $\eta$, $\zeta$, ... 的集合，集合中的元素称为**基本事件**（elementary events）。$\mathcal{E}$ 是集合 $E$ 的子集的集合，那么集合 $\mathcal{E}$​​ 中的元素称为**随机变量**（random events）。

**公理**

I. $\mathcal{E}$​ 是一个集合的域（a field of sets）；

II. $\mathcal{E}$ 包含集合 $E$ [^1.3]​；

III. 对于集合 $\mathcal{E}$ 中的任意一个集合 $A$，赋予它一个非负实数 $\mathrm{P}(A)$。这个实数 $\mathrm{P}(A)$ 称为事件 $A$ 的概率。

IV. $\mathrm{P}(E) = 1$。

V. 如果集合 $A$ 和集合 $B$ 没有公共的元素，则
$$
\mathrm{P}(A + B) = \mathrm{P}(A) + \mathrm{P}(B)
$$
一个集合系统 $\mathcal{E}$，加上确定的作为概率的实数 $\mathrm{P}(A)$​​，当它们满足公理 1-5 时，就称为一个**概率域**（a field of probability）。

由下面的例子可以证明，公理系统I-V是一致的（consistent）。假设集合$E$ 包含一个元素 $\xi$，且集合 $\mathcal{E}$ 包括集合 $E$ 和空集 $\empty$，则：
$$
\mathrm{P}(E) = 1 \\
\mathrm{P}(\empty) = 0
$$
但是以上公理系统是不完备的（complete），因为在概率理论的各种问题中，需要考虑不同的概率域。

**构造概率域**

按以下方式构造一个最简单的概率域：

给定任意一个有限集合$E = \{\xi_1, \xi_2, ..., \xi_k \}$，以及任意一个非负实数的集合 $\{p_1, p_2, ..., p_k\}$，后者满足约束$p_1 + p_2 + ... + p_k = 1$。$\mathcal{E}$ 为所有集合 $E$ 的子集组成的集合，则有：
$$
\mathrm{P}\{\xi_{i1}, \xi_{i2}, ..., \xi_{i \lambda}\} = p_{i1} + p_{i2} + \dots + p_{i \lambda}
$$
以上公式中，$p_1, p_2,..., p_k$ 称为基本事件 $\xi_1, \xi_2, ..., \xi_k$ 的概率，或简称为基本概率（elementary probabilities）。用这种方式，可以推导出所有可能的有限概率域。在这个概率域中， $\mathcal{E}$ 由所有集合 $E$ 的子集组成。当集合 $E$ 为有限集合时，这样产生的概率域称为是有限的。关于概率域更多的例子，参考第二章第三节。

### 与实验数据的关系[^1.4]

我们用以下的方式把概率理论应用于现实世界：

1.   假设有一个条件复合体（a complex of conditions） $\mathcal{E}$，其中的条件全部确定时，我们称为一次实现；这里允许发生多次实现，即允许这个条件复合体中的条件重复实现多次（可能出现不同结果）。

2.   我们研究一组明确定义的事件，每当条件 $\mathcal{E}$ 中的条件确定时，这一组事件就会发生。对于每一组（或许是不同）的确定条件，不同的事件发生。假设 $E$ 是所有给定的事件变量（给每一个事件赋予一个变量） $\xi_1, \xi_2, ...$ 的集合，集合中的一些变量可能永远也不会发生。我们利用先验知识，尽可能多地在集合 $E$ 中包含可能的变量。

3.   如果在条件实现时所发生的事件变量属于集合 $A$（以任何方式定义），那么我们称事件 $A$​ 已经发生。

     例子：假设条件复合体 $\mathcal{E}$ 是扔一枚硬币 2 次，则第 2 段提到的事件集合包括每次抛硬币时可能出现正面或反面的情况。由此得出，只可能有 4 种事件变量（随机事件），分别为：正正、正反、反正、反反。如果事件 $A$ 表示**重复结果发生**，则它包括正正和反反两种基本事件。这样，**每一个事件都可以视为随机事件的集合**。

4.   在某些条件下（这里不会讨论），我们可以假设对于在条件 $\mathcal{E}$  下可能发生或可能不发生的事件 $A$，给它分配一个实数 $\mathrm{P}(A)$，这个实数具有以下特征：

     a）可以确定，当条件复合体 $\mathcal{E}$ 中的条件重复足够多次数 $n$ 时，如果 $m$ 表示事件$A$ 发生的次数，则 $m/n$ 与 $\mathrm{P}(A)$ 的差别会非常小。

     b）如果 $\mathrm{P}(A)$ 很小，则几乎可以确定，当条件只重复一次时事件 $A$ 不会发生。

**从经验角度推导公理**

一般来说，我们会假设观测到的事件 $A, B, C, ...$组成的系统 $\mathcal{E}$ 构成一个域，其中每个事件都被赋予了一个确定的概率。这个事件系统 $\mathcal{E}$ 包含了集合 $E$ （公理 I, II，以及公理 III 的第一部分，假定概率的存在）。很显然 $0 \le m/n \le 1$，因此自然推导出公理 III 的第二部分。对于事件 $E$，$m$ 总是等于 $n$，因此自然推导出 $\mathrm{P}(E) = 1 $（公理 IV）。最后，如果集合 $A$ 与集合 $B$ 没有重叠（不兼容），则：
$$
m = m_1 + m_2
$$
其中 $m_1$，$m_2$ 分别表示事件 $A$，$B$ 发生的次数。由此：
$$
\frac{m}{n} = \frac{m_1}{n} + \frac{m_2}{n}
$$
因此可以推出$\mathrm{P}(A+B) = \mathrm{P}(A) + \mathrm{P}(B)$ （公理 V）。

**备注 1**

如果两个独立的陈述各自都是实际可靠的，那么我们可以说它们同时都是可靠的，尽管可靠程度在这个过程中稍微降低了。然而，如果这样的陈述的数量很大，那么从每一个的实际可靠性中就不能推断出它们是否全部同时正确【不知所云】。

【chatgpt的解释】

这段话探讨了关于多个陈述同时可靠性的问题。首先，它指出，如果两个独立的陈述都是可靠的，那么我们可以认为它们同时都是可靠的，尽管它们的可靠程度可能会在这个过程中略微降低。这是因为独立事件的同时发生概率通常低于单个事件的发生概率。但是，当涉及到大量的陈述时，情况就不同了。作者指出，即使每个陈述单独看都是可靠的，我们不能从这些陈述的可靠性推断出它们全部同时正确的可能性。这是因为随着事件数量的增加，出现任何一种错误的可能性也在增加，尤其是在大量事件中。因此，即使每个单独的测试中的结果都接近预期的概率，但当测试次数增多时，某些测试可能会出现与预期结果有所偏差的情况，这就是所谓的 "实际可靠性"。

因此，由原则（a）中陈述的原则，无法说明在大量重复试验中，每一次计算的 $m/n$ 都会与 $\mathrm{P}(A)$ 非常接近。【不会因为这次抛硬币的结果是正面，下次就更可能是反面；也不会因为这次刮彩票不中，下次就更可能中】

**备注 2**

对于不可能事件（空集），根据公理可得 $\mathrm{P}(\empty) = 0$。但是相反的说法，由 $\mathrm{P}(A) = 0$[^1.5] 无法推出事件 $A$ 不可能发生。事实上，当$P(A) = 0$时，由原则（b）我们可以确定的是，当条件只重复实现一次时，事件 $A$ 几乎是不可能发生的，但并不能说在大量重复试验的情况下 $A$ 不会发生。另一方面，由原则（a），我们只能推导出，当 $\mathrm{P}(A)=0$ 并且 $n$ 很大时，$m/n$ 的值会非常小（例如等于 $1/n$）。

【概率为 0 的含义，只能说明是一个事件几乎不可能发生】。



### 术语说明

我们已经将未来研究的对象——随机事件——定义为集合。然而，在概率论中，许多集合论的概念被用其他术语来表示。下面我们将简要列出这些概念。



| 集合论                                                       | 随机事件                                                     |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 1. 集合 $A$ 与集合 $B$ 没有交集（do not intersect），即 $AB=0$。 | 1. 事件 $A$ 与事件 $B$ 是互斥的（incompatible）。            |
| 2. $AB…N=0$。                                                | 2. 事件 $A, B, ..., N$ 都互斥。                              |
| 3. $AB...N=X$。                                              | 3. 当事件 $A, B, ..., N$ 同时发生时，事件 $X$ 发生。         |
| 4. $A\dot{+}B\dot{+}...\dot{+}N=X$。                         | 4. 当事件 $A, B, ..., N$ 中至少有一个发生时，事件 $X$ 发生。 |
| 5. 集合 $A$ 的补集（complementary）$\bar{A}$。               | 5. 事件 $A$ 的对立事件 $\bar{A}$（opposite event）由所有当事件 $A$ 不发生时（必然）发生的事件组成。 |
| 6. $A=0$。                                                   | 6. 事件 $A$ 不可能发生。                                     |
| 7. $A=E$。                                                   | 7. 事件 $A$ 必然发生。                                       |
| 8. 当 $A_1 + A_2 + ... A_n = E$ 时，称集合 $A_1, A_2, ..., A_n$ 组成的系统 $\mathcal{U}$ 组成集合 $E$ 的一个分解（decomposition）。 | 8. 试验 $\mathcal{U}$ 表示事件 $A_1, A_2, ..., A_n$ 中哪些发生，则称事件 $A_1, A_2, ..., A_n$ 为试验 $\mathcal{U}$ 的可能结果。 |
| 9. $B$ 是 $A$ 的子集：$B \subset A$。                        | 9. 由事件 $B$ 发生，推断出事件 $A$ 必然发生。                |



### 公理的直接推论；条件概率；贝叶斯理论

由 $A + \bar{A} = E$，结合公理 IV 和 V 可得：
$$
\mathrm{P}(A) + \mathrm{P}(\bar{A}) = 1
\tag{1}
$$
$$
\mathrm{P}(\bar{A}) = 1 - \mathrm{P}(A)
\tag{2}
$$



因为 $\bar{E} = 0$，所以有：
$$
\mathrm{P}(0) = 0
\tag{3}
$$
如果事件 $A, B, ..., N$ 互不相容，则由公理 V 可得：
$$
\mathrm{P}(A + B + \dots + N) = \mathrm{P}(A) + \mathrm{P}(B) + \dots + \mathrm{P}(N)
\tag{4}
$$
如果$\mathrm{P}(A) > 0$，则公式
$$
\mathrm{P}_A(B) = \frac{\mathrm{P}(AB)}{\mathrm{P}(A)}
\tag{5}
$$
定义为在事件 $A$ 发生的条件下，事件 $B$ 发生的条件概率（conditional probability）。由上面的条件概率定义公式可直接推出：
$$
\mathrm{P}(AB) = \mathrm{P}(A)\mathrm{P}_A(B)
\tag{6}
$$
采用归纳法，可得上式的更广义的形式（乘法定理）：
$$
\mathrm{P}(A_1 A_2 ... A_n) = \mathrm{P}(A_1)\mathrm{P}_{A_1}(A_2)\mathrm{P}_{A_1 A_2}(A_3)...\mathrm{P}_{A_1 A_2 ... A_{n-1}}(A_n)
\tag{7}
$$

>   条件概率的广义形式。当有 $n$ 个事件 $A_1, A_2, ..., A_n$ 时。它们同时发生的概率等于事件 $A_1$ 发生的概率，乘事件 $A_1$ 发生的条件下，事件 $A_2$ 发生的概率；乘事件 $A_1, A_2$ 同时发生的条件下，事件 $A_3$ 发生的概率；……；乘 事件 $A_1, A_2, ... A_{n-1}$ 同时发生的条件下，事件 $A_n$ 发生的概率。

显然，以下定理可直接推出：
$$
\tag{8}
\mathrm{P}_A(B) \ge 0
$$

$$
\tag{9}
\mathrm{P}_A(E) = 1
$$

$$
\tag{10}
\mathrm{P}_A(B + C) = \mathrm{P}_A(B) + \mathrm{P}_A(C)
$$
将上面公式 $(8-10)$ 与公理 III 到 公理 V 对比，可知当集合 $A$ 是一个固定的集合时，给定函数 $\mathrm{P}_A(B)$后，集合系统 $\mathcal{E}$ 构成一个概率域，因此当事件 $A$ 给定时，所有以上关于事件 $B$ 的概率 $\mathrm{P}(B)$ 的定理/推论同样适用于条件概率 $\mathrm{P}_A(B)$。

由条件概率的定义可得：
$$
\tag{11}
\mathrm{P}_A(A) = 1
$$

>   当已知事件 $A$ 发生时，事件 $A$​ 必然发生。

由公式$(6)$可类比得到：
$$
\mathrm{P}(AB) = \mathrm{P}(B) \mathrm{P}_B(A)
$$
进一步得到以下这个非常重要的公式：
$$
\tag{12}
\mathrm{P}_B(A) = \frac{\mathrm{P}(A)\mathrm{P}_A(B)}{\mathrm{P}(B)}
$$
即，**贝叶斯定理**（the Theorem of Bayes）。

**全概率定理**（The Theorem on Total Probability）

假设 $A_1 + A_2 + ... + A_n = E$，并且事件 $A_1, A_2, ..., A_n$ 相互独立，假设 $X$ 为任意事件，则有：
$$
\tag{13}
\mathrm{P}(X) = \mathrm{P}(A_1)\mathrm{P}_{A_1}(X) + \mathrm{P}(A_2)\mathrm{P}_{A_2}(X) + \dots + \mathrm{P}(A_n)\mathrm{P}_{A_n}(X)
$$
证明：
$$
X = A_1X + A_2X + \dots + A_nX
$$
由公式$(4)$可得
$$
\mathrm{P}(X) = \mathrm{P}(A_1X) + \mathrm{P}(A_2X) + \dots + \mathrm{P}(A_nX)
$$
由公式$(6)$可得
$$
\mathrm{P}(A_iX) = \mathrm{P}(A_i)\mathrm{P}_{A_i}(X)
$$
**贝叶斯定理**

假设 $A_1 + A_2 + ... + A_n = E$，并假设 $X$​ 为任意事件，则有：
$$
\tag{14}
\mathrm{P}_X(A_i) = \frac{\mathrm{P}(A_i)\mathrm{P}_{A_i}(X)}{\mathrm{P}(A_1)\mathrm{P}_{A_1}(X) + \mathrm{P}(A_2)\mathrm{P}_{A_2}(X) + \dots + \mathrm{P}(A_n)\mathrm{P}_{A_n}(X)}
$$
上式中，$A_1, A_2, ..., A_n$ 称为假说（hypotheses），公式$(14)$表示当事件 $X$ 发生时，假说 $A_i$ 成立的概率 $\mathrm{P}_X(A_i)$。$\mathrm{P}(A_i)$ 表示事件 $A_i$ 的先验概率（a priori probability）。

证明：

由公式$(12)$可得：
$$
\mathrm{P}_X(A_i) = \frac{\mathrm{P}(A_i)\mathrm{P}_{A_i}(X)}{\mathrm{P}(X)}
$$
把上式中的$\mathrm{P}(X)$用公式$(13)$替换，即可证明。



### 独立性

在某种意义上，两个或多个试验的相互独立性的概念，占据着概率论的核心地位。事实上，正如我们已经看到的，从数学的角度来看，概率论可以被视为一般的可加集函数理论（general theory of additive set functions）的一种特殊应用。既然如此，概率论是如何发展成为一门拥有特定研究方法的科学的呢？

想要回答这个问题，首先必须指出当可加集函数理论中的一般问题在概率理论领域提出时，它所经历的特殊化过程。

可加集函数 $\mathrm{P}(A)$ 为非负值且满足 $\mathrm{P}(E) = 1$，本身并不会导致新的困难。从数学角度看，随机变量（random variables，见第 III 章）仅仅表示相对于 $\mathrm{P}(A)$ 可测的（measurable）函数，并且它的数学期望是抽象勒贝格积分（Lebesgue integrals）（这个类比在 Fréchet[^1.6] 的工作中第一次得到了详尽的阐述）。仅仅以上这些概念的介绍，根本不足以产生一门新科学发展的基础。

历史上，试验和随机变量的独立性代表着一个给概率论打上了特别的印记概念。LaPlace、Poisson、Tchebychev、Markov、Liapounov、 Mises、以及 Bernstein 的经典工作致力于对独立随机变量序列的基本研究。尽管最新的论文（Markov、 Bernstein 等人）常常不假设完全独立性，但它们仍然揭示了引入类似的、弱条件的独立性的必要性，以获得足够显著的结果（见第 6 节，马尔可夫链）。

由此可见，独立性的概念是概率论中这一类独特问题的起源。然而本书中并不强调这一点，我们主要关注的是概率理论专业研究的逻辑基础。因此，在自然科学的基本原理（philosophy）中，最重要的问题之一是明确那些使得任意给定的真实事件可以被视为独立的前提——除了众所周知的关于概率这一概念的本质的问题之外。然而，这个问题超出了本书的讨论范围。

---

独立性的定义：给定 $n$ 个试验，$\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}$，即基本集合 $E$ 的 $n$ 个分解：
$$
E = A^{(i)}_1 + A^{(i)}_2 + \dots + A^{(i)}_{\tau_i},    \quad i = 1, 2, ..., n
$$
由此定义 $r = r_1 r_2... r_n$ 种概率：
$$
p_{q_1 q_2 ... q_n} = \mathrm{P}(A^{(1)}_{q_1} A^{(2)}_{q_2} ... A^{(n)}_{q_n}) \ge 0
$$
这些概率是任意值，但需要满足下面的条件[^1.7]：
$$
\tag{1}

\sum_{q_1, q_2, ..., q_n} p_{q_1 q_2 ... q_n} = 1
$$
**定义 I**	当对于任意的 $q_1, q_2, ..., q_n$，以下等式总成立时，称 $n$ 个试验 $\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}$ 相互独立：
$$
\tag{2}
\mathrm{P}(A^{(1)}_{q_1} A^{(2)}_{q_2} ... A^{(n)}_{q_n}) = \mathrm{P}(A^{(1)}_{q_1}) \mathrm{P}(A^{(2)}_{q_2}) ... \mathrm{P}(A^{(n)}_{q_n})
$$
在$(2)$中的 $r$ 个方程中，仅有 $r - r_1 - r_2 - ... - r_n + n - 1$​ 个独立方程[^1.8]。

**定理 I**	如果 $n$ 个试验 $\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}$ 相互独立，则其中的任意 $m$ 个（$m < n$）试验 $\mathscr{U}^{(i_1)}, \mathscr{U}^{(i_2)}, ..., \mathscr{U}^{(_m)}$​ 也相互独立[^1.9]。在独立性的情况下，有以下方程：
$$
\tag{3}
\mathrm{P}(A^{(i_1)}_{q_1} A^{(i_2)}_{q_2} ... A^{(i_m)}_{q_m}) = \mathrm{P}(A^{(i_1)}_{q_1}) \mathrm{P}(A^{(i_2)}_{q_2}) ... \mathrm{P}(A^{(i_m)}_{q_m})
$$
（所有的 $i_k$ 都互不相同）。

**定义 II**	对于 $k = 1, 2, ..., n$，如果以下分解/试验成立：
$$
E = A_k + \bar{A_k}
$$
则称 $n$ 个事件 $A_1, A_2, ..., A_n$ 相互独立。

在这种情况下，$r_1 = r_2 = ... = r_n$，$r = 2^n$；因此 公式$(2)$中的 $2^n$ 个方程中，只有 $2^n - n - 1$ 个是独立的。事件 $A_1, A_2, ..., A_n$ 独立的充分必要条件是满足以下的 $2^n - n - 1$[^1.10]：
$$
\tag{4}
\mathrm{P}(A_{i_1} A_{i_2} ... A_{i_m}) = \mathrm{P}(A_{i_1}) \mathrm{P}(A_{i_2}) ... \mathrm{P}(A_{i_m})  \\
m = 1, 2, ..., n, \\
1 \le i_1 \le i_2 < ... < i_m \le n
$$
以上所有方程都相互独立。

当 $n = 2$ 时，由公式 $(4)$ 可得两个事件 $A_1$ 和 $A_2$ 独立的一个条件（2$^2$ - 2 - 1 = 1）：
$$
\tag{5}
\mathrm{P}(A_1 A_2) = \mathrm{P}(A_1)\mathrm{P}(A_2)
$$
在这种情况下（$n = 2$），方程系统 $(2)$ 退化为三个方程（除方程$(5)$外）：
$$
\mathrm{P}(A_1 \bar{A_2}) = \mathrm{P}(A_1)\mathrm{P}(\bar{A_2}) \\
\mathrm{P}(\bar{A_1} A_2) = \mathrm{P}(\bar{A_1})\mathrm{P}(A_2) \\
\mathrm{P}(\bar{A_1} \bar{A_2}) = \mathrm{P}(\bar{A_1})\mathrm{P}(\bar{A_2})
$$
以上公式显然可以由公式 $(5)$​ 导出[^1.11]。

需要强调的是，事件 $A_1, A_2, ..., A_n$ 两两相互独立，例如以下关系
$$
\mathrm{P}(A_i A_j) = \mathrm{P}(A_i)\mathrm{P}(A_j) \qquad (i \ne j)
$$
并不能推导出当 $n > 2$ 时这些事件相互独立[^1.12]。（当公式 $(4)$ 中的方程全部成立时，事件$A_1, A_2, ..., A_n$ 才相互独立）。

在引入独立性的概念时，并没有使用条件概率。我们的目标是用纯数学的方式，尽可能清楚地解释这个概念的含义。然而，它的应用通常依赖于某些条件概率的性质。

如果我们假设所有的概率 $\mathrm{P}(A_q ^{(i)})$ 都为正，则从公式 $(3)$ 可以推导出[^1.13]
$$
\tag{6}
\mathrm{P}_{A^{(i_1)}_{q_1} A^{(i_2)}_{q_2} ... A^{(i_{m - 1})}_{q_{m - 1}}} (A^{(i_m)}_{q_m}) = \mathrm{P}(A^{(i_m)}_{q_m})
$$
由公式 $(6)$ 成立，以及第 1.4 节（公理的直接推论；条件概率；贝叶斯理论）公式 $(7)$  可得公式 $(2)$。因此，我们得到定理 II。

**定理 II**：当概率 $\mathrm{P}(A_q ^{(i)})$ 为正时，试验  $\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}$ 独立的必要和充分条件是，在其他试验 $\mathscr{U}^{(i_1)}, \mathscr{U}^{(i_2)}, ..., \mathscr{U}^{(i_k)}$ 有确定的结果 $A_{q_1} ^{(i_1)}, A_{q_2} ^{(i_2)}, A_{q_3} ^{(i_3)}, ..., A_{q_k} ^{(i_k)}$ 的假设下，试验 $\mathscr{U}^{(i)}$ 的结果 $A_q ^{(i)}$ 的条件概率等于该结果的绝对概率 $\mathrm{P}(A_q ^{(i)})$。

在公式 $(4)$ 的基础上，我们可以以类比的方式证明如下定理：

**定理 III**： 如果所有的概率 $\mathrm{P}(A_K)$ 都为正值，则事件 $A_1, A_2, ..., A_n$ 相互独立的必要和充分条件是，对于任意不同的 $i_1, i_2, ..., i_k, i$，如下等式成立：
$$
\tag{7}
\mathrm{P}_{A_{i_1} A_{i_2} ... A_{i_k}} (A_i) = \mathrm{P}(A_i)
$$
当 $n = 2$ 时，公式 $(7)$ 退化为两个等式：
$$
\tag{8}
\mathrm{P}_{A_1} (A_2) = \mathrm{P}(A_2) \\
\mathrm{P}_{A_2} (A_1) = \mathrm{P}(A_1)
$$
很容易看出，公式 $(8)$ 中的第一个等式是当 $\mathrm{P}(A_i) > 0$ 时，事件 $A_1$ 和 $A_2$ 独立的必要和充分条件。



### 作为随机变量的条件概率；马尔可夫（Markov）链

假设 $\mathscr{U}$ 为基本集合 $E$ 的一个分解：
$$
E = A_1 + A_2 + ... + A_n
$$
并假设 $x$ 为基本事件 $\xi$ 的一个实函数（real function），即对于每一个集合 $A_q$，都有一个常数 $a_q$ 与它对应。$x$ 称为随机变量（random variable），以下等式
$$
\mathrm{E}(x) = \sum_{q} a_q \mathrm{P}(A_q)
$$
称为变量 $x$ 的数学期望（mathematical expectation）。随机变量的理论将在第三章和第四章介绍。我们不应该仅局限于只能取有限个值的随机变量。

对应集合 $A_q$ 的随机变量记为 $\mathrm{P}_{A_{q_i}}(B)$，我们称其为当试验 $\mathscr{U}$ 给定时事件 $B$ 的条件概率，并用 $\mathrm{P}_{\mathscr{U}}(B)$。当且仅当下面公式成立时，两个试验 $\mathscr{U^{(1)}}$ 和  $\mathscr{U^{(2)}}$ 独立。
$$
\mathrm{P}_{\mathscr{U^{(1)}}} (A_q ^{(2)}) = \mathrm{P}(A_q ^{(2)}) \qquad q = 1, 2, ..., r_2
$$
给定任意试验的分解 $\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}$，我们用以下公式
$$
\mathscr{U}^{(1)} \mathscr{U}^{(2)} ... \mathscr{U}^{(n)}
$$
表示基本集合 $E$ 的分解的乘积：
$$
A_{q_1} ^{(1)} A_{q_2} ^{(2)} ... A_{q_n} ^{(n)}
$$
试验 $\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}$ 相互独立，当且仅当以下公式成立：
$$
\mathrm{P}_{\mathscr{U^{(1)}} \mathscr{U^{(2)}} ... \mathscr{U^{(k -1)}}} (A_q ^{(k)}) = \mathrm{P}(A_q ^{(k)})
$$
其中 $k$ 和 $q$ 为任意值[^1.14]。

**定义**：当对于任意的 $n$ 和 $q$，如果以下等式
$$
\mathrm{P}_{\mathscr{U^{(1)}} \mathscr{U^{(2)}} ... \mathscr{U^{(n -1)}}} (A_q ^{(n)}) = \mathrm{P}_{\mathscr{U^{(n -1)}}} (A_q ^{(n)})
$$
成立，则序列 $\mathscr{U}^{(1)}, \mathscr{U}^{(2)}, ..., \mathscr{U}^{(n)}, ...$ 构成一个马尔可夫链（Markov chain）。

因此，马尔可夫链是相互独立的试验的序列的推广。如果假设
$$
p_{q_m, q_n}(m, n) = \mathrm{P}_{A_m^{(m)}}(A_{q_n} ^{(n)}) \qquad m < n
$$
则马尔可夫链理论的基本方程为以下形式：
$$
\tag{1}
p_{q_k, q_n}(k, n) = \sum _{q_m} p_{q_k, q_m}(k, m) \ p_{q_m, q_n}(m, n)  \qquad k < m < n
$$
如果把矩阵 $||p_{q_m, q_n}(m, n)||$ 记为 $p(m, n)$，则公式 $(1)$ 可以记为[^1.15]：
$$
\tag{2}
p(k, n) = p(k, m) \  p(m, n) \qquad k < m < n
$$




[^1.1]: 见参考文献 R. von Mises [1] and [2] and S. Bernstein [1].
[^1.2]: 希望从一开始就为以下公理赋予具体含义的读者，可以参考第二章（与实验数据的关系）。
[^1.3]: 参考 HAUSDORFF, Mengenlehre, 1927, p. 78. 如果一个集合系统中的任意两个集合的和、差、积也属于这个集合系统，则这个集合系统称为一个域。任意非空集合包含空集。使用豪斯多夫（Hausdorff）的标记，把 $A$ 和 $B$ 的乘积记为 $AB$；当 $AB=0$ 时和记为 $A+B$；广义的和记为 $A+B$；$A$ 与 $B$ 的差记为 $A-B$。集合 $E-A$ 表示集合 $A$ 的补集，记为 $\bar{A}$。我们假定读者熟悉集合以及集合的和、差、乘积的基本运算规则。所有 $\mathcal{E}$ 的子集都用大写拉丁字母表示。
[^1.4]: 只对概率论的纯数学发展感兴趣的读者可以略过此节。接下来的内容都只基于第一节的公理，并不利用当前的讨论。第二章，我们仅限于简要解释概率论的公理如何产生，并忽略了关于经验世界中概率概念的深刻哲学论述。在建立概率论应用于现实世界的前提条件时，作者在很大程度上使用了冯·米泽斯的工作，[1] 第21-27页。
[^1.5]: 第四节公式（3）
[^1.6]: 见 Fréchet [1] 和 [2].
[^1.7]: 在满足上面提到的条件下，可以用任意概率值构建一个概率域：$E$ 由 $r$ 个元素组成，$\xi _{q_1 q_2 ... q_n}$。 令每一个元素对应的基本概率为 $p_{q_1 q_2 ... q_n}$，则 $A^{(i)}_{q_n}$ 表示当 $q_i = q$ 时的所有 $\xi _{q_1 q_2... q_n}$ 组成的集合。
[^1.8]: 事实上在独立的情况下，可以只选取 $r_1 + r_2 + ... r_n$ 种概率，$p^{(i)}_q = \mathrm{P}(A^{i}_q)$，以遵循 $n$ 个条件 $\sum_{q} p^{(i)}_q = 1$，因此在一般情形下有 $r-1$ 个自由度，但在独立情形下只有 $r_1 + r_2 ... + r_n - n$ 个自由度。

[^1.9]: 要证明这一点，只需表明，从（概率空间的） $n$ 个分解的相互独立性可以推导出前 $n-1$ 个分解的相互独立性（关于 $n$ 个分解的解释：In the context of probability and statistics, **n decompositions** typically refers to the partitioning or breaking down of a probability space into nnn mutually exclusive and exhaustive events or sets.）假如公式 $(2)$ 正确，则有：

$$
\mathrm{P}(A^{(1)}_{q_1} A^{(2)}_{q_2} ... A^{(n-1)}_{q_{n-1}}) = \sum_{q_n}\mathrm{P}(A^{(1)}_{q_1} A^{(2)}_{q_2} ... A^{(n)}_{q_{n}}) = \mathrm{P}(A^{(1)}_{q_1}) \mathrm{P}(A^{(2)}_{q_2}) ... \mathrm{P}(A^{(n-1)}_{q_{n-1}}) \sum_{q_n} \mathrm{P}(A^{(n)}_{q_{n}}) = \mathrm{P}(A^{(1)}_{q_1}) \mathrm{P}(A^{(2)}_{q_2}) ... \mathrm{P}(A^{(n-1)}_{q_{n-1}})
$$

[^1.10]: 参考 S. N. Bernstein [1] pp. 47-57。不过读者应该可以很容易采用数学归纳法自己证明这一推论。
[^1.11]: $\mathrm{P}(A_1 \bar{A_2}) = \mathrm{P}(A_1) - \mathrm{P}(A_1 A_2) = \mathrm{P}(A_1) - \mathrm{P}(A_1)\mathrm{P}(A_2) = \mathrm{P}(A_1)\{1 - \mathrm{P}(A_2)\} = \mathrm{P}(A_1)\mathrm{P}(\bar{A_2})$，其他公式也是类似的推导方法。
[^1.12]: 以下为一个简单的证明（来自 S. N. Bernstein）：假设集合 $E$ 包含 4 个元素 $\xi_1, \xi_2, \xi_3, \xi_4$；并假设对应的基本概率 $\mathrm{P}_1, \mathrm{P}_2, \mathrm{P}_3, \mathrm{P}_4$ 均为 $1/4$，并且有

$$
A = \{\xi_1, \xi_2\}, \quad B = \{\xi_1, \xi_3\}, \quad C = \{\xi_1, \xi_4\}
$$

由此很容易可以算出（注意 $\mathrm{P}(AB)$ 表示事件 $A$ 和 $B$ 同时发生，即基本事件 $\xi_1$ 发生，所以为 $1/4$）。
$$
\mathrm{P}(A) = \mathrm{P}(B) = \mathrm{P}(C) = 1/2 \\
\mathrm{P}(AB) = \mathrm{P}(BC) = \mathrm{P}(AC) = 1/4 = (1/2)^2 \\
\mathrm{P}(ABC) = 1/4 \ne (1/2)^3
$$

[^1.13]: 想要证明该公式，读者需要记住条件概率的定义（1.4节公式 $(5)$），并将乘积的概率 $\mathrm{P}(AB)$ 替换为公式 $(3)$。
[^1.14]: 这些条件的必要性可由第 5 节的定理 II 导出；其充分性可以从乘法定理（第 4 节公式 $(7)$）得出。
[^1.15]: 关于马尔可夫链理论更深入的阐述，参考 v. Mises [1], § 16，以及 B. HOSTINSKY, Méthodes générales du calcul des probabilités, “Mém. Sci. Math.” V. 52, Paris 1931。



## 无限概率域

### 连续性公理

按照惯例，我们用符号 $\underset{m}{\mathscr{D}} A_m$ 表示集合 $A_m$ 的乘积（无论是有限个还是无限个），用符号 $\underset{m}{\mathscr{G}} A_m$ 表示集合 $A_m$ 的和。仅当集合 $A_m$ 为不相交集（disjont sets，集互斥集合），集合的和的形式记为 $\sum\limits _m A_m$。由此可得：
$$
\underset{m}{\mathscr{G}} A_m = A_1 \dot{+} + A_2 \dot{+} \dots \\
\sum\limits _m A_m = A_1 + A_2 + \dots \\
\underset{m}{\mathscr{D}} A_m = A_1 A_2 \dots
$$
在后续的研究中，除了公理 I-V 外，我们认为以下公理也成立：

**公理 VI**：对于集合  $\mathcal{E}$ 中的一个递减的事件序列
$$
\tag{1}
A_1 \supset A_2 \supset \dots \supset A_n \supset \dots
$$

$$
\tag{2}
\underset{n}{\mathscr{D}} A_n = 0
$$



当公式 $(2)$ 成立时，以下等式（公式 $(3)$）成立。
$$
\tag{3}
\lim \limits_{n \rightarrow \infin} \mathrm{P}(A_n) = 0
$$
在后续的研究中，我们将满足第一章第1节中五个公理，以及本章开头提到的公理 VI 的**集合系统**及其对应的$\mathrm{P}(A)$，称为概率域。而第一章中原本定义的概率域称为广义概率域（generalized fields of probability）。

如果集合系统 $\mathcal{E}$ 是有限的，公理 VI 可以由公理 I-V 推导得出。事实上，这种情况下在序列 ($1$) 中仅存在有限个不同的集合。假设 $A_k$ 是该序列中最小的（集合的大小指集合的**基数**），则所有与 $A_k$ 恰巧（基数）相等的集合 $A_{p + k}$，有以下关系：
$$
A_k = A_{k + p} = \underset{n}{\mathscr{D}} A_n = 0 \\
\lim \mathrm{P}(A_n) = \mathrm{P}(0) = 0
$$
因此，第一章中提到的所有有限概率域的例子都满足公理 VI。已证明，公理系统 I-VI 是一致且不完备的（consitent and incomplete）。

然而，对于无限概率域，连续性公理（公理VI）已被证明是独立于公理 I-V 的。因为这一新的公理仅对于无限概率域是必要的，所以几乎无法像第一章第 2 节那样中，对公理 I-V 那样，解释清楚公理 VI 的经验意义（empirical meaning）。因为在描述任何可观测的随机过程时，我们只能得到有限概率域。无限概率域只出现在现实中的随机过程的理想化模型中。后续的讨论，我们仅限于满足公理 VI 的模型。这一限制（尽管有些随意）在研究绝大多数的模型时有效且可行的。

**广义加法定理**：如果 $A_1, A_2, ..., A_n, ...$ 以及 $A$ 属于 $\mathcal{E}$，则由公式$(4)$
$$
\tag{4}
A = \sum_n A_n
$$
可得公式$(5)$
$$
\tag{5}
\mathrm{P}(A) = \sum_n \mathrm{P}(A_n)
$$
证明：令
$$
R_n = \sum _{m > n} A_m
$$
则显然
$$
\underset{n}{\mathscr{D}} (R_n) = 0
$$
因此根据公理 VI 得
$$
\tag{6}
\lim \mathrm{P}(R_n) = 0 \qquad n \rightarrow \infin
$$
另一方面，根据加法定理得
$$
\tag{7}
\mathrm{P}(A) = \mathrm{P}(A_1) + \mathrm{P}(A_2) + \dots + \mathrm{P}(A_n) + \mathrm{P}(R_n)
$$
由公式 (6) 和公式 (7) 可得公式 (5)。

由此已表明，概率 $\mathrm{P}(A)$ 是一个集合系统 $\mathcal{E}$ 上的完全可加集函数。公理 V 和 VI 对于定义在任意 $\mathcal{E}$ 上的完全可加集函数都成立[^*]。因此可以通过以下方式定义概率域的概念：

>   令 $E$ 为任意集合，$\mathcal{E}$ 为 $E$ 的包括其本身在内的子集组成的域，以及 $\mathrm{P}(A)$ 为一个定义在 $\mathcal{E}$ 上的非负的完全可加集函数。则 $\mathcal{E}$ 与集合函数 $\mathrm{P}(A)$ 共同构成一个概率域。

**收敛性定理**：如果 $A_1, A_2, ..., A_n, ...$ 以及 $A$ 属于 $\mathcal{E}$，并且
$$
\tag{8}
A \subset \underset{n}{\mathscr{G}} A_n
$$
则
$$
\tag{9}
\mathrm{P}(A) \leq \sum_n \mathrm{P}(A_n)
$$
证明：
$$
A = A \underset{n}{\mathscr{G}} A_n = A (A_1 + A_2(1 - A_1) + A_3(1 - A_2 - A_1)) + \dots = A A_1 + A (A_2 - A_2 A_1) + A (A_3 - A_3 A_2 - A_3 A_1) + \dots \\
\mathrm{P}(A) = \mathrm{P}(A A_1) + \mathrm{P}(A (A_2 - A_2 A_1)) + \dots \leq \mathrm{P}(A_1) + \mathrm{P}(A_2) + \dots
$$




### 概率的波莱尔（Borel）域

对于 $\mathcal{E}$ 中的集合 $A_n$，如果集合 $A_n$ 的可数和 $\sum _n A_n$ 也属于 $\mathcal{E}$，则称 $\mathcal{E}$ 为一个波莱尔域。波莱尔域也称为**完全可加的集合系统**（completely additive systems of sets）。由以下等式
$$
\tag{1}
\underset{n}{\mathscr{G}} A_n = A_1 + (A_2 - A_2 A_1) + (A_3 - A_3 A_2 - A_3 A_1) + \dots
$$
可推断出，波莱尔域包含了所有的 $\underset{n}{\mathscr{G}} A_n$，后者由波莱尔域中的可数个集合 $A_n$ 组成。

由以下等式
$$
\tag{2}
\underset{n}{\mathscr{D}} A_n = E - \underset{n}{\mathscr{G}} A_n
$$
可知，对于集合 $A_n$ 的乘积 $\underset{n}{\mathscr{D}} A_n$ 也有相同的结论。

当域 $\mathcal{E}$ 为波莱尔域时，对应的概率域称为波莱尔概率域。只有在波莱尔概率域下，我们才可以自由地研究概率，而不用担心**没有概率的事件**。接下来我们将从**扩展定理**开始证明，后续讨论都将在波莱尔概率域中进行。

给定一个概率域 $(\mathcal{E}, \mathrm{P})$，可知[^2.1] 存在一个包含域 $\mathcal{E}$ 的最小波莱尔域 $B\mathcal{E}$，我们有以下扩展定理：

**扩展定理**：总是可以将定义在域 $\mathcal{E}$ 上的非负完全可加集函数 $\mathrm{P}(A)$ 扩展到所有的最小波莱尔域 $B\mathcal{E}$，且不丢失它的任何性质（非负性、完全可加性）。且只有一种方式可以做到。

扩展域 $B\mathcal{E}$ 与扩展后的集合函数 $\mathrm{P}(A)$ 一起构成了概率域 $(B\mathcal{E}, \mathrm{P})$。这个概率域称为域 $(\mathcal{E}, \mathrm{P})$ 的波莱尔扩展。

这个定理的证明属于可加集函数理论的范畴，并且有时以其他的形式出现。此处给出一种形式的证明：

令 $A$ 为集合 $E$ 的任一子集，用 $\mathrm{P}^*(A)$表示以下和式的下限
$$
\sum_n \mathrm{P}(A_n)
$$
对于集合 $A$ 的所有由有限个或可数多个 $\mathcal{E}$ 中的集合 $A_n$ 构成的覆盖的覆盖（coverings）：
$$
A \subset \underset{n}{\mathscr{G}} A_n
$$
很容易证明，$\mathrm{P}^*(A)$ 是 Carathéodory 外测度[^2.2]。根据收敛定理（本章第一节），对于所有 $\mathcal{E}$ 中的集合，$\mathrm{P}^*(A)$ 恰巧等于 $\mathrm{P}(A)$。进一步可以证明，所有 $\mathcal{E}$ 中的集合在 Carathéodory 测度下都是可测的。因为所有的可测集构成了波莱尔域，所以域 $B\mathcal{E}$ 中的集合也是可测的。因此集合函数 $\mathrm{P}^*(A)$ 在 $B\mathcal{E}$ 上是完全可加的。在域 $B\mathcal{E}$ 上，我们令
$$
\mathrm{P}(A) = \mathrm{P}^*(A)
$$
由此我们证明了扩展域的存在。扩展域的唯一性来源于域 $B\mathcal{E}$ 是最小波莱尔域。

**备注**：即使域 $\mathcal{E}$ 中的集合（事件）$A$ 是真是存在的、可观测的事件，但这并不意味着扩展域中的集合也是真实存在的、可观测的。

因此存在这样的可能性：当概率域 $(\mathcal{E}, \mathrm{P})$ 可以视为现实中的随机事件的镜像时，扩展概率域 $(B\mathcal{E}, \mathrm{P})$ 仍然只具有数学上的结构，而没有现实的概率意义。

因此域 $(B\mathcal{E}, \mathrm{P})$ 中的集合通常只是理想事件，它们并没有在现实世界中对应的对象。然而，如果利用这些理想事件的概率进行的推演，能够让我们得出现实世界中的随机事件的概率，那么从经验的角度来看，这并不矛盾【利用只在数学概念中存在的概率，计算出现实世界中事件的概率】。

### 无限概率域举例

I. 在本章第 1 节中，我们构造了很多有限概率域。现在假定 $E = {\xi_1, \xi_2, \dots, \xi_n, \dots }$ 为一个可数集合，并令 $\mathcal{E}$ 为 $E$ 的所有子集的集合。基于 $\mathcal{E}$，所有可能的概率域可以按如下方式得出：

给定一个非负实数序列 $p_n$：
$$
p_1 + p_2 + \dots + p_n + \dots = 1
$$
且对于每个集合 $A$，定义
$$
\mathrm{P}(A) = \sum_n ' p_n
$$
其中求和符号 $\sum '$ 作用于所有的属于 $A$ 的 $\xi_n$ 的下标。这些概率域显然是波莱尔域。

II. 在这个例子中，我们应当假设 $E$ 代表实数轴。首先假设 $\mathcal{E}$ 由所有可能的半开区间 $[a_i; b) = \{a \le \xi < b\}$ [^#] 的有限和组成（不仅仅只考虑由有限实数 $a$ 和 $b$ 构成的常规区间，也考虑如 $[-\infin; a), [a; +\infin), [-\infin; +\infin)$ 这类的非常规区间）。这样，$\mathcal{E}$ 即为一个域。然而，根据扩展定理，任何定义在 $\mathcal{E}$ 上的概率域都可扩展为一个定义在 $B\mathcal{E}$ 上的类似的域。因此在这种情况下，集合系统 $B\mathcal{E}$ 实际上无非是实数轴上所有波莱尔点集的系统。接下来考虑下面的情况。

III. 同样假设 $E$ 为实数轴，$\mathcal{E}$ 由这条轴上的所有波莱尔点集构成。想要构造给定域 $\mathcal{E}$ 上的概率域，只需在 $\mathcal{E}$ 上定义一个任意的非负的完全可加集函数 $\mathrm{P}(A)$，且满足 $\mathrm{P}(E) = 1$。已知这样的函数由其在区间 $[-\infin; x)$ 上的取值唯一确定[^2.3]：
$$
\tag{1}
\mathrm{P}[-\infin;x) = F(x)
$$
函数 $F(x)$ 称为 $\xi$ 的分布函数。之后在第三章第 2 节我们可以证明 $F(x)$ 是左连续的非减函数，且具有以下的极限值：
$$
\tag{2}
\lim_{x \rightarrow -\infin} F(x) = F(-\infin) = 0, \\
\lim_{x \rightarrow +\infin} F(x) = F(+\infin) = 1
$$
反过来，如果一个函数 $F(x)$ 满足这些条件，则它总是可以确定一个非负的完全可加集函数 $\mathrm{P}(A)$，使得 $\mathrm{P}(E) = 1$ [^2.4]。

IV. 现在假设基本集合 $E$ 为一个 $n$ 维欧几里得空间 $R^n$，即集合中的每个元素 $\xi$ 是一个由实数构成的含有 $n$ 个元素的有序元组（tuples）：$\xi = \{ x_1, x_2, \dots, x_n \}$。假设 $\mathcal{E}$  由欧几里得空间 $R^n$ 中所有的波莱尔点集[^2.5]组成。根据与示例 II 中使用的类似推理，我们不需要研究更狭义的集合系统，例如 n 维区间的系统。概率函数 $\mathrm{P}(A)$ 同样应该是一个定义在 $\mathcal{E}$ 上的非负的完全可加集函数，且满足 $\mathrm{P}(E) = 1$。这样的集合函数的取值由特定集合 $L_{a_1, a_2, \dots, a_n}$ 确定：
$$
\tag{3}
\mathrm{P}(L_{a_1, a_2, \dots, a_n}) = F(a_1, a_2, \dots, a_n)
$$
其中 $L_{a_1, a_2, \dots, a_n}$ 表示所有满足 $x_i < a_i (i = 1, 2, \dots, n)$ 的 $\xi$ 的集合。

对于 $F(a_1, a_2, \dots, a_n)$ ，我们选择的函数需要满足对于每一个变量都是左连续的非减函数，且满足以下条件：
$$
\tag{4}
\lim_{a_i \rightarrow -\infin} F(a_1, a_2, \dots, a_n) = F(a_1, \dots, a_{i - 1}, -\infin, a_{i + 1}, \dots, a_n) = 0, \\
(i = 1, 2, ..., n) \\
\lim_{a_1 \rightarrow +\infin, a_2 \rightarrow +\infin, \dots, a_n \rightarrow +\infin} F(x) = F(+\infin, +\infin, \dots, +\infin) = 1
$$

>注意以上公式中。$F(x) = 0$ 的极限，只需有其中一个 $a_i$ 趋近于负无穷即可；$F(x) = 1$ 的极限,需要所有的 $a_i$ 都趋近于正无穷。

【注】参考的两个版本在公式 $(4)$ 上有出入，这里贴了一个我能看得懂的，下面图中是另一个版本的我看不懂的公式：

![f0019-04](/assets/images/2025-02-22-probability/f0019-04.jpg)

$F(a_1, a_2, \dots, a_n)$ 称为变量 $x_1, x_2, \dots, x_n$ 的分布函数。

对上述类型的概率域的研究足以解决概率理论中的所有经典问题[^2.6]。特别地，$R^n$ 上的概率函数可以定义为：

定义在 $R^n$ 上的任意非负点函数：
$$
\int^{+\infin}_{-\infin} \int^{+\infin}_{-\infin} \dots \int^{+\infin}_{-\infin} f(x_1, x_2, \dots, x_n) dx_1 dx_2 \dots dx_n = 1
$$
并且令
$$
\tag{5}
\mathrm{P}(A) = \int \int \dots \int_{A} f(x_1, x_2, \dots, x_n) dx_1 dx_2 \dots dx_n
$$
$f(x_1, x_2, \dots, x_n)$ 此时称为在点 $(x_1, x_2, \dots, x_n)$ 处的概率密度（参考第三章第 2 节）。

另一类 $R^n$ 上的概率函数可以由以下方式得出：假设 ${\xi_i}$ 为 $R^n$ 上的一个点的序列，令 $p_i$ 为一个非负实数的序列，使得 $\sum \mathrm{P}_i = 1$。此时如例子 I 中那样，令
$$
\mathrm{P}(A) = \sum' p_i
$$
其中求和符号 $\sum'$ 作用于所有属于 $A$ 的 $\xi$ 的下标。

以上提到的两种定义在 $R^n$ 上的概率函数并没有涵盖所有可能的情况，但是通常认为这两种定义对于概率理论的应用已经足够。然而，我们可以设想一些在经典范围之外的应用问题，其中基本事件通过无限多个坐标来定义。我们将在引入为此目的所需的若干概念后，进一步详细研究相应的概率场（参考第三章第 3 节）。



[^*]: See, for example, O. NIKODYM, Sur une généralisation des intégrales de M. J. Radon, Fund. Math. v. 15, 1930, p. 136.
[^2.1]: 参考 HAUSDORFF, Mengenlehre, 1927, p. 85.
[^2.2]: CARATHÉODORY, Vorlesungen über reelle Funktionen, pp.237-258. (New York, Chelsea Publishing Company).
[^#]: 此处原文，区间表示方式，中间为`;`。
[^2.3]: Cf., for example, LEBESGUE, Leçons sur l’intégration, 1928, p. 152-156.
[^2.4]: 参考前一个注释中的内容。
[^2.5]: For a definition of Borel sets in R see HAUSDORFF, Mengenlehre, 1927, pp. 177-181.
[^2.6]: Cf., for example, R. v. MISES [1], pp. 13-19. Here the existence of probabilities for “all practically possible:” sets of an n-dimensional space is required.



## 随机变量

### 概率函数

给定一个由任意类型元素构成的集合 $E$ 到 $E'$ 的映射，例如定义在 $E$ 上的单值函数 $u(\xi)$，其值属于 $E'$。 对于 $E'$ 的每一个子集 $A'$，我们将与之对应的 $E$ 中的原像（pre-image）记为 $u^{-1}(A')$，它包含所有映射到 $A'$ 中元素的 $E$ 中的元素。令 $\mathcal{E}^{(u)}$ 为$E'$ 的所有子集 $A'$ 组成的系统，其中 $A'$ 的原像属于域 $\mathcal{E}$，则 $\mathcal{E}^{(u)}$ 也是一个域。如果 $\mathcal{E}$ 恰巧是一个波莱尔域，则 $\mathcal{E}^{(u)}$ 也是一个波莱尔域。由此可令：
$$
\tag{1}
\mathrm{P}^{(u)} (A') = \mathrm{P} \{u^{-1} (A')\}
$$
因为定义在 $\mathcal{E}^{(u)}$ 上的集合函数 $\mathrm{P}^{(u)}$ 满足公理 I-VI，所以它可以作为 $\mathcal{E}^{(u)}$  上的概率函数。在证明以上内容之前，我们需要先给出下面的定义：

**定义**：给定一个随机事件 $\xi$ 的单值函数 $u(\xi)$，公式 $(1)$ 中定义的函数 $\mathrm{P}^{(u)} (A')$ 称为 $u$ 的概率函数。

**备注 1**：在研究概率域 $(\mathcal{E}, \mathrm{P})$ 时，我们只简单地把 $\mathrm{P}(A)$ 称为概率函数。但是我们称 $\mathrm{P}^{(u)} (A')$ 为$u$ 的概率函数。当 $u(\xi) = \xi$ 时，$\mathrm{P}^{(u)} (A') $ 恰巧与 $\mathrm{P}(A)$ 相等。

**备注 2**：事件 $u^{-1} (A')$ 表明 $u(\xi)$ 属于 $A'$，因此 $\mathrm{P}^{(u)} (A') $ 表示 $u(\xi)$ 属于 $A'$ 的概率。

我们仍需证明以上提到的 $\mathcal{E}^{(u)}$ 和 $\mathrm{P}^{(u)}$ 的性质，不过它们实际上来源于一个简单的事实：

**引理**：原像集合 $u^{-1} (A')$ 的和、乘积、差，等于对应原始集合 $A'$ 的和、乘积和差的原像。

以上引理的证明过程留给读者。

【补充证明过程】

令 $A'$ 和 $B'$ 为域 $\mathcal{E}^{(u)}$中 的两个集合，它们的原像 $A$ 和 $B$ 则属于 $\mathcal{E}$。由于 $\mathcal{E}$ 构成一个域，则集合 $AB$、$A+B$、$A-B$ 也属于 $\mathcal{E}$；同时这三个集合也分别是集合 $A'B'$、$A' + B'$、$A' - B'$ 的原像（后三者属于域 $\mathcal{E}^{(u)}$）。由此证明 $\mathcal{E}^{(u)}$ 是一个域。同样地，可以证明如果 $\mathcal{E}$ 是一个波莱尔域，则 $\mathcal{E}^{(u)}$ 也是。进一步可得
$$
\mathrm{P}^{(u)}(E') = \mathrm{P} \{u^{-1} (E')\} = \mathrm{P}(E) = 1
$$
显然 $\mathrm{P}^{(u)}$ 总是非负的。因此接下来只需证明，$\mathrm{P}^{(u)}$ 是完全可加的（参考第二章第 1 节结尾）。

假设所有的集合 $A'_n$ ，以及它们各自的原像 $u^{-1} (A'_n)$ 是互斥的（对于所有的 $n$， $A'_n$ 两两互斥，原像同理），则有
$$
\mathrm{P}^{(u)} (\sum_n A'_n) = \mathrm{P} \{u^{-1}(\sum_n A'_n)\} = \mathrm{P} \{\sum_n u^{-1}(A'_n)\} = \sum_n \mathrm{P} \{u^{-1}(A'_n)\} = \sum_n \mathrm{P}^{(u)} (A'_n)
$$
由此证明了$\mathrm{P}^{(u)}$ 的完全可加性。

最后，还有一点需要说明。令 $u_1(\xi)$ 是由 $E$ 映射到 $E'$ 的函数，$u_2(\xi')$ 是由 $E'$ 映射到 $E''$ 的函数。则乘积函数 $u_2 u_1 (\xi)$ 从 $E$ 映射到 $E''$。接下来研究概率函数 $\mathrm{P}^{(u_1)} (A')$ 和 $\mathrm{P}^{(u)} (A'')$，其中 $u(\xi) = u_2 u_1 (\xi)$。很容易得出，这两个概率函数有以下关联：
$$
\tag{2}
\mathrm{P}^{(u)}(A'') = \mathrm{P}^{(u_1)} \{ u_2 ^{-1}(A'') \}
$$



### 随机变量和分布函数的定义

**定义**：定义在基本集合 $E$ 上的实单值函数 $x(\xi)$，当对于任意实数 $a$，满足 $x < a$ 的所有 $\xi$ 组成的集合属于集合系统 $\mathcal{E}$，则这个实单值函数 $x(\xi)$ 称为随机变量（random variable）。

函数 $x(\xi)$ 将基本集合 $E$ 映射到集合 $R^1$，即全体实数。如本章第 1 节所言，这个函数确定了一个实数集 $R^1$ 的子集组成的域 $\mathcal{E}^{(x)}$。我们可以用以下方式重新表述随机变量的定义：

> 对于实函数 $x(\xi)$， 当且仅当域 $\mathcal{E}^{(x)}$ 包含形如 $(- \infin; a)$ 的所有区间时，$x(\xi)$ 为一个随机变量。

因为  $\mathcal{E}^{(x)}$ 是一个域，所以连同区间 $(-\infin, a)$ 一起，这个域包含了半开区间 $[a; b)$ 的所有可能的有限和。如果我们的概率域是波莱尔域，则域  $\mathcal{E}$ 和  $\mathcal{E}^{(x)}$ 也是波莱尔域。因此域 $\mathcal{E}^{(x)}$ 包含集合 $R^1$ 的所有波莱尔集。

后面我们会用 $\mathrm{P}^{(x)}(A')$ 来表示随机变量的概率函数。它是定义在域 $\mathcal{E}^{(x)}$ 的所有集合上的。特别地，对于最重要的情形——概率的波莱尔域，$\mathrm{P}^{(x)}$ 定义在实数集 $R^1$ 的所有波莱尔集上。

**定义**：函数
$$
F^{(x)}(a) = \mathrm{P}^{(x)} (-\infin,a) = \mathrm{P} \{x < a\}
$$
其中 $-\infin$ 和 $+\infin$ 是 $a$ 的可取值。此时该函数称为随机变量 $x$ 的分布函数（distribution function of the random variable $x$）。

由定义马上可知
$$
\tag{1}
F^{(x)}(-\infin) = 0 \\
F^{(x)}(+\infin) = 1
$$
满足不等式 $a \le x < b$ 的 $x$ 概率由下式给出
$$
\tag{2}
\mathrm{P} \{x \subset [a; b)\} = F^{(x)}(b) - F^{(x)}(a)
$$
由此可得，对于 $a < b$，有
$$
F^{(x)}(a) \le F^{(x)}(b)
$$
上式表明，函数 $F^{(x)}(a) $ 是个非递减函数。现在假设 $a_1 < a_2 < \dots < a_n < \dots < b$，则有
$$
\underset{n}{\mathscr{D}} \{x \subset [a_n; b)\} = 0
$$
因此，根据连续性公理可得，当 $n \rightarrow + \infin$ 时，
$$
F^{(x)}(b) - F^{(x)}(a_n) = \mathrm{P} \{x \subset [a_n; b) \}
$$
上式趋近于 $0$。由此可得 函数$F^{(x)}(b)$为左连续函数。

采用类比的方法可以证明：
$$
\tag{3}
\lim F^{(a)} = F^{(x)}(- \infin) = 0, \quad a \rightarrow - \infin
$$

$$
\tag{4}
\lim F^{(a)} = F^{(x)}(+ \infin) = 0, \quad a \rightarrow + \infin
$$

如果概率域 $(\mathcal{E}, \mathrm{P})$ 是波莱尔域，则对于所有实数集 $R^1$ 上的波莱尔集合 $A$，概率函数 $\mathrm{P}^{(x)} (A)$ 的值由分布函数 $F^{(x)} (a)$ 唯一确定（参考第二章第 3 节中的第 III 个例子）。本文重点关注 $\mathrm{P}^{(x)} (A)$ 的值，所以分布函数在后续研究中将发挥极其重要的作用。

如果分布函数 $F^{(x)} (a)$ 可微，则其相对于参数 $a$ 的导数（如下）称为 $x$ 在点 $a$ 处的概率密度（probability density）。
$$
f^{(x)}(a) = \frac{d}{da} F^{(x)} (a)
$$
如果对于每一个 $a$，都有
$$
\frac{d}{da} F^{(x)} (a) = \int^a _{-\infin} f^{(x)}(a) da
$$
则任意波莱尔集 $A$ 的概率函数 $\mathrm{P}^{(x)} (a)$ 可以用以下形式表示
$$
\tag{5}
\mathrm{P}^{(x)} (A) = \int _{A} f^{(x)}(a) da
$$
这种情况下我们称 $x$ 的分布是连续的。上式可以写为另一种更一般的形式
$$
\tag{6}
\mathrm{P}^{(x)} (A) = \int _{A} dF^{(x)}(a)
$$
所有刚刚介绍到的概念都可以推广到条件概率的情形。

集合函数
$$
\mathrm{P}^{(x)} _B (A) = \mathrm{P}_B \{ x \subset A \}
$$
表示在假设 $B$ 下 $x$ 的条件概率。非递减函数
$$
F^{(x)} _B (a) = \mathrm{P}_B \{x < a\}
$$
为对应的分布函数。并且，当 $F^{(x)} _B (a)$ 可微时，
$$
f^{(x)} _B (a) = \frac{d}{da} F^{(x)} _B (a)
$$
是在假设 $B$ 的条件下， $x$ 在 $a$ 点的条件概率密度。



###  多维分布函数

现在给定 $n$ 个随机变量 $X_1, X_2, \dots, X_n$。$n$ 维空间 $R^n$ 中的点 $x = (X_1, X_2, \dots, X_n)$ 是基本事件 $\xi$ 的函数。则根据本章第一节的内容，可得到定义在空间 $R^n$ 上的域 $\mathcal{E}^{(x_1, x_2, \dots, x_n)}$，这个域包括了空间 $R^n$ 的子集；也可以得到定义在 $\mathcal{E}'$ 上的概率函数 $\mathrm{P}^{(x_1, x_2, \dots, x_n)}(A')$。这个概率函数称为随机变量 $x_1, x_2, \dots, x_n$ 的 $n$ 维概率函数。

对于任意选择的 $i$ 和 $a_i$（$i = 1, 2, ..., n$），由随机变量的定义可以直接得出 $R^n$ 中满足 $x_i < a_i$ 的所有的点组成的集合。因此 $\mathcal{E}'$ 也包括了以上集合的交集，例如集合 $L_{a_1 a_2 \dots a_n}$ 表示 $R^n$ 中满足所有不等式 $x_i < a_i$（$i = 1, 2, \dots, n$）的点的集合[^3.1]。

如果把 $R^n$ 空间中满足不等式 $a_i \le x_i < b_i$ 的所有点组成的集合记为 $n$ 维半开区间 $[a_1, a_2, \dots, a_n; b_1, b_2, \dots, b_n)$，则可以发现每一个这样的区间都属于域 $\mathcal{E}'$，因为
$$
[a_1, a_2, \dots, a_n; b_1, b_2, \dots, b_n) = L_{b_1 b_2 \dots b_n} - L_{a_1 b_2 \dots b_n} - L_{b_1 a_2 \dots b_n} - \dots - L_{b_1 b_2 \dots b_{n - 1} a_n}
$$
所有的 $n$ 维半开区间系统的波莱尔扩展，包含 $R^n$ 中的所有波莱尔集合。由此可得出，在波莱尔概率域下，域 $\mathcal{E}$ 包含了 $R^n$ 空间的所有波莱尔集合。

【关于波莱尔扩展（Borel extension）和波莱尔集合（Borel sets）的区别】

**定理**：在波莱尔概率域下，每一个定义在有限个随机变量 $x_1, x_2, \dots, x_n$ 上的波莱尔函数 $x = f(x_1, x_2, \dots, x_3)$ 也是个随机变量。

要证明以上定理，只需证明 $R^n$ 上满足 $x = f(x_1, x_2, \dots, x_n) < a$ 的所有的点 $(x_1, x_2, \dots, x_n)$ 组成的集合是波莱尔集。特别地，所有的对随机变量进行有限的求和和求乘积的操作得到的变量，也是随机变量。

**定义**：函数
$$
F^{(x_1, x_2, \dots, x_n)} (a_1, a_2, \dots, a_n) = \mathrm{P}^{(x_1, x_2, \dots, x_n)} (L_{a_1 a_2 \dots a_n})
$$
称为随机变量 $x_1, x_2, \dots, x_n$ 的 $n$ 维分布函数。

与在一维下的情形类似，我们证明证明 $n$ 维分布函数 $F^{(x_1, x_2, \dots, x_n)} (a_1, a_2, \dots, a_n)$ 是一个非减函数，并且对于任意一个变量都是左连续的。类比第二节中的公式 $(3)$ 和 $(4)$，可得：
$$
\tag{7}
\lim_{a_i \rightarrow - \infin} F (a_1, a_2, \dots, a_n) = F (a_1, a_2, \dots, a_{i - 1}, - \infin, a_{i + 1}, \dots, a_n) = 0
$$

$$
\tag{8}
\lim_{a_1 \rightarrow + \infin, a_2 \rightarrow + \infin, \dots, a_n \rightarrow + \infin} F (a_1, a_2, \dots, a_n) = F (+ \infin, + \infin, \dots, + \infin) = 1
$$

分布函数 $F^{(x_1, x_2, \dots, x_n)}$ 只针对于特定集合 $L_{a_1 a_2 \dots a_n}$ 给出了概率 $\mathrm{P}^{(x_1, x_2, \dots, x_n)}$ 的值。但如果概率域是波莱尔域，则[^3.2] 对于所有 $R^n$ 上的波莱尔集， $\mathrm{P}^{(x_1, x_2, \dots, x_n)}$ 都可以由分布函数 $F^{(x_1, x_2, \dots, x_n)}$ 唯一确定（uniquely determined）。

如果存在导数
$$
f (a_1, a_2, \dots, a_n) = \frac{\part}{\part a_1 \part a_2 \dots \part a_n} F^{(x_1, x_2, \dots, x_n)} (a_1, a_2, \dots, a_n)
$$
则我们称这个导数为随机变量 $x_1, x_2, \dots, x_n$ 在点 $a_1, a_2, \dots, a_n$ 处的 $n$ 维概率密度。并且如果对于每个点 $(a_1, a_2, \dots, a_n)$，都有
$$
F^{(x_1, x_2, \dots, x_n)} (a_1, a_2, \dots, a_n) = \int_{-\infin}^{a_1} \int_{-\infin}^{a_2} \dots \int_{-\infin}^{a_n} f (a_1, a_2, \dots, a_n) d a_1 d a_2 \dots d a_n
$$
则 $x_1, x_2, \dots, x_n$ 的分布称为连续的。对于每一个波莱尔集合 $A \sub R^n$，有以下等式
$$
\tag{9}
\mathrm{P}^{(x_1, x_2, \dots, x_n)} (A) = \int \int \dots \int f (a_1, a_2, \dots, a_n) d a_1 d a_2 \dots d a_n
$$
在本章最后我们对各种概率函数和分布函数的关系再做一个说明。

给定如下代换
$$
S = \begin{pmatrix}
1, 2, \dots, n \\
i_1, i_2, \dots, i_n \\
\end{pmatrix}
$$
令 $r_s$ 表示如下的空间 $R^n$ 到其自身的一个变换：
$$
x'_k = x_{ik} \quad (k = 1, 2, \dots, n)
$$
显然可得
$$
\tag{10}
\mathrm{P}^{(x_{i_1}, x_{i_2}, \dots, x_{i_n})} (A) = \mathrm{P}^{(x_1, x_2, \dots, x_n)} \{r_s ^{-1} (A)\}
$$


现在令 $x' = p_k (x)$ 为空间 $R^n$ 在空间 $R^k$ ($k < n$) 的投影，所以空间 $R^n$ 中的点 $x_1, x_2, \dots, x_n$ 映射到空间 $R^k$ 中为 $x_1, x_2, \dots, x_k$。所以，与第 1 节中的公式 $(2)$ 类似，有
$$
\tag{11}
\mathrm{P}^{(x_1, x_2, \dots, x_k)} (A)  = \mathrm{P}^{(x_1, x_2, \dots, x_n)} (A) \{ p^{-1} _k (A) \}
$$
对应的分布函数，由公式 $(10)$ 和公式 $(11)$ 可得以下两个方程：
$$
\tag{12}
F^{(x_{i_1}, x_{i_2}, \dots, x_{i_n})} (a_{i_1}, a_{i_2}, \dots, a_{i_n})  = F^{(x_1, x_2, \dots, x_n)} (a_1, a_2, \dots, a_n)
$$

$$
\tag{13}
F^{(x_1, x_2, \dots, x_k)} (a_1, a_2, \dots, a_k)  = F^{(x_1, x_2, \dots, x_n)} (a_1, a_2, \dots, a_k, +\infin, \dots, +\infin)
$$



### 无限维空间中的概率

在第二章第 3 节我们已经看到如何构造概率论中常见的各种概率域。不过我们仍可以想象某一类问题，在这类问题中基本事件是由无限多个坐标定义的。假定一个由任意基数 $m$ 中的索引 $\mu$ 构成的集合 $M$。我们把实数 $x_{\mu}$ 构成的的系统的总体（其中 $\mu$ 可以遍历整个集合 $M$）
$$
\xi = \{ x_{\mu} \}
$$
称为空间 $R^M$（为了定义空间 $R^M$ 中的一个元素 $\xi$，我们必须将集合 $M$ 中的每一个元素 $\mu$ 与一个实数 $x_{\mu}$ 对应，或者等效地给每一个元素 $\mu$ 赋予一个定义在 $M$ 上的单值实函数 $x_{\mu}$）[^3.3]。如果集合 $M$ 中所包含的是 $n$ 个自然数 $1, 2, \dots, n$，则 $R^M$ 就是普通的 $n$ 维空间 $R^n$。如果集合 $M$ 中是所有的实数 $R^1$，则对应的空间 $R^M = R^{R^1}$ 包含了实变量 $\mu$ 的所有的实函数：
$$
\xi (\mu) = x_{\mu}
$$
现在我们将集合 $R^M$（其中 $M$ 为任意集合）作为基本集合 $E$。令 $\xi = \{ x_{\mu} \}$ 为集合 $E$ 中的一个元素。则我们可以用符号 $p_{\mu_1 \mu_2 \dots \mu_n} (\xi)$ 表示 $n$ 维空间 $R^n$ 中的点 $(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n})$。如果 $E$ 的子集 $A$ 可以表示成如下形式，我们称集合 $A$ 为圆柱集合（cylinder set）：

$$
A = p^{-1} _{\mu_1 \mu_2 \dots \mu_n} (\xi) (A')
$$
其中 $A'$ 是 $R^n$ 的子集。因此，所有圆柱集组成的类（class），与那些可以按如下形式的关系定义的集合组成的类一致：
$$
\tag{1}
f(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n}) = 0
$$
为按照以上关系定义任意圆柱集 $p_{\mu_1 \mu_2 \dots \mu_n} (A)$，我们只需构造一个函数 $f$，使它在 $A'$ 处等于 $0$，并且在除 $A'$ 以外的其他地方等于 $1$（unity）。

当 $A'$ 是波莱尔集合时，对应的这个圆柱集是波莱尔圆柱集（Borel cylinder set）。空间 $R^M$ 中的所有波莱尔圆柱集构成一个域，记为 $\mathcal{E}^{M}$ [^3.4]。

我们把域 $\mathcal{E}^{M}$ 的波莱尔扩展记为 $B \mathcal{E}^{M}$。$B \mathcal{E}^{M}$ 中的集合称为空间 $R^M$ 的波莱尔集合。

稍后我们会给出一种在 $\mathcal{E}^{M}$  上构造和操作概率函数的方法，并且借由扩展定理（Extension THeorem），该方法同样可用于在 $B \mathcal{E}^{M}$ 上构造和操作概率函数。由此方式得到的概率域，在当集合 $M$ 为可数的情况下，可以满足所有的（研究）目的。由此我们可以处理涉及到可数随机变量序列的所有问题。但当 $M$ 不可数时，$R^M$ 中的很多简单又有趣的子集就超出 $B \mathcal{E}^{M}$ 的范围。例如，当集合 $M$ 不可数时，对于所有的索引 $\mu$，那些满足 $x_{\mu}$ 小于某个固定常数的所有元素 $\xi$ 构成的集合，就不属于系统 $B \mathcal{E}^{M}$。

因此，尽可能地把每一个问题转化为一种形式，使得在这种形式下所有基本事件 $\xi$ 的空间只有一个可数的坐标集（a denumerable set of coordinates），这种方法是非常可取的。

假设概率函数 $\mathrm{P}(A)$ 定义在 $\mathcal{E}^{M}$ 上，则我们可以把基本事件 $\xi$ 的每一个坐标 $x_{\mu}$ 都当作一个随机变量。因此这些坐标的每个有限群（group） $(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n})$ 都有一个 $n$ 维的概率函数 $\mathrm{P}_{\mu_1 \mu_2 \dots \mu_n}(A)$ 和与之对应的分布函数 $F_{\mu_1 \mu_2 \dots \mu_n} (a_1, a_2, \dots, a_n)$。显然，对每一个波莱尔圆柱集 $A$：
$$
A = p^{-1} _{\mu_1 \mu_2 \dots \mu_n} (A')
$$
有如下等式成立：
$$
\mathrm{P}(A) = \mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} (A')
$$
其中 $A'$ 是空间 $R^n$ 的一个波莱尔集。这样，空间 $\mathcal{E}^{M}$ 上所有圆柱集的概率函数 $\mathrm{P}$ 可以由空间 $R^n$ 上的所有波莱尔集的优点概率函数 $\mathrm{P} _{\mu_1 \mu_2 \dots \mu_n}$ 确定。然而，对于波莱尔集，概率函数 $\mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} $ 又是由相应的分布函数唯一确定的。由此我们证明了以下定理：

> 所有有限维分布函数 $F_{\mu_1 \mu_2 \dots \mu_n}$ 的集合唯一确定了所有在空间 $\mathcal{E}^{M}$ 中的集合的概率函数 $\mathrm{P}(A)$。如果 $\mathrm{P}(A)$ 定义在 $\mathcal{E}^{M}$ 上，则（根据扩展定理）它由分布函数 $F_{\mu_1 \mu_2 \dots \mu_n}$ 的值在 $B \mathcal{E}^{M}$ 上唯一确定。

接下来读者或许想问：在哪种情况下，一个分布函数 $F_{\mu_1 \mu_2 \dots \mu_n}$ 系统能够先验地定义一个空间 $\mathcal{E}^{M}$ 上（或者空间 $B \mathcal{E}^{M}$）的概率域呢。

首先需要说明，分布函数 $F_{\mu_1 \mu_2 \dots \mu_n}$ 必须满足第二章第 3 节的第 III 个例子（无限概率域的例子）。事实上这些条件包含在分布函数的概念当中。此外，作为本章第 2 节方程 $(13)$ 和 $(14)$ 的结果（注，推测应为第 3 节的方程 $(12)$ 和 $(13)$），我们有如下两个等式关系：
$$
\tag{2}
F_{\mu_{i_1} \mu_{i_2} \dots \mu_{i_n}} (a_{i_1}, a_{i_2}, \dots, a_{i_n})  = F_{\mu_1 \mu_2 \dots \mu_n} (a_1, a_2, \dots, a_n)
$$

$$
\tag{3}
F_{\mu_1 \mu_2 \dots \mu_k} (a_1, a_2, \dots, a_k)  = F_{\mu_1 \mu_2 \dots \mu_n} (a_1, a_2, \dots, a_k, +\infin, \dots, +\infin)
$$
其中 $k < n$，并且 $\begin{pmatrix}
1, 2, \dots, n \\
i_1, i_2, \dots, i_n \\
\end{pmatrix}$ 是一个任意的排列（permutation）。这些必要条件也被证明为是充分条件。这一点由下面的定理可以得出。

基本定理：每一个满足条件 $(2)$ 和 $(3)$ 的分布函数系统，都定义了一个 $\mathcal{E}^{M}$ 上的概率函数 $\mathrm{P}(A)$，它满足公理 I - VI。并且这个概率函数 $\mathrm{P}(A)$ 也可以（通过扩展定理）扩展到空间 $B \mathcal{E}^{M}$ 上。

**证明**

给定满足第二章第 3 节例子 III 的一般条件，以及满足条件 $(2)$ 和 $(3)$ 的分布函数 $F_{\mu_1 \mu_2 \dots \mu_n}$。meige1分布函数都唯一确定了空间 $R^n$ 上的所有波莱尔集的概率函数 $\mathrm{P}_{\mu_1 \mu_2 \dots \mu_n}$ （参考本章第 3 节）。后面的讨论中，我们只关注空间 $R^n$ 中的波莱尔集和空间 $E$ 中的波莱尔圆柱集。

对于每一个圆柱集 $A = p^{-1} _{\mu_1 \mu_2 \dots \mu_n} (A')$，我们令
$$
\tag{4}
\mathrm{P}(A) = \mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} (A'')
$$
由于可以由不同的集合 $A'$ 经过构造得到相同的圆柱集 $A$，所以我们首先需要确定公式 $(4)$ 总是得到相同的 $\mathrm{P}(A)$。

假设 $(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n})$ 为随机变量 $x_{\mu}$ 的有限系统。根据这些随机变量的概率函数 $\mathrm{P} _{\mu_1 \mu_2 \dots \mu_n}$，再结合第 3 节提到的规则，我们可以定义每一个子系统 $(x_{\mu_{i_1}}, x_{\mu_{i_2}}, \dots, x_{\mu_{i_k}})$ 的概率函数 $\mathrm{P}_{\mu_{i_1} \mu_{i_2} \dots \mu_{i_k}}$。由等式 $(2)$ 和 $(3)$ 可得，根据第 3 章内容定义的概率函数与先验给出的函数 $\mathrm{P}_{\mu_{i_1} \mu_{i_2} \dots \mu_{i_k}}$ 相同。现在我们假设圆柱集 $A$ 是通过下面等式定义的：
$$
A = p^{-1} _{\mu_{i_1} \mu_{i_2} \dots \mu_{i_k}} (A')
$$
同时也是通过下面等式定义的：
$$
A = p^{-1} _{\mu_{j_1} \mu_{j_2} \dots \mu_{j_m}} (A'')
$$
其中所有的随机变量 $x_{\mu_i}$ 和 $x_{\mu_j}$ 都属于系统 $(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n})$，这显然不是一个本质上的限制。条件
$$
(x_{\mu_{i_1}}, x_{\mu_{i_2}}, \dots, x_{\mu_{i_k}}) \sub A'
$$
和条件
$$
(x_{\mu_{j_1}}, x_{\mu_{j_2}}, \dots, x_{\mu_{j_m}}) \sub A''
$$
是等价的。因此
$$
\mathrm{P}_{\mu_{i_1} \mu_{i_2} \dots \mu_{i_k}} (A') = \mathrm{P}(A) = \mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} \{ (x_{\mu_{i_1}}, x_{\mu_{i_2}}, \dots, x_{\mu_{i_k}}) \sub A' \} = \mathrm{P} _{\mu_1 \mu_2 \dots \mu_m} \{ (x_{\mu_{j_1}}, x_{\mu_{j_2}}, \dots, x_{\mu_{j_m}}) \sub A'' \} = \mathrm{P}_{\mu_{j_1} \mu_{j_2} \dots \mu_{j_m}} (A'')
$$
由此证明了 $\mathrm{P}(A)$ 定义的唯一性。

接下来证明概率域 $(\mathcal{E}^{M}, \mathrm{P})$ 满足所有的公理 I - VI。公理 I 只要求 $\mathcal{E}^{M}$ 为一个域——这一事实已经在上面的内容中证明。并且，对于任意索引 $\mu$，有如下关系：
$$
E = p^{-1} _{\mu} (R^1) \\
\mathrm{P}(E) = \mathrm{P}_{\mu} (R^1) = 1
$$
由此证明了公理 II 和公理 IV 也满足。最后，由 $\mathrm{P}(A)$ 的定义可得 $\mathrm{P}(A)$ 是非负的（满足公理 III）。

证明公理 V 略微复杂一些。为此我们需要考虑两个圆柱集
$$
A = p^{-1} _{\mu_{i_1} \mu_{i_2} \dots \mu_{i_k}} (A')
$$
和
$$
B = p^{-1} _{\mu_{j_1} \mu_{j_2} \dots \mu_{j_m}} (B')
$$
我们假设随机变量 $x_{\mu_i}$ 和 $x_{\mu_j}$ 属于有限系统（原文为inclusive finite system，inclusive 不知道该怎么翻译） $(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n})$。如果集合 $A$ 和集合 $B$ 没有交集，则以下两个关系
$$
(x_{\mu_{i_1}}, x_{\mu_{i_2}}, \dots, x_{\mu_{i_k}}) \sub A'
$$
和（下面的等式，最后的下标 $k$，或许应该为 $m$，待确定）
$$
(x_{\mu_{j_1}}, x_{\mu_{j_2}}, \dots, x_{\mu_{j_k}}) \sub B'
$$
是互斥的。因此
$$
\mathrm{P}(A + B) = \mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} \{ (x_{\mu_{i_1}}, x_{\mu_{i_2}}, \dots, x_{\mu_{i_k}}) \sub A' \quad \mathrm{OR} \quad \{ (x_{\mu_{j_1}}, x_{\mu_{j_2}}, \dots, x_{\mu_{j_m}}) \sub B' \} = \mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} \{ (x_{\mu_{i_1}}, x_{\mu_{i_2}}, \dots, x_{\mu_{i_k}}) \sub A' \} + \mathrm{P} _{\mu_1 \mu_2 \dots \mu_n} \{ (x_{\mu_{j_1}}, x_{\mu_{j_2}}, \dots, x_{\mu_{j_m}}) \sub B' \} = \mathrm{P}(A) + \mathrm{P}(B)
$$
由此得证公理 V。

此时只剩下公理 VI。令
$$
A_1 \supset A_2 \supset \dots \supset A_n \supset \cdots
$$
为一个圆柱集的递减序列，并且满足以下条件
$$
\lim \mathrm{P}(A_n) = L > 0
$$
我们需要证明，所有集合 $A_n$ 的乘积为非空。在不会实际限制原有问题的情况下，我们可以假设在前 $n$ 个圆柱集 $A_k$ 的定义中，序列 $x_{\mu_{1}}, x_{\mu_{2}}, \dots, x_{\mu_{i_n}}, \dots$ 中只有前 $n$ 个坐标 $x_{\mu_k}$ 存在，即
$$
A_n = p^{-1} _{\mu_1 \mu_2 \dots \mu_n} (B_n)
$$
为简写记，记为
$$
\mathrm{P}_{\mu_1 \mu_2 \dots \mu_n} (B) = \mathrm{P}_n (B)
$$
显然有
$$
\mathrm{P}_n (B_n) = \mathrm{P}(A_n) \geqq L > 0
$$
在每一个集合 $B_n$ 中都有可能找到一个具有闭区间的有界集合 $U_n$，使得
$$
\mathrm{P}_n (B_n - U_n) \leqq \frac{\epsilon}{2^n}
$$
根据以上不等式，对于集合 $V_n$
$$
V_n = p^{-1} _{\mu_1 \mu_2 \dots \mu_n} (U_n)
$$
可得不等式
$$
\tag{5}
\mathrm{P}(A_n - V_n) \leqq \frac{\epsilon}{2^n}
$$
进一步，令
$$
W_n = V_1 V_2 \dots V_n
$$
由公式 $(5)$ 可得
$$
\mathrm{P}(A_n - W_n) \leqq \epsilon
$$
由于 $W_n \sub V_n \sub A_n$，所以有如下关系
$$
\mathrm{P}(W_n) \geqq \mathrm{P}(A_n) - \epsilon \geqq L - \epsilon
$$
如果 $\epsilon$ 足够小，$\mathrm{P}(W_n) > 0$ 并且 $W_n$ 不为空。我们可以在每个集合 $W_n$ 中选择一个坐标为 $x_{\mu} ^{(n)}$ 的点 $\xi^{(n)}$。每个点 $\xi ^{(n + p)}$，其中 $p = 0, 1, 2, \dots$，都属于集合 $V_n$。因此有
$$
(x ^{(n + p)} _{\mu_1}, x ^{(n + p)} _{\mu_2}, \dots, x ^{(n + p)} _{\mu_n}) = p^{-1} _{\mu_1 \mu_2 \dots \mu_n} (\xi^{(n + p)}) \sub U_n
$$
因为集合 $U_n$ 是有界的，所以我们可以从序列 $\{ \xi^{(n)} \}$ 中挑选出一个子序列（采用对角线法，by the diagonal method）：
$$
\xi^{(n_1)}, \xi^{(n_2)}, \dots, \xi^{(n_i)}, \cdots
$$
在这个子序列中，对于任意的 $k$，每一个点的坐标 $x_{\mu_k} ^{(n_i)}$ 都趋向于一个确定的极限 $x_k$。最后我们令 $\xi$ 为集合 $E$ 中的一点，它的坐标为
$$
x_{\mu_k} = x_k \\
x_m = 0, \quad \mu \ne \mu_k \qquad k = 1, 2, 3, \cdots
$$
作为序列 $x_1 ^{(n_i)}, x_2 ^{(n_i)}, \dots, x_k ^{(n_i)}, \quad i = 1, 2, 3, \dots$ 的极限，点 $(x_1, x_2, \dots, x_k)$ 属于集合 $U_k$。因此对于任意 $k$， $\xi$ 属于
$$
A_k \sub V_k = p^{-1} _{\mu_1 \mu_2 \dots \mu_k} (U_k)
$$
因此证明
$$
A = \underset{k}{\mathscr{D}} A_k
$$



### 等价随机变量及多种收敛

从现在开始，我们将专门讨论概率的波莱尔域（Borel fields of probability）。正如在第 2 节中已经解释过的，这一讨论范围的限定实际上并不会构成对我们研究问题的限制。【有点奇怪】

如果两个随机变量  $x$ 和 $y$，$x \ne y$ 的概率等于 $0$，则称这两个随机变量等价（equivalent）。显然，两个等价的随机变量具有相同的概率函数：
$$
\mathrm{P}^{(x)} (A) = \mathrm{P}^{(y)} (A)
$$
因此，它们对应的分布函数 $F^{(x)}$ 和 $F^{(y)}$ 也相同。在概率理论的许多问题中，我们都可以把某一个随机变量替换为任何其他等价随机变量。

现在，令
$$
\tag{1}
x_1, x_2, \cdots, x_n, \cdots
$$
为一个随机变量序列。现在来研究满足序列 $(1)$ 为收敛序列的所有基本事件 $\xi$ 构成的集合 $A$。将满足以下不等式的基本事件 $\xi$ 的集合记为 $A^{(m)} _{n \ p}$：
$$
|x_{n + k} - x_n| < \frac{1}{m} \qquad k = 1, 2, \cdots, p
$$
由此可得
$$
\tag{2}
A = \underset{m}{\mathscr{D}} \underset{n}{\mathscr{G}} \underset{p}{\mathscr{D}}  A^{(m)} _{n \ p}
$$
由第 3 节可知，集合 $A^{(m)} _{n \ p}$ 总是属于域 $\mathcal{E}$。等式 $(2)$ 表明，集合 $A$ 也属于 $\mathcal{E}$。因此，我们可以讨论随机变量序列收敛的概率，因为它总是具有明确的意义。

现在令收敛集合 $A$ 的概率 $\mathrm{P}(A)$ 等于 $1$，则序列 $(1)$ 依概率 $1$ 收敛于一个随机变量 $x$。其中 $x$ 除了等价关系外是唯一确定的。为确定随机变量 $x$，令集合 $A$ 中的
$$
x = \lim x_n \qquad n \rightarrow \infin
$$
且集合 $A$ 外有 $x = 0$。我们需要证明，$x$ 是一个随机变量，即满足 $x < a$ 的所有元素 $\xi$ 构成的集合 $A(a)$ 属于 $\mathcal{E}$。但当 $a \le 0$ 时有
$$
A(a) = A \underset{n}{\mathscr{G}} \underset{p}{\mathscr{D}} \{ x_{n + p} < a \}
$$
相反，当 $a > 0$ 时有
$$
A(a) = A \underset{n}{\mathscr{G}} \underset{p}{\mathscr{D}} \{ x_{n + p} < a \} + \bar{A}
$$
由此，可立即得出**序列 $(1)$ 依概率 $1$ 收敛于一个随机变量 $x$**的结论。

如果收敛序列 $(1)$ 收敛于 $x$ 的概率为 $1$，则称序列 $(1)$ 几乎必然收敛到 $x$ （converges almost surely to x）。但对概率理论来说，另一种收敛的概念可能更为重要。

**定义** ：如果对于任意 $\epsilon > 0$，随机变量序列 $x_1, x_2, \dots, x_n, \dots$  依概率收敛于随机变量 $x$，则当 $n \rightarrow \infin$ 时，概率
$$
\mathrm{P} \{ | x_n - x | > \epsilon \}
$$
趋近于 $0$ [^3.5]。

**I**. 如果序列 $(1)$ 依概率收敛于 $x$ 和 $x'$，则 $x$ 与 $x'$ 等价。事实上
$$
\mathrm{P} \{ |x - x'| > \frac{1}{m} \} \le \mathrm{P} \{ |x_n - x| > \frac{1}{2m} \} + \mathrm{P} \{ |x_n - x'| > \frac{1}{2m} \}
$$
由于对于足够大的 $n$，最后这些概率可以任意小，因此可以得出
$$
\mathrm{P} \{ |x - x'| > \frac{1}{m} \} = 0
$$
并且可立即得出
$$
\mathrm{P} \{ x \ne x' \} \le \sum_m \mathrm{P} \{  |x - x'| > \frac{1}{m} \} = 0
$$
**II**. 如果序列 $(1)$ 几乎必然收敛于 $x$，则它也依概率收敛于 $x$。令 $A$ 为序列 $(1)$ 的收敛集合，则
$$
1 = \mathrm{P}(A) \le \lim_{n \rightarrow \infin} \{ |x_{n + p} - x | < \epsilon, p = 0, 1, 2, \dots \} \le \lim_{n \rightarrow \infin} \mathrm{P} \{ |x_n - x| < \epsilon \}
$$
 由此可以推得依概率收敛。

**III**. 对于序列 $(1)$ 依概率收敛的情况，以下条件既是充分的，也是必要的：对任意 $\xi > 0$，总存在一个 $n$，使得对于任意 $p > 0$，以下等式成立【这里有问题，因为都没有 $p$】：
$$
\mathrm{P} \{ |x_{n + p} - x_n| > \epsilon \} < \epsilon
$$
令 $F_1(a), F_2(a), \dots, F_n(a), \dots, F(a)$ 为随机变量 $x_1, x_2, \dots, x_n, \dots, x$ 的分布函数。如果序列 $x_n$ 依概率收敛于 $x$，分布函数 $F(a)$ 由 $F_n(a)$ 唯一确定。事实上有以下定理

**定理**：如果序列 $x_1, x_2, \dots, x_n, \dots$ 依概率收敛于 $x$，则对应的分布函数 $F_n(a)$ 的序列在 $F(a)$ 的每一个连续点处收敛于 $x$ 的分布函数 $F(a)$。

$F(a)$ 是左连续的单调函数，由连续点处的值唯一确定[^3.6]。由此可得，$F(a)$ 由 $F_n(a)$ 确定。为证明这一定理，假设 $F$ 在点 $a$ 连续。令 $a' < a$，则当 $x < a'$、$x_n \ge a$ 时，必须有 $|x_n - x| > a - a'$。因此有


$$
\tag{3}
\lim \mathrm{P}(x < a', x_n \ge a) = 0, \\
F(a') = \mathrm{P}(x < a') \le \mathrm{P}(x_n < a) + \mathrm{P}(x < a', x_n \ge a) = F_n(a) + \mathrm{P}(x < a', x_n \ge a), \\
F(a') \le \lim \inf F_n(a) + \lim \mathrm{P}(x < a', x_n \ge a), \\
F(a') \le \lim \inf F_n(a)
$$
注：$\lim \inf$ 表示下极限（limit inferior），即序列 $F_n(a)$ 在所有子序列极限中的最小极限值。下文中的 $\lim \sup$ 表示上极限。【感慨一下，翻译到这里时，deepseek已经声名鹊起】

由以上结果类比，当 $a'' > a$ 时有
$$
\tag{4}
F(a'') \ge \lim \sup F_x(a)
$$
因为当 $a' \rightarrow a$，且 $a'' \rightarrow a$时，$F(a')$ 与 $F(a'')$ 都收敛于 $F(a)$，所以由公式 $(3)$ 和 $(4)$ 可得
$$
\lim F_n(a) = F(a)
$$
由此证明以上定理。



[^3.1]: $a_i$ 也可以取无限值 $\pm \infin$。
[^3.2]: 参见第 IV 章第 3 节。

[^3.3]: 参考HAUSDORFF, Mengenlehre, 1927, p. 23。
[^3.4]: 由以上内容可知，波莱尔圆柱集是可以通过公式 $(1)$ 中的关系定义的波莱尔集合。现假定集合 $A$ 和 $B$ 是两个由如下关系定义的波莱尔圆柱集：

$$
f(x_{\mu_1}, x_{\mu_2}, \dots, x_{\mu_n}) = 0 \\
g(x_{\lambda_1}, x_{\lambda_2}, \dots, x_{\lambda_m}) = 0
$$

由此可根据以下关系定义集合 $A + B$，$AB$，以及 $A - B$：
$$
f \cdot g = 0 \\
f^2 + g^2 = 0 \\
f^2 + \omega(g) = 0
$$
其中当 $x \ne 0$ 时 $\omega(x) = 0$，并且 $\omega(0) = 1$。如果 $f$ 和 $g$ 都是波莱尔函数，则 $f \cdot g$ 、$f^2 + g^2$ 和 $f^2 + \omega(g)$ 也是波莱尔函数。因此  $A + B$，$AB$，以及 $A - B$ 都是波莱尔圆柱集。由此我们证明集合 $\mathcal{E}^{M}$ 的系统为一个域。

[^3.5]: 这一概念来源于 Bernoulli，它的完整的、一般性的分析由 E. E. Slutsky 提出（见参考文献 [1]）。
[^3.6]: 事实上 $F_n(a)$ 至多只有可数个间断点（见 LEBESGUE, Leçons sur l’intégration, 1928, p. 50）。因此连续点是处处稠密的（everywhere dense），并且函数 $F(a)$ 在间断点的值由它左连续点的函数值的极限决定。





## 数学期望[^4.1]

### 抽象勒贝格（Lebesgue）积分

令 $x$ 为随机变量，$A$ 为 $\mathcal{E}$ 构成的集合。给定一个正的 $\lambda$，构造以下和式：
$$
\tag{1}
S_{\lambda} = \sum_{k = -\infin} ^{k = + \infin} k \lambda \mathrm{P}\{ k \lambda \le x < (k + 1) \lambda, \quad \xi \sub A \}
$$
如果对每一个 $\lambda$ ，这个级数都绝对收敛，则当 $\lambda \rightarrow 0$时，$S_{\lambda}$ 趋近于一个确定值，且这个确定值为一个积分：
$$
\tag{2}
\int_{\lambda} x \mathrm{P}(d E)
$$
在这一抽象形式下，积分的概念由 Fréchet[^4.2] 提出。它对概率理论是必不可少的（dispensable）。（后文中读者会发现，除去一个常数因子外，变量 $x$ 在假设 $A$ 下的条件数学期望的常规定义与积分 ($2$) 中的定义恰巧相同）。

此处简要介绍下形式 $(2)$ 中的积分的最重要的一些性质。读者会在任何一本关于实变量的习题册中找到它们的证明，尽管这些证明通常都是基于 $\mathrm{P}(A)$ 是空间 $R^n$ 上集合的勒贝格测度（Lebesgue measure）这一假设做出的。将这些证明推广到更广义情形的过程并不包含任何新的数学问题，大多数证明（与勒贝格测度前提相比）都是完全一样的。

I. 如果随机变量 $x$ 在 $A$ 上可积，则它在 $A$ 的每一个子集 $A'$ 上可积（$A' \sub \mathcal{E}$）。

II. 如果 $x$ 在 $A$ 上可积，且 $A$ 可以分解为 $\mathcal{E}$ 上的不超过可数个的不相交集合 $A_n$，则
$$
\int_A x \mathrm{P}(dE) = \sum_n \int_{A_n} x \mathrm{P}(dE)
$$
III. 如果 $x$ 可积，则 $|x|$ 也可积，并且在这种情况下
$$
|\int_A x \mathrm{P}(dE)| \le \int_A |x| \mathrm{P}(dE)
$$
IV. 如果对于每一个事件 $\xi$ ，都有 $0 \le y \le x$ 成立，则 $x$、$y$  也可积[^4.3] ，并且在这种情况下
$$
\int_A y \mathrm{P}(dE) \le \int_A x \mathrm{P}(dE)
$$
V. 如果 $m \le x \le M$，其中 $m$ 和 $M$ 为两个常数，则
$$
m \mathrm{P}(A) \le \int_A x \mathrm{P}(dE)  \le M \mathrm{P}(A)
$$
VI. 如果 $x$ 和 $y$ 可积，且 $K$ 和 $L$ 为两个实常数，则 $Kx + Ly$ 也可积，并且在这种情况下
$$
\int_A (Kx + Ly) \mathrm{P}(dE) = K \int_A x \mathrm{P}(dE)  + L \int_A y \mathrm{P}(dE)
$$
VII. 如果级数
$$
\sum _n \int_A |x_n| \mathrm{P}(dE)
$$
收敛，则级数
$$
\sum _n x_n = x
$$
在除使得 $\mathrm{P}(B) = 0$ 成立的集合 $B$之外， 在集合 $A$ 的任意一点处都收敛。如果在 $A - B$ 外的任意位置都令 $x = 0$，则有
$$
\int_A x \mathrm{P}(dE) = \sum _n \int_A x_n \mathrm{P}(dE)
$$
VIII. 如果 $x$ 和 $y$ 等价（即 $\mathrm{P}\{ x \ne y \} = 0$），则对于任意的 $A \sub \mathcal{E}$，都有
$$
\tag{3}
\int_A x \mathrm{P}(dE) = \int_A y \mathrm{P}(dE)
$$
IX. 如果对于任意的 $A \sub \mathcal{E}$ 等式 $(3)$ 都成立，则 $x$ 和 $y$ 等价。

根据前述的积分定义还可以得出以下通常勒贝格理论中没有的性质：

X. 令 $\mathrm{P}_1(A)$ 和 $\mathrm{P}_2(A)$ 为两个定义在相同 $\mathcal{E}$ 上的概率函数，$\mathrm{P}(A) = \mathrm{P}_1(A) + \mathrm{P}_2(A)$，并且 $x$ 对于 $\mathrm{P}_1(A)$ 和 $\mathrm{P}_2(A)$，在 $A$ 上是可积的，则有
$$
\int_A x \mathrm{P}(dE) = \int_A x \mathrm{P}_1(dE) + \int_A x \mathrm{P}_2(dE)
$$
XI. 每一个有界随机变量都是可积的。



### 绝对数学期望/条件数学期望

令 $x$ 为一随机变量，则积分
$$
\mathrm{E}(x) = \int _E x \mathrm{P}(dE)
$$
称为变量 $x$ 的数学期望（mathematical expectation）。由性质 III、IV、V、VI、VII、VIII、XI，可得

I. $|\mathrm{E}(x)| \le \mathrm{E}(|x|)$；

II. 如果 $0 \le y \le x$，则 $\mathrm{E}(y) \le \mathrm{E}(x)$；

III. $\inf (x) \le \mathrm{E}(x) \le \sup (x)$；

IV. $\mathrm{E}(Kx + Ly) = K \mathrm{E}(x) + L \mathrm{E}(y)$；

V. 如果级数 $\sum_n \mathrm{E}(|x_n|)$ 收敛，则 $\mathrm{E}(\sum_n x_n) = \sum_n \mathrm{E}(x_n)$；

VI. 如果 $x$ 和 $y$ 等价，则 $\mathrm{E}(x) = \mathrm{E}(y)$；

VII. 每个有界的随机变量都有数学期望。

由积分的定义可得
$$
\mathrm{E}(x) = \lim \sum^{k = + \infin} _{k = - \infin} k m \mathrm{P} \{ km \le x < (k + 1) m \} \\
= \lim \sum^{k = + \infin} _{k = - \infin} \{ F((k + 1)m) - F(km) \}
$$
第二行是 Stieltjes 积分[^*]的定义
$$
\tag{1}
\int ^{+\infin} _{- \infin} a dF^{(x)} (a) = \mathrm{E}(x)
$$
公式 $(1)$ 可以作为数学期望 $E(x)$ 的定义。

现在令 $u$ 为基本事件 $\xi$ 的函数，且 $x = x(u)$ 为 $u$ 的单值函数，则
$$
\mathrm{P} \{ km \le x < (k + 1)m \} = \mathrm{P}^{(u)} \{ km \le x(u) < (k + 1)m \}
$$
其中 $\mathrm{P}^{(u)} (A)$ 是变量 $u$ 的概率函数。由积分的定义可得
$$
\int _E x \mathrm{P}(dE) = \int _{E^{(u)}} x \mathrm{P}^{(u)} (d E^{(u)})
$$
因此
$$
\tag{2}
\mathrm{E} (x) = \int _{E^{(u)}} x(u) \mathrm{P}^{(u)} (d E^{(u)})
$$
其中 $E^{(u)}$ 表示所有 $u$ 的可能取值构成的集合。

特别地，当 $u$ 本身是个随机变量时，可得
$$
\tag{3}
\mathrm{E}(x) = \int _E x \mathrm{P}(dE) = \int _{R^1} x(u) \mathrm{P}^{(u)} (d R^1) = \int ^{+ \infin} _{- \infin} x(a) d F^{(u)} (a)
$$
当 $x(u)$ 连续时，公式 $(3)$ 的最后一项积分为常规的 Stieltjes 积分。此处有必要指出，即使数学期望 $\mathrm{E}(x)$ 不存在，但积分
$$
\int ^{+ \infin} _{- \infin} x(a) d F^{(u)} (a)
$$
是存在的。$\mathrm{E}(x)$ 存在的充要条件是，积分
$$
\int ^{+ \infin} _{- \infin} |x(a)| d F^{(u)} (a)
$$
是有限的[^4.4]。

如果 $u$ 是空间 $R^n$ 中的点 $(u_1, u_2, \dots, u_n)$，则由公式 $(2)$ 可得
$$
\tag{4}
\mathrm{E}(x) = \int \int \cdots \int _{R^n} x(u_1, u_2, \dots, u_n) \mathrm{P}^{(u_1, u_2, \dots, u_n)} (d R^n)
$$
前文已经证明过，条件概率 $\mathrm{P}_B (A)$ 具有所有概率函数的性质。对应的积分
$$
\tag{5}
\mathrm{E} _B (x) = \int _E x \mathrm{P} _B (dE)
$$
称为随机变量 $x$ 在事件 $B$ 下的条件数学期望。由于
$$
\mathrm{P} (\bar{B}) = 0 \\
\int _{\bar{B}} x \mathrm{P} _B (dE) = 0
$$
所以由公式 $(5)$ 可得
$$
\mathrm{E}_B (x) = \int _E x \mathrm{P} _B (dE) = \int _B x \mathrm{P} _B (dE) + \int _{\bar{B}} x \mathrm{P} _B (dE) = \int _B x \mathrm{P} _B (dE)
$$
已知当 $A \sub B$ 时有
$$
\mathrm{P}_B (A) = \frac{\mathrm{P} (AB)}{\mathrm{P} (B)} = \frac{\mathrm{P} (A)}{\mathrm{P} (B)}
$$
由此得
$$
\tag{6}
\mathrm{E} _B (x) = \frac{1}{\mathrm{P}(B)} \int _B x \mathrm{P} (dE)
$$

$$
\tag{7}
\int _B P(dE) = \mathrm{P} (B) \mathrm{E} _B (x)
$$

由公式 $(6)$ 和以下等式
$$
\int _{A+B} x \mathrm{P} (dE) = \int _{A} x \mathrm{P} (dE) + \int _{B} x \mathrm{P} (dE)
$$
最终可得
$$
\tag{9}
\mathrm{E} _{A+B} (x) = \frac{\mathrm{P} (A) \mathrm{E} _A (x) + \mathrm{P} (B) \mathrm{E} _B (x)}{\mathrm{P} (A + B)}
$$
特别地，有如下等式
$$
\tag{9}
\mathrm{E} (x) = \mathrm{P}(A) \mathrm{E} _A (x) + \mathrm{P} (\bar{A}) \mathrm{E} _{\bar{A}} (x)
$$






### 切比雪夫（Tchebycheff）不等式

### 收敛判据

### 数学期望对参数的微分和积分







[^4.1]: 和第三章第 $5$ 节一样，本章及之后的章节的研究内容都是在波莱尔概率域上开展的。
[^4.2]: FRÉCHET, Sur l’intégrale d’une functionnelle étendue à un ensemble abstrait, Bull. Soc. Math. France v. 43, 1915, p. 248.
[^4.3]: 假设 $y$ 是一个随机变量，按照积分理论的术语来说，$y$ 对于 $\mathcal{E}$ 是可测的。

[^*]: Stieltjes积分，以荷兰数学家**托马斯·约翰内斯·斯蒂尔杰斯（Thomas Joannes Stieltjes）**的名字命名，是黎曼积分的一种推广。它允许对一个函数进行积分，而不仅仅是对变量（如黎曼积分中的 $dx$）进行积分。这使得Stieltjes积分在概率论、泛函分析和物理学等领域中成为一个强大的工具。

[^4.4]: 参考 V. GLIVENKO, Sur les valeurs probables de fonctions, Rend. Accad. Lincei v. 8, 1928, pp. 480-483.







## 条件概率和数学期望

### 条件概率

### 波莱尔悖论的解释

### 随机变量的条件概率

### 条件数学期望



## 独立性；大数定律

### 独立性

### 随机变量的独立性

### 大数定律

### 关于数学期望概念的说明

### 强大数定律；级数收敛



## 附录：概率理论中的零一定律

## 参考文献

## 关于补充参考文献的说明

## 补充参考文献



## 注



[^3.6]:
