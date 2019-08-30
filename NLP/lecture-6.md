这篇主要是介绍论文[A Fast and Accurate Dependency Parser using Neural Networks](https://nlp.stanford.edu/pubs/emnlp2014-depparser.pdf)

Greedy Transition-based Dependency Parsing算法：

arc-standard system(Nivre, 2004)，是最流行的transition-based system。

>在这个系统中，假设有：
>
>configuration c = (s, b, A)，s: 一个stack；b：一个buffer；A：一组依赖弧；
>
>步骤：
>
>1. 对c进行初始化：对于一个语句$w_1,...,w_n$，$s=[\text {ROOT}]$，$b=[w_1,...,w_n]$，$A=\emptyset$，如果b为空并且stack值包含一个节点ROOT，则终止；
>   1. 标识说明$s_i$：$s_i(i=1,2,...)$作为$i^{th}$（第i个）stack中最顶部的元素；
>   2. 标识说明$b_i$：$b_i(i=1,2,...)$作为$b^{th}$（第i个）buffer中的元素；
>2. 进行转移，分三类，LEFT-ARC($l$)，RIGHT_ARC($l$)，SHIFT：
>   1. LEFT-ARC($l$)：增加弧线（弧线标签为$l$）$s_1\to s_2$，并且将$s_2$从stack中移除，进行此次转移的前提条件是$|s|\ge 2$
>   2. RIGHT-ARC($l)$：增加弧线（弧线标签为$l$）$s_2\to s_1$，并且将$s1$从stack中移除，进行此次此次转移的前提条件是$|s|\ge 2$；
>   3. SHIFT：将$b_1$从buffer中挪到stack，进行词操作的前提条件是$|b|\ge 1$。
>
>有两点点需要注意的是：
>
>1. 在dependency中，不能存在环型依赖，也不能存在交叉依赖，例如下图中，如果good到has还有一个向左的箭头，那就出现了环形依赖（has->control, control->good->has)；如果有control到He的向左箭头，那就出现了交叉，算法也会失效；
>
>2. 在最后才进行ROOT到$s_1$的依赖操作；
>
>   ![1567131045009](..\images\1567131045009.png)
>
>总共会有$|\mathcal{T}|=2N_l+1$次转换，其中$N_l$是标签的总数，可以这么理解：每一次的向左或者向右的弧线都伴随着两次转换，一次是shift，一次是画弧线。额外的一次就是初始化的操作。
>
>而如何来判断进行哪个transition就是一个问题，先来看看我们有哪些数据：所有的词和他们对应的POS tag（例如：has\VBZ)；2. 弧线；3. 一个词在转换前在stack和buffer中的位置。
>
>常见的方法是使用stack和buffer中1~3个词以及他们的POS和弧线标签，组成指示特征，用这些特征去做训练，存在的问题就是：稀疏；手工特征会不完整，词越多越稀疏，越少越不完整；由于稀疏，计算起来比较昂贵；

本模型就是基于上面指示特征的问题，借鉴word2vec的方法，采用新的representation方法来学习tag、pos的向量表示，将稀疏的特征矩阵换换位dense matrix。

模型的输入就是$(c_i, t_i)$，$c_i$就是在第$i$词转换前的一个configuration（即stack、buffer状态）；$t_i$就是第$i$此具体的转换操作（$t_i\in \mathcal {T}$，这里有个疑问，每个sentence的$\mathcal T$应该都是不一样的，因为每个语句的依赖数量是不一样的，即$N_l$是不一样，这个值是取最大值还是每次batch可以不同，而且在预测时如何做呢？target的大小不固定，按理$t_i$只需要表示三个值即可：shift/left/right）。网络架构示意图：

![1567132155242](..\images\1567132155242.png)

上面省了一步就是投射，输入肯定是词和tag、arc label的one-hot向量，投射成$x$的嵌入向量表示。

一些计算步骤：

- $x^w=[e_{w_1}^w;e_{w_2}^w;...;e_{w_{n_w}}^w]$，表示词向量输入，拼接起来的$n_w$个词的向量，其他两个也类似；
- $W_1^w\in \mathbb{R^{d_h\times (d·n_w)}}$，其他两个类似，也只是$n_w$换一下，其中$d$是词向量的维度，其实这三类的表示向量维度可以不同。

值得注意的是，这上面的$h$的激活函数使用的是cube，而不是传统的tanh等激活函数，解释是，这样就会有$x^w\times x^t\times x^l$的存在，变相的增加了是三个元素组合的特征。

其中$x^w$是词的向量表示（论文中大小是$n_w$=18个元素）；$x^t$是tag的向量表示（论文中大小是$n_w$=18个元素）；$x^l$是label的向量表示（12），具体元素取值见下面描述：

- $x^w$: $n_w=18$:
  - top 3 words on stack and buffer：$s_1, s_2, s_3, b_1, b_2, b_3$;
  - the first and second leftmost/rightmost children of the top 2 words on the stack：$lc_1(s_i), rc_1(s_i), lc_2(s_i), rc_2(s_i)，i=1,2$；
  - the leftmost of leftmost / rightmost of rightmost children of the top two words on the stack：$lc_1(lc_1(s_i)), rc_1(rc_1(s_i))，i=1,2$
- $x^t$: $n_t=18$，是$x^w$里面词的POS tags；
- $x^l$: $n_l=12$， the corresponding arc labels of words excluding those 6 words on the stack/buffer for $S_l$；$这块没明白$

训练时，$x^w$可以是预训练好的word vector，而$x^t$和$x^l$会按照分布随机初始化。

模型的损失函数是：

![1567133602479](..\images\1567133602479.png)



问题：

1. 在$x^{\{w,t,l\}}$中，如果元素数不够怎么办？

   **Ans**：会补成特定字符，这份实现中是NULL_TOKEN（\<null\>)，包括leftmost/rightmost children都是如此，如果不足则补成NULL_TOKEN；

2. 模型的输出$t_i$到底是什么值？每次batch都不同？预测怎么搞的？$n_l$应该是固定的，因为网络输入参数$W^t$的大小为$\mathbb{R^{d_h\times (d·n_t)}}$也是固定大小；

   **Ans**: 目标就是0/1/2（分别表示left arc、right arc、shift）；targets的大小就是$n\times 3$，$n$是输入参数数量；$t_i$并不是$n_l$，$t_i$是目标而$n_l$是输入参数中的label，输出的target是这次是什么transition，而label是表示的dep；

3. 一个词leftmost和rightmost到底是什么？是箭头所指的最右最左端的词么（弧线传递？）？

   **Ans**：是弧线传递；但并不是最右或最左（就是传递到头），而是右边或左边一个、两个，如果是leftmost/rightmost(leftmost/rightmost)，则是左边或右边一个词的左边或右边一个；

4. $S_l$到底是什么值？

   **Ans**：$S_l$是dep集合，是依赖词的依赖关系；

5. （-0.01，0.01）使用均匀分布还是正态分布？

   **Ans**：这个实现是正态分布，记得以前看的一本书里面也是正态分布；

代码参看实现：https://github.com/sevenguin/dependency_parsing_tf