# task02 图的基本表示和特征工程



## 一、基本表示

### 1.1、图的基本表示

**节点（N）**： nodes vertices

**边（E）**： links，edges

**图：G（N，E）**：network，graph

### 1.2 本体图（Ontology）

- 取决于将来想解决的问题
- 需要提前设计好节点的类型，以及节点之间存在的连关系

![image-20230217104955761](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171049822.png)

### 1.3 图的种类

有向图：连接是有方向的

无向图：连接是无方向的

异质图：节点存在不同的类型，连接也存在不同的类型，如讲到的红楼梦知识图谱

二分图（Bipartite graph）：含有两类节点特殊的异质图，如用户和电影，菜谱和食材。

- 展开二分图：![image-20230217111000748](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171110802.png)

无权图：连接非1即0

带权图：连接



### 1.4 节点连接数

节点连接数（Node degree）：指的是一个节点与其它节点相连的边的数量

<img src="https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171817007.png" alt="image-20230217181701913" style="zoom:80%;" />

### 1.5 图的矩阵表示

**邻接矩阵**，各种工具一般都对矩阵进行处理，相当于计算机可以理解的形式

无向图：对称阵，主对角线为0

- 度数：$k_{i}=\sum_{i=1}^N A_{i j} =\sum_{i j}^N A_{i j}$（按行按列求和均可）
- 连接总数：$L=\frac{1}{2} \sum_{i=1}^N k_i=\frac{1}{2} \sum_{i j}^N A_{i j}$



有向图：非对称阵，主对角线为0

- 入度：按行求和 $k_j^{ \text { in } } =\sum_{i}^N A_{i j}$
- 出度：按列求和 $k_i^{ \text { out } } =\sum_{j}^N A_{i j}$

- 连接总数：$L=\sum_{i=1}^N k_i^{ \text { in }}=\sum_{j=1}^N k_j^{\text {out}}=\sum_{i, j}^N A_{i j}$



如果存在自己指向自己的连接，主对角线则不为0



稀疏矩阵：如社交网络，使用邻接矩阵表示，会浪费大量空间。



**连接列表**（list of edges）：只记录存在连接的节点对

**邻接列表**（Adjacency list）：只记录相关连接的节点，每个节点占用一行。进一步压缩，但不损失信息。

<img src="https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171326701.png" alt="image-20230217132639532" style="zoom:80%;" />

### 1.6 其他类型的图

![image-20230217134605288](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171346357.png)

### 1.7 图的连通性

- Connected graph ：如果能满足任意两个节点能到达的图

- Disconnected graph：不能满足任意两个节点能到达的图
  - Giant Component：最大连通域
  - Isolated node：孤立节点

<img src="https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171339373.png" alt="image-20230217133949323"  />

如果是连通的，则会打破block-diagonal（分块对角）的限制

- Strongly connected directed graph（强连通图）：有向图中，任意两个节点可以互相到达

- Weakly connected directed graph（弱连通图）：忽略方向之后，如果任意两个节点能互相到达

- Strongly connected compoinents(SCCs)：强连通域，任意两个节点能互相到达的子图

  - In-component：节点指向SCC

  - Out-component：节点指出SCC

  - 进行SCC的分解十分重要

    

六度空间理论

## 二、传统图机器学习（人工特征工程+机器学习）



抽取D个特征，编码为D维度向量。然后交给模型学习，由进行模型评估特征的好坏。

特征分为属性特征和连接特征。本讲所讲的特只是连接特征



### 节点特征工程

#### 流程

- 给出图 G = （V，E）

- 学习得到一个函数 f（V） =  R  （输入某个节点D维向量，输出该节点是某类的概率）

- 半监督节点分类：由已经知道的节点推测未知的节点。

  

#### Node degree

- 节点的相邻节点个数，各相邻节点平等对待

Eigenvector Centrality(特征向量 节点重要度)

- 如果相邻的节点很重要，那么该节点就很重要

- $c_v=\frac{1}{\lambda} \sum_{u \in N(v)} c_u$ （ $\lambda$ 是常数），相邻节点的特征向量重要度的和
- 本质上是个递归问题
- $\lambda c = Ac$  等价于求邻接矩阵A的特征向量c，和特征值 $\lambda$  

**Betweenness centrality**

- 寻找交通咽喉，必经之地 
- $c_v=\frac{1}{\lambda} \sum_{u \in N(v)} c_u$ 

**Closeness centrality**

- 去哪都远

- $c_v=\frac{1}{节点v到其他节点u最短路径长度求和} $ 

**Clustering coefficient（集群系数）**

- 有多抱团,

- $e_{v} = \frac{v节点相邻节点两两也相连个数（三角形个数）}{v节点相邻节点两两对数}$
- ![image-20230217181008690](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171810868.png)

Graphlet count vector(GDV)	

- Graphlets（图元）：一系列连通的非同构子图

![image-20230217181234990](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171812059.png)



### 连接层次的特征工程

通过已知连接，补全未知连接

- 方案

  - 直接提取link的特征，把link变成D维向量

  - 把link两端的节点的D维向量拼在一起（丢失link本身的连接结构信息）

#### **Two formulation**

- 不随时间变化（客观静态图，如蛋白质，分子）：随机删掉一部分连接，然后把它们预测出来（半监督学习）
- 随时间变化（如论文引用，社交网络）：用上一个时间区段(或时刻)的图，去预测下一个时间区段（或时刻）的图的L个连接，选出，Top n个连接，然后与真实的n个连接进行比较。
  - 提取连接特征——》D维向量——》评估分数——》排序——》选出top n——》与真实值比较进行评估

#### 连接特征分类

##### 1、基于两节点距离（Distance-based feature）

- 只看最短路径长度，忽略个数

##### 2、基于两节点局部信息（Local neighborhood overlap）

![image-20230217105015819](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171050906.png)



limitation：两个节点没有共同好友，它们仍然可能是相连的

##### 3、基于两节点在全图的连接信息（Global neighborhood overlap）

- **Katz index** 

  - 节点u和节点v之间长度为k的路径个数是$A^{k}_{uv}$矩阵得第u行第v列元素
  - 通过邻接矩阵的幂进行运算$P^{(K)}=A^{k}$

  ![image-20230216210314879](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171844923.png)

  $S_{v_1v_2} = $

###  全图层次的特征工程

提取出的特征应反映全图结构特点

####  4.1、Bag of  Words思想

一篇文章的每个词列出来，计算每个词出现的次数

- Bag of node：只看第i个node有没有，不看连接结构

  - ![image-20230217105107021](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171051068.png)

- Bag of node degree 只看Node Degree个数，不看节点和连接结构

  - ![image-20230217105122215](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171051282.png)

- **Graphlet Features**：计算全图中Graphlets的个数

  - $(f_{G})_i$=#($g_{i}$⊆G) for i =1,2,⋯ ,$n_k$表示第*i*个Graphlet在全图中的个数

  - 可以存在孤立的节点；计数全图Graphlet个数，而非特定节点邻域Graphlet个数

  - ![image-20230217105128650](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171051710.png)

    

**Graphlet Kernel**

- $K(G,G')=f_{G}^{T}f_{G'}$ 两个Graphlet count vector数量积（标量），如果两个图size不一样时，需要进行归一化

- 不常用的原因是，需要进行子图匹配，复杂度高，是np难问题。



####  4.2、**Weisfeiler-Lehman Kernel**

使用颜色微调的方法（Color refinement），迭代地丰富“节点词库”

- 先给每个节点分配统一的颜色

- 把每个节点的颜色，以及它周围节点的颜色，进行哈希，编码成一个编号补充到词汇表中，数全图中有多少这样的编号
  - $c^{(k+1)}(v)=HASH(\{c^{(k)}(v),{c^{(k)}(u)}_{u∈N(v)}\})$
- 重复K步，表示$c^{(K)}(v)$表示 K-hop 的连接

计算十分高效，线性复杂度，只关注非零元素



**Kernel methods**

- 使用kernel代表图的结构而不是feature vectors
- $K(G,G')=ϕ(G)^{T}ϕ(G')$ 两个向量的数量积（标量）
- 一旦kernel一定，现成的机器学习库（如SVM）就可以使用



下面是Weisfeiler-Lehman Kernel的计算过程

![image-20230217105900933](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171059984.png)

![image-20230217105916536](https://hc-images-1307048923.cos.ap-guangzhou.myqcloud.com/images/202302171059611.png)































