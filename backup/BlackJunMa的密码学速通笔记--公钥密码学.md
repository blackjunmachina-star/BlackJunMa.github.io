### 前置定义

##### 公钥IND-CPA安全

##### 公钥IND-CCA安全

##### 公钥OW-CPA安全


### DL为基础的算法

可以定义三个基于离散对数的问题：

##### 离散对数问题(Discrete Logarithm Problem,DLP)
给定一个 $N$阶循环群 $G$的生成元 $g$以及元素 $h\in G$，找出整数 $x$使得 $g^x=h$，此时称 $x=\log_{G,g}h$为离散对数。

##### 计算性Diffie-Hellman问题 (CDH)

若在 $G=\langle g\rangle$中随机选取 $h_1,h_2$，计算

$$h_3=g^{\log_{G,g}h_1\cdot\log_{G,g}h_2}$$

是否简单可行

##### 判定性Diffie-Hellman问题 (DDH)

给予了一个 $G=\langle g\rangle$中的三元组 $(A=g^a,B=g^b,C)$，判定是否 $C=g^{ab}$。

##### Hashed Diffie-Hellman问题 (HDH)

判定 $G$中的三元组是否符合情况 $(g^a,g^b,H(g^{ab}))$，其中 $H$为Hash函数。

##### Strong Diffie-Hellman问题 (SDH)

假设可以使用Oracle判定 $u,v$两个数是否符合 $u^a=v$，给定 $g^a,g^b$，计算 $g^{ab}$。


接下来我们定义一下

求解DLP具有以下的算法：

##### 小步大步算法
(1)定义 $B=\lfloor\sqrt{N}\rfloor$，计算 $g^0,g^B,\dots,g^{\lfloor N/B\rfloor B}$
(2)对 $(i,g^{iB})$二元组进行排序
(3)对 $0\leq j\leq B-1$计算$hg^{-j}$，若存在 $hg^{-j}=g^{iB}$，则 $\log_{G,g}h=iB+j$。

该算法的复杂度为 $O(N^{1/2}\log N)$。

##### Pohlig-Hellman算法

设 $N=p_1^{l_1}\dots p_r^{l_r},p_1\gt\dots\gt p_r$，已知

$$h_i=h^{N/p_i^{l_i}}=(g^{N/p_i^{l_i}})^x=g_i^x$$

如果 $x_i=\log_{G,g_i}h_i$，则 $g_i^x=h_i=g_i^{x_i}$可以推出 $x\equiv x_i \mathrm{mod}p_i^{l_i}$，可以使用CRT复原出 $x$。该算法的复杂度为 $O(\sqrt{p_1}\log^lN),l\geq 1$。

##### Pollard算法

设 $f:G\to G$为一个随机的函数， $g_0\in G,g_{i+1}=f(g_i)$。此时，必然存在 $i_0\lt j_0$使得 $g_{i_0}=g_{j_0}$。此时令 $i'=\lceil i_0/(j_0-i_0)\rceil(j_0-i_0)$，这样有 $g_{i'}=g_{2i'}$。于是可以计算一系列 $(g_i,g_{2i}),i\geq 1$。

将 $G$分割成为大小基本一致的子集 $G=S_1\cup S_2\cup\dots\cup S_t$，随机选取 $2t$个整数 $a_1,b_1,\dots,a_t,b_t$，定义

$$f(x)=xg^{a_i}h^{b_i},x\in S_i$$

设 $g_{j}=g^{u_j}h^{v_j}$，这样可以计算得到

$$x=(u_{2i}-u_i)(v_i-v_{2i})^{-1}\mathrm{mod} N$$

该算法为 $O(N^{1/2}\log N)$的。