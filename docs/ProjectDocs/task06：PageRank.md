# PageRank



## 历史地位

- 造就了传奇的谷歌公司
- 搜索引擎、信息检索、图机器学习、图数据挖掘：必读论文
- 线性代数的优秀应用典范
- 改变世界的十大IT论文（图灵机、香农信息论、维纳控制论、比特币白皮书、PageRank、AleNet、ResNet、Alpha Go、 Transformer、AlphaFold2）



## PageRank概述

将整个Web视为一张图，网页之间存在关联，而不是孤立的个体

- Nodes  = web pages
- Edges = hyperlinks

时代的改变

- 现在网页可以随时生成的
- dark matter（暗物质），微信朋友圈等无法爬取得到
- 传统的navigational（导航链接式网页）变为transactional（短视频、电商交互式网页）

表示为图的结构

- Web 、论文引用 、百科词条引用



- **无标度网络（Scale-Free）**：少数称之为Hub点的节点拥有极其多的连接，而大多数节点只有很少量的连接

- 使用link structure定量表示网页的重要度

- Links as Votes，通过In-coming links 即其他人引用的次数进行投票，来自其他重要网页的引用更重要

- Recursive（递归） question



##  理解PageRank（评价节点重要度）的五个角度

迭代求线性方程组 ：重要节点引出的稀少链接，权重更高

- 网页$i$的重要度$r_{i}$,出度为$d_{i}$，则每一条链接的投票为 $\frac{r_{i}}{d_{i}}$
- 网页$j$的重要度为所有入度链接的投票值之和**$r_{j}= \sum_{i \rightarrow j}\frac{r_{i}}{d_{i}}$**

- 可扩展性差（求逆矩阵）

迭代左乘M矩阵：将重要度写成矩阵形式，做成重要度矩阵

- 如果网页$i$的出度为$d_{i}$，则从网页$i$指向网页$j$，**概率矩阵**(**column stochastic matrix**)$ M_{ji} = \frac{1}{d}$,   每列的求和为1
- **Rank vector** $r$是 $n$维向量，其中$r_{i}$是网页$i$的PageRank值
- 所有网页PageRank值求和为1  
- **$r =  M \cdot r$**  

矩阵的特征向量

- $1 \cdot r = M \cdot r$   则 $r$是$M$的特征值为1的特征向量
- 从任意一个向量$u$开始，不断左乘$M$矩阵，最终会稳定收敛，即得到M矩阵的主特征向量，即PageRank

- 收敛性分析：对于Column Stochastic矩阵，由Perreon-Frobenius定理，最大的特征值为1，存在唯一的主特征向量（向量所有元素求和为1），向量所有元素求和为1

随机游走

- 一个随机游走的浏览者，在有向图中随机游走，计数求和，每个网页浏览的次数越多说明该网页越重要。当浏览次数足够多，归一化为概率就是PageRank的值。

马尔可夫链

- 由状态和状态之间的转移表示，每个节点表示一种状态，节点之间的连接表示状态的转移。
- PageRank $\iff$ Stationary Distributions of Markov Chains

## 求解PageRank

推荐使用迭代左乘M矩阵，幂迭代。转化为矩阵乘法，优化比较多。

（迭代求线性方程组、矩阵的特征向量都是$O(n^3)$，马尔科夫和求解特征向量等价，随机游走需要模拟很多游走。消耗资源大。）

计算步骤

- Initialize: $r^{(0)}=[1/N,......,1/N]^{T}$

- Iterate: $r^{(t + 1)} = M \cdot r^{(t)}  \iff  r_{j}^{(t+1)} = \sum_{i -> j}\frac{r_{i}^{(t)}}{d_{i}}$（大约50次迭代可以达到收敛）
- Stop when |$r^{(t +1) }- r^{(t)}|_{1} \leq  \varepsilon$ ($L_1$范数可以使用其他的，如欧式距离)  

## 收敛性分析

- For irreducible  and aperiodic    Markov chains    (reduceible Markov chains：节点彼此孤立   periodic  Markov chains：周期性震荡 ) 
  - A unique stationary distribution  $\pi$ exists
  - All initial distributions $\pi_0$ converge to $\pi$
  - 所以可以收敛到稳定值；且不同初始值，可以收敛到同一个结果

- 出现的问题

  - dead ends 死胡同节点，PageRank最后都为0   （某些列为0，数学角度存在问题不收敛）

  - Spider traps爬虫仅指向自己，PageRank最后都为1 （数学原理没问题，特征值，特征向量仍然存在，仍能收敛，但是求出的PageRank值无意义）

  - 有多个彼此独立的连通域

- Solution to Spider Traps 每一步随机游走，有$\beta$概率按照链接进行随机游走，$1 - \beta$概率被随机传送到任意节点

- Solution to dead ends  百分百被传送走到其他节点

- PageRank解决方案：

  ​                                                        $r_j = \sum_{i \rightarrow j}\beta\frac{r_{i}}{d_{i}} + (1 - \beta)\frac{1}{N}$

  其中**Damping Factor**（阻尼系数）：$\beta$  ，事件中取0.8，0.9

- 等价为Google Matrix：

  ​                                                       $G = \beta M + (1 - \beta)[\frac{1}{N}_{N\times N}]$

  得到递归问题 $r = G \cdot r$

- RandomWalk只是一种思考角度，不用于真正求解。



PageRank升级变种

- MapReduce可以并行计算PageRank



## PageRank计算相似度（可用以推荐系统）

- Goal：寻找与指定节点最相似的节点

- 基本假设：被同一个用户访问过的节点，更可能是相似的

- 将“随机传送到任一节点”优化为“随机传送到指定的一些节点”或“随机传送到指定的一个节点”，用随机游走的访问次数反映亲疏远近

  （随机游走考虑了所有情况，如两个用户有多个用户，有向连接，无向连接，节点的度数不同等）

  - PageRank （评价节点的重要度）：随机传送到任意节点

  - Personalized PageRank （PPP）：随机传送到指定的**一些**节点

  - Random Walks with Restart：随机传送到指定的**一个**节点









