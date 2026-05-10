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

基于DLP的困难性，我们定义Diffie-Hellman密钥交换协议和El-Gamal方案如下：

##### Diffie-Hellman密钥交换协议

(0) $G=\langle g\rangle,|G|=q$
(1) $A:a\leftarrow\mathbb{Z}/q\mathbb{Z},h_1=g^a;A\to B: h_1$
(2) $B:b\leftarrow\mathbb{Z}/q\mathbb{Z},h_2=g^b;B\to A: h_2$
(3) $A:K=h_2^a;B:K=h_1^b$


##### El-Gamal方案

 $(G,q,g)\leftarrow\mathcal{G}(1^n)$:$G=\langle g\rangle,|G|=q$.
 $(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n):x\leftarrow\mathbb{Z}/q\mathbb{Z},h=g^x,\mathrm{pk}=(G,q,g,h),\mathrm{sk}=x$.

 $(c_1,c_2)\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m):y\leftarrow\mathbb{Z}/q\mathbb{Z},c_1=g^{y},c_2=mh^y$

 $m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(c_1,c_2):m=(c_1^{-1})^xc_2$

##### 若CDH难解，则El-Gamal方案是OW-CPA的

令 $S,A$为仿真者和攻击者。假设El-Gamal不是OW-CPA的，于是$A$可以从 $(c_1,c_2)$复原出 $m=c_1^{-x}c_2$。

(0) $S:(g,g^x,g^y),G=\langle g\rangle,1\lt x,y\lt q$
(1) $S\to A:(\mathrm{pk}=(g,g^x),(c_1,c_2)=(g^y,h)),h\in G$
(2) $A\to S:m=c_1^{-x}c_2=hg^{-xy}$
(3) $S:hm^{-1}=g^{xy}$

于是这说明 $S$在仅仅知道 $g,g^x,g^y$的情况下得知了 $g^{xy}$，即CDH易解。 $\square$

##### 若DDH难解，则El-Gamal方案是IND-CPA的

假设El-Gamal不是IND-CPA的。

(0) $S:(g,g^x,g^y,g^z),G=\langle g\rangle,1\lt x,y,z\lt q$
(1) $S\to A:\mathrm{pk}=(g,g^x)$
(2) $A\to S:m_0,m_1$
(3) $S:b\leftarrow\{0,1\},c_1=g^y,c_2=g^zm_b$
(4) $S\to A:c^{\ast}=(c_1,c_2)$
(5) $A\to S:b'$

由于El-Gamal在假设中不是IND-CPA， $A$得到 $b'=b$在 $g^{z}=g^{xy}$的时候，否则 $A$不能在猜测 $b$的时候获得优势，即DDH易解。

除此之外，El-Gamal无论如何不是IND-CCA安全的，设 $C,A$为挑战者和攻击者，假设 $A$可以通过 $C$在选取测试明文前后调用 $\mathrm{Dec}$ Oracle，但不能直接输入测试密文 $c^{\ast}$，则

(1) $C\to A:c^{\ast}=(g^y,h^ym_b)$
(2) $A\to C:(g^zg^y,h^zh^ym_b)$
(3) $C\to A:m_b$

由此 $\mathrm{Pr}[\mathrm{PubK}^{\mathrm{cca}}_{A,\Pi}=1]=1$。


##### 双密钥El-Gamal方案

 $(G,q,g)\leftarrow\mathcal{G}(1^n)$: $G=\langle g\rangle,|G|=q,w\leftarrow(\mathbb{Z}/q\mathbb{Z})^{\times},\hat{g}=g^w$.
 $(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n):x_1,x_2\leftarrow\mathbb{Z}/q\mathbb{Z},e=g^{x_1}\hat{g}^{x_2},\mathrm{pk}=(G,q,g,\hat{g},e),\mathrm{sk}=(x_1,x_2)$.

 $(a,\hat{a},c)\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m):u\leftarrow\mathbb{Z}/q\mathbb{Z},a=g^{u},\hat{a}=\hat{g}^u,c=me^u$

 $m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(a,\hat{a},c):m=c(a^{x_1}\hat{a}^{x_2})^{-1}$.

##### 若DDH假设成立，则双密钥El-Gamal方案是IND-CPA安全的。

假设El-Gamal不是IND-CPA的。

(0) $S:(g,g^x,g^y,g^z),G=\langle g\rangle,1\lt x,y,z\lt q$
(1) $S\to A:\mathrm{pk}=(g,\hat{g},e)$
(2) $A\to S:m_0,m_1$
(3) $S:b\leftarrow\{0,1\},a=g^y,\hat{a}=g^z,c=a^{x_1}\hat{a}^{x_2}m_b$
(4) $S\to A:c^{\ast}=(a,\hat{a},c)$
(5) $A\to S:b'$

由于假设中不是IND-CPA， $A$得到 $b'=b$在 $g^{z}=g^{xy}$的时候，否则 $A$不能在猜测 $b$的时候获得优势，即DDH易解。


##### Cramer-Shoup方案

 $(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n)$:
 $(G,q,g)\leftarrow\mathcal{G}(1^n)$; $w\leftarrow(\mathbb{Z}/q\mathbb{Z})^{\times},\hat{g}=g^w$;
 $x_1,x_2,y_1,y_2,z_1,z_2\leftarrow\mathbb{Z}/q\mathbb{Z}$, $e=g^{x_1}\hat{g}^{x_2},f=g^{y_1}\hat{g}^{y_2},h=g^{z_1}\hat{g}^{z_2}$;
 $\mathrm{pk}=(G,q,H,g,\hat{g},e,f,h),\mathrm{sk}=(x_1,x_2,y_1,y_2,z_1,z_2)$
其中 $H:G^3\to\mathbb{Z}/q\mathbb{Z}$为Hash函数，抗第二原象。

 $C=(a,\hat{a},c,d)\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m):$
 $u\leftarrow\mathbb{Z}/q\mathbb{Z},a=g^u,\hat{a}=\hat{g}^u,c=me^u,v=H(a,\hat{a},c),d=f^uh^{uv}$

 $m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(C):$
(1)检查是否 $a,\hat{a},c,d\in G$，否则直接输出 $\bot$.
(2)计算 $v=H(a,\hat{a},c)$，检查是否 $d=a^{y_1+vz_1}\hat{a}^{y_2+vz_2}$，否则输出 $\bot$.
(3) $m=c(a^{x_1}\hat{a}^{x_2})^{-1}$

##### CS的安全性

若DDH难解，且 $H$抗第二原象，则CS为IND-CCA安全的。如果 $\mathcal{A}_1,\mathcal{A}_2$代表 $G$的DDH攻击和$H$的第二原象攻击的敌手，则CS的优势具有关系

$$\mathrm{Adv}_{\mathcal{A},\mathrm{CS}}^{\mathrm{cca}}\leq 2\mathrm{Adv}_{\mathcal{A}_1,G}^{\mathrm{ddh}}+\mathrm{Adv}_{\mathcal{A}_2,H}^{\mathrm{tcr}}+\frac{4+Q}{q}$$

其中 $q$代表 $\mathcal{A}$询问解密Oracle的次数上限。证明略。 $\square$

##### Hashed El-Gamal方案

设已经有一个对称认证加密方案 $(E^s,D^s)$。

 $(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n):\mathrm{pk}=(G,q,g,h),\mathrm{sk}=x$.

 $(c_1,c_2)\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m):y\leftarrow\mathbb{Z}/q\mathbb{Z},c_1=g^y,k=H(c_1,h^y),c_2=E^s(k,m)$

 $m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(c_1,c_2):k'=H(c_1,c_1^x),m=D^s(k',c_2)$.

若HDH假设成立，则Hashed El-Gamal是IND-CPA安全的。如果SDH假设成立，则Hashed El-Gamal是IND-CCA安全的。

###### Twin El-Gamal方案

 $\mathrm{pk}=(h_1,h_2);\mathrm{sk}=(x_1,x_2),h_i=g^{x_i}$,

 $(c_1,c_2)\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m): y\in\mathbb{Z}/q\mathbb{Z},c_1=g^y,k=H(c_1,h_1^y,h_2^y),c_2=E^s(k,m)$

 $m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(c_1,c_2):k'=H(c_1,c_1^{x_1},c_1^{x_2}),m=D^s(k',c_2)$

在CDH假设和 $H$是“随机预言”的情况下，Twin El-Gamal方案是IND-CCA的。

### 因子分解为基础的算法

##### RSA问题

给定 $e,c$，寻找 $m$使得 $m^e\equiv c\mod N$，其中 $N=pq$， $p,q$为大素数

该问题易于 $N$的分解问题。

##### RSA方案

 $(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n):\mathrm{pk}=(N,e),\mathrm{sk}=(p,q,d),N=pq,\gcd(e,\phi[N])=1,ed\equiv 1\mod\phi[N]$

 $c\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m)=m^e\mod N$

 $m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(m)=c^d\mod N$


##### RSA的公共模和低私钥攻击

设两个用户公用一个RSA模，也就是使用密钥 $(N,e_1,d_1),(N,e_2,d_2),$，并且具有 $\gcd(e_1,e_2)=1$，则可以找到 $ve_1+we_2=1$，于是

$$c_1=m^{e_1},c_2=m^{e_2},m=c_1^{v}c_2^w=m^{ve_1+we_2}=m$$

如果多个用户使用使用同样的小私钥$e$并且具有大模数 $N_1,\dots N_l$，则可以列方程

$$m^e\equiv c_i\mod N_i,1\leq i\leq l$$

可以通过CRT得到 $m^e\equiv C\mod\mathrm{lcm}(N_1,\dots,N_l)$，由于 $e$很小，可以直接求根。

##### RSA的Wiener连分数攻击

若 $d\lt N^{1/4}/3$，则可以使用连分数获得 $d$。

任意实数都可以被化为一个简单连分数形式，即

$$\alpha=a_0+\cfrac{1}{a_1+\cfrac{1}{a_2+\cfrac{1}{a_3+\dots}}}=[a_0,a_1,a_2,a_3,\dots]$$

一个数是有理数当且仅当其为有限的简单连分数。我们设第 $j$个渐进分数为 $p_j/q_j=[a_0,a_1,\dots,a_j]$，于是我们可以得到渐进分数的递归规律,类似于Euclid算法：

$$\left[\begin{matrix}p_{i-1} & q_{i-1} \\\\ p_i & q_i\end{matrix}\right]=\left[\begin{matrix}0 & 1 \\\\ 1 & a_i\end{matrix}\right]\left[\begin{matrix}p_{i-2} & q_{i-2}\\\\ p_{i-1} & q_{i-1}\end{matrix}\right]$$

这一关系说明，如果 $|\alpha-p/q|\lt 1/2q^2$，则 $p/q$必然为一个 $\alpha$的渐进分数。

假设 $d\lt N^{1/4}/3$，设$ed=1+k\phi[N]$，于是

$$\left|\frac{e}{\phi[N]}-\frac{k}{d}\right|=\frac{1}{d\phi[N]}$$

考虑到 $|N-\phi[N]|\lt 3\sqrt{N}$，有

$$\left|\frac{e}{N}-\frac{k}{d}\right|\leq\frac{3k}{\sqrt{N}d}\lt\frac{1}{2d^2}$$

于是 $k/d$是 $e/N$的一个渐进分数，我们可以找到渐进分数的符合假设的分母，它就是要找到的 $d$。

##### 模平方根问题

给定 $z=x^2\mod N$，求 $x$。

若有 $N=pq$已知，则可以在 $\mathbb{Z}/q\mathbb{Z},\mathbb{Z}/p\mathbb{Z}$中求得 $z$平方根之后再CRT。而计算 $a\in(\mathbb{Z}/p\mathbb{Z})^{\times}$的平方根可以使用以下算法：

$$
\begin{array}{l}
\mathbf{Algorithm:}\text{Mod Squareroot } z \\
\hline
\textbf{Input: } z\in\mathrm{QR}(p) \\
\textbf{Output: } x,x^2\equiv z\mod p\\
\hline
1: \textbf{if } p\equiv 3\mod 4 \\
2: \quad \textbf{Return } a^{(p+1)/4}\mod p\\
3: \quad \textbf{else }\\
4: \quad \quad b\leftarrow (\mathbb{Z}/p\mathbb{Z})\backslash\mathrm{QR}(p),i\leftarrow(p-1)/2,j\leftarrow 0\\
5: \quad \quad \textbf{while } i\equiv 0\mod 2 \\
6: \quad \quad \quad i \leftarrow i/2,j\leftarrow j/2 \\
7: \quad \quad \quad \textbf{if } a^ib^j\equiv -1\mod p\\
8: \quad \quad \quad \quad j\leftarrow j+(p-1)/2\\
9: \quad \quad \textbf{Return } a^{(i+1)/2}b^{j/2}\mod p \\
\hline
\end{array}
$$
