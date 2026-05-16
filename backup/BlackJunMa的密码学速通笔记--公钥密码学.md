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
\mathbf{Algorithm:}\text{ModSqrt}(z,p) \\
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

若存在求模平方根的Oracle，则可以分解 $N=pq$。步骤如下：

(1) $x\leftarrow\mathbb{Z}/N\mathbb{Z};z\equiv x^2\mod N$
(2) $y\leftarrow\sqrt{z}\mod N$
(3) $y\neq\pm x\mod N\Rightarrow[\gcd(x+y,N)\gt 1]\vee[\gcd(x-y,N)\gt 1]$

也就是求模平方根问题和分解问题具有类似的复杂度。

##### Rabin加密方案

$(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n):\mathrm{pk}=N,\mathrm{sk}=(p,q),p,q\equiv 3\mod 4,N=pq$.

$c\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m):c\equiv m^2\mod N$

$m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(c):m^2\equiv c\mod N$

该方案的解密利用了求素数模平方根，我们需要从 $4$个不同的解出的明文中选择一个。

##### Rabin加密的OW-CPA安全性

假设Rabin加密不是OW-CPA安全的。

(1) $S:\mathrm{pk}=N,m\leftarrow(\mathbb{Z}/N\mathbb{Z})^{\times},c\equiv m^2\mod N$
(2) $S\to A:(\mathrm{pk},c)$
(3) $A\to S:m'$
(4) $S:\gcd(m'-m,N)=p,q=N/p$.

##### Hardcore Bit

设 $f:\\{0,1\\}^{\ast}\to\\{0,1\\}^{\ast}$是单向函数， $h:\\{0,1\\}^{\ast}\to\\{0,1\\}$是一个多项式可计算的函数，如果对任意PPT的 $\mathcal{A}$,有

$$\mathrm{Pr}[x\leftarrow\\{0,1\\}^n:\mathcal{A}(f(x))=h(x)]\leq\frac{1}{2}+\mathrm{negl}$$

则称 $h$为一个 $f$的Hardcore Bit。单项函数都可以构造一个hardcore bit，该构造称作Goldreich-Levin定理：定义 $g(x,r)=(f(x),r),|x|=|r|,x=x_1\dots x_k,r=r_1\dots r_k$,

$$B(x,r)=x_1r_1\oplus x_2r_2\oplus\dots\oplus x_kr_k$$

则 $B$是 $g$的一个hardcore bit。

##### 具有Hardcore的陷门函数族

我们使用 $\mathrm{lsb}(x)$表示最低有效bit位。

设 $N=pq,e$是一个RSA公钥，定义 $f:(\mathbb{Z}/N\mathbb{Z})^{\times}\to(\mathbb{Z}/N\mathbb{Z})^{\times},x\mapsto x^e\mod N$，则

$$\mathrm{Pr}[x\leftarrow(\mathbb{Z}/N\mathbb{Z})^{\times}:\mathcal{A}(f(x))=\mathrm{lsb}(x)]\leq \frac{1}{2}+\mathrm{negl}$$
即 $\mathrm{lsb}$是其hardcore。

此外，对于Blum整数 $N=pq,p,q\equiv 3\mod 4$，定义 $f:x\mapsto x^2\mod N$，则$\mathrm{lsb}$也是该函数的hardcore。

##### 基于陷门置换的加密方案

以下使用 $f$为陷门函数，给定索引 $I$则可以计算$f_I:\mathcal{D}_I\to\mathcal{D}_I$，但 $f_I^{-1}$需要得到陷门 $t$才能容易计算。 $h$为一个hardcore。

$(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n):\mathrm{pk}=I,\mathrm{sk}=t$

$(y,m')\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m),m\in\{0,1\}:x\leftarrow\mathcal{D}_I,y=f_I(x),m'=h_I(x)\oplus m$

$m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(y,m'): x=f^{-1}_I(y),m=h_I(x)\oplus m'$

该方案只能加密单个bit，但是可以使用并联方式来加密多个bit $m=m_1\dots m_l$：

$$x\leftarrow\mathcal{D}_I,x_{i+1}=f_I(x_i); c=(x_{l+1},h_I(x_1)\oplus m_I,\dots,h_I(x_l)\oplus m_l)$$

##### 基于陷门置换的加密方案具有IND-CPA安全性

令$S$为$\mathcal{A}_h$，即攻击hardcore $h$的敌手以及仿真者，$A$为攻击者；

(0) $S:I,y=f_I(x),\mathrm{pk}=I$
(1) $S\to A:\mathrm{pk}$
(2) $A\to S:m_0,m_1$
(3) $S:b,z\leftarrow\{0,1\},m'=z\oplus m_b$
(4) $S\to A:(y,m')$
(5) $A\to S:b';(b'=b)?z:\overline{z}$.

在该过程中，攻击 $h$的优势为

$$\mathrm{Pr}[\mathcal{A}_h(I,f_I(x))=h_I(x)]=\frac{1}{2}[\mathrm{Pr}[b'=b|z=h_I(x)]+\mathrm{Pr}[b'\neq b|z\neq h_I(x)]]$$

也就是如果 $b$被猜出的优势不可忽略，则 $h$的攻击优势不可忽略。

于是类似可以定义IND-CPA的RSA方案。

##### IND-CPA的RSA方案

$\mathrm{pk}=(N=pq,e),\mathrm{sk}=d$

$(c_1,c_2)\leftarrow\mathrm{Enc}_{\mathrm{pk}}(m),m\in\\{0,1\\}^{l(n)}:$
$r\leftarrow(\mathbb{Z}/N\mathbb{Z})^{\times},c_1\equiv r^e\mod N,c_2=H(r)\oplus m,H:(\mathbb{Z}/N\mathbb{Z})^{\times}\to\\{0,1\\}^{l(n)}$

$m\leftarrow\mathrm{Dec}_{\mathrm{sk}}(c_1,c_2):r\equiv c_1^d\mod N,m=H(r)\oplus c_2$

如果RSA问题困难且 $H$为随机谕言，则该方案是IND-CPA安全的。

##### IND-CCA的RSA方案

设 $(G^s,E^s,D^s)$为一个对称加密方案。

$(\mathrm{pk,sk})=(N=pq,e;d)$

$(c\_1,c\_2)\leftarrow\mathrm{Enc}\_{\mathrm{pk}}(m),m\in\\{0,1\\}^{l(n)}:$
$r\leftarrow(\mathbb{Z}/N\mathbb{Z})^{\times},c_1\equiv r^e\mod N,c_2=E^s_{H(r)}(m),H:(\mathbb{Z}/N\mathbb{Z})^{\times}\to\\{0,1\\}^n$

$m\leftarrow\mathrm{Dec}\_{\mathrm{sk}}(c\_1,c\_2):r\equiv c\_1^d\mod N,m=D^s\_{H(r)}(c_2)$

若RSA问题困难且 $H$为随机谕言，$(G^s,E^s,D^s)$为IND-CCA的，则上述方案为IND-CCA的。

##### 密钥封装(Key Encapsulation Mechanism,KEM)

一个KEM由以下部分组成：

$(\mathrm{pk,sk})\leftarrow\mathrm{KEM.Gen}(1^n)$

$(c,k)\leftarrow\mathrm{Encap}(\mathrm{pk})$

$k\leftarrow\mathrm{Decap}(\mathrm{sk},c)$

即在生成私钥和公钥之后，使用 $\mathrm{Encap}$将公钥转化为文本 $c$和密钥 $k$，并且 $\mathrm{Decap}$可以通过私钥和 $c$复原出 $k$来。

###### KEM-DEM混合加密

设$\mathrm{DEM}=(G^s,E^s,D^s)$为一个对称加密方案， $(\mathrm{KEM.Gen,Encap,Decap})$为KEM，则可以定义以下的KEM-DEM混合加密方案

$(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n): (\mathrm{pk,sk})=\mathrm{KEM.Gen}(1^n)$

$(c\_1,c\_2)\leftarrow\mathrm{Enc}\_{\mathrm{pk}}(m):\mathrm{Encap}(\mathrm{pk})=(c\_1,k),c_2=E^s\_{k}(m)$

$m\leftarrow\mathrm{Dec}\_{\mathrm{sk}}(c\_1,c\_2):k=\mathrm{Decap}(\mathrm{sk},c\_1),m=D^s_k(c\_2)$

##### 使用OW-CPA构造IND-CCA的KEM

设$(\mathrm{Gen,Enc,Dec})$为一个OW-CPA的确定性公钥加密，$H$为Hash函数，$G$为密钥派生函数。

$(\mathrm{pk,sk})\leftarrow\mathrm{KEM.Gen}(1^n):(\mathrm{pk,sk})=\mathrm{Gen}(1^n)$

$(c,k)\leftarrow\mathrm{Encap}(\mathrm{pk}):r\leftarrow M,(c_1,c_2)=(\mathrm{Enc}_{\mathrm{pk}}(r),H(r)),k=G(r)$

$k\leftarrow\mathrm{Decap}(c_1,c_2):r=\mathrm{Dec}\_{\mathrm{sk}}(c_1),[(\mathrm{Enc}_{pk}(r)=c\_1)\wedge c\_2=H(r)]?G(r):\bot$

##### Rabin-KEM

 $(\mathrm{pk,sk})\leftarrow\mathrm{KEM.Gen}(1^n):\mathrm{pk}=N=pq,\mathrm{sk}=(p,q)$.

 $(c,k)\leftarrow\mathrm{Encap}(\mathrm{pk}):r\leftarrow\mathbb{Z}/N\mathbb{Z},c=(r^2\mod N,H(r)),k=G(r)$

 $k\leftarrow\mathrm{Decap}(c=(c_1,c_2))$:
若 $c_1\notin(\mathbb{Z}/N\mathbb{Z})^{\times 2}$，输出 $\bot$。否则，计算 $r_1,r_2,r_3,r_4$作为 $c_1\mod N$的平方根，若 $H(r_i)\neq c_2$，则输出 $\bot$;若存在 $i,j$使得 $H(r_i)=H(r_j)=c_2$则输出 $\bot$;若对唯一的 $r_i$有 $H(r_i)=c_2$，则输出  $k=G(r_i)$。

### CCA设计

##### 非交互零知识(Non Interactive Zero Knowledge,NIZK)解决思路

NIZK是一种特殊的密码学协议。它允许证明者不透露任何秘密信息前提下向验证者证明某陈述是正确的，且整个过程不需要双方来回对话。

假设 $A,B$对应发送方和接收方，在传统的公钥密码方案中，一般都是：

$$A\to B:\mathrm{Enc}_{\mathrm{pk}}(m)$$

但为了应对CCA攻击，我们应当发送一个二元组，也就是：

$$A\to B:(c,p),c=\mathrm{Enc}_{\mathrm{pk}}(m)$$
而 $p$代表一个NIZK证明，陈述为：“我知道该密文对应 $m$”

这个方法还有一个扩展：Naor-Yung范式，它基于两个由 $B$生成的公钥 $\mathrm{pk_1,pk_2}$，并运行

$$A\to B:(c\_1,c\_2,p); c\_1=\mathrm{Enc}\_{\mathrm{pk}\_1}(m),c_2=\mathrm{Enc}\_{\mathrm{pk}\_2}(m)$$
而 $p$代表NIZK陈述：“$c_1,c_2$对应明文相等”。

##### NY-90

设 $(\mathrm{Gen,Enc,Dec})$为一个公钥方案。

Setup:

(1) $(e_1,d_1)\leftarrow \mathrm{Gen}(1^n);(e_2,d_2)\leftarrow \mathrm{Gen}(1^n)$ (生成两对公私密钥)
(2) $R\leftarrow U(n)$ ( $U(n)$为均匀分布，$R$为取得的字符串)
(3) $L$为一个语言，包含对 $(c_1,c_2)$，其中 $c_1,c_2$是同一个明文对应的密文。
(4) $\mathrm{pk}=(e_1,e_2,R);\mathrm{sk}=(d_1,d_2)$

Encrypt:

(1) $(r_1,r_2)\leftarrow\\{0,1\\}^{l(n)}$, $l(n)$为一个多项式
(2) $c\_1\leftarrow\mathrm{Enc}\_{e\_1}(b,r\_1),c_2\leftarrow\mathrm{Enc}\_{e_2}(b,r\_2)$, 其中 $b$为明文，。
(3) $p\leftarrow P((e\_1,e\_2,(c\_1,c\_2)),(r\_1,r\_2),R)$为NIZK:" $c\_1,c\_2$为同明文对应的密文"
(4) $c\leftarrow (c\_1,c\_2,p)$

Decrypt:

(1) 使用NIZK验证 $V$在验证 $p$:运行 $V(c,p,R)$
(2) 如果 $V$接受，则直接解密 $b=\mathrm{Dec}_{d_1}(c_1)$

可以证明，若原本的公钥方案是IND-CPA，则NY-90为IND-CCA1。

##### (DDN)ABO+NIZK+One-Time Signature

设 $(G,E,D)$为一个已有的方案, $GS$输出一个签名。

Setup:

(1) $(e_i^b,d_i^b)\leftarrow G(n),i=1,\dots,n;b=0,1$
(2) $R\leftarrow U(n),\mathrm{pk}\leftarrow(e_i^{b},R),\mathrm{sk}\leftarrow d_i^b$

Encrypt:

(1) $(\mathrm{Sig,Ver})\leftarrow GS(n)$
(2) $v=v_1,\dots,v_n\leftarrow h(\mathrm{Ver}),r_i\leftarrow\{0,1\}^{l(n)}$
(3) $c_i\leftarrow E_{e_i^{v_i}}(m,r_i)$
(4) $p=P((e_i^{v_i},c_i);r_i,R)$
(5) $s=\mathrm{Sig}(c_i,p),c\leftarrow(\mathrm{Ver},s,c_i,p)$ 

Decrypt:

(1) $s=?\mathrm{Ver}(c_i,p)$
(2) $v=v_1,\dots,v_n\leftarrow h(\mathrm{Ver}),V(c_i,p)?$
(3) $m\leftarrow D_{d_i^{v_i}}(c)$

该方案是CCA2的。

##### 基于Hash Proof System构造CCA的PKE

设 $G$为一个素数 $q$阶群。 $H$为Hash。

Setup:

(1) $g\leftarrow G,(z_1,z_2)\leftarrow(\mathbb{Z}/q\mathbb{Z})^{\times}$
(2) $h_1\leftarrow g^{z_1},h_2\leftarrow g^{z_2},(x_1,x_2,x_3,y_1,y_2,y_3)\leftarrow(\mathbb{Z}/q\mathbb{Z})^{\times}$
(3) $c_1\leftarrow g^{x_1}h_1^{x_3},c_2\leftarrow g^{x_2}h_2^{-x_3},d_1\leftarrow g^{y_1}h_1^{y_3},d_2\leftarrow g^{y_2}h^{-y_3}$
(4) $\mathrm{pk}\leftarrow(h_1,h_2,c_1,c_2,d_1,d_2);\mathrm{sk}\leftarrow(z_1,z_2,x_1,x_2,x_3,y_1,y_2,y_3)$

Encrypt:

(1) $(w_1,w_2)\leftarrow(\mathbb{Z}/q\mathbb{Z})^{\times}$
(2) $(u_1,u_2,e_1,e_2)\leftarrow(g^{w_1},g^{w_2},mh_1^{w_1},mh_2^{w_2})$
(3) $(C_1,C_2)\leftarrow((u_1,e_1),(u_2,e_2))$
(4) $t\leftarrow H(C_1,C_2);v\leftarrow c_1^{w_1}d_1^{w_1t}c_2^{w_2}d_2^{w_2t}$
(5) $C\leftarrow (C_1,C_2,v)$

Decrypt:

(1) $t\leftarrow H(C_1,C_2),f\leftarrow e_1/e_2$
(2) $v=? u_1^{x_1+ty_1}u_2^{x_2+ty_2}f^{x_3+ty_3}$
(3) $m\leftarrow e_1/u_1^{z_1}$

以上方案可以简化，简化的方案在下方，均可以得到CCA安全性。

##### CS98

##### Hybrid CS98 KEM+DEM

##### KD04


### 单向函数签名方案

##### 存在性不可伪造选择信息攻击安全性(Existentially Unforgeable under Chosen-Message Attack,EUF-CMA)

设 $(\mathrm{Vrfy,Sign})$为一个签名方案，以下为CMA攻击的内容, $A,C$分别为挑战者和攻击者：

(1) $C:(\mathrm{pk,sk})\leftarrow\mathrm{Gen}(1^n)$
(2) $C\to A:\mathrm{pk}$
(3) $A\to C:m_i$
(4) $C:\mathcal{Q}=\emptyset,\sigma_i\leftarrow\mathrm{Sign}_{\mathrm{sk}}(m_i)$
(5) $C\to A:\sigma_i;\mathcal{Q}\leftarrow\mathcal{Q}\cup\{m_i\}$
(6) $A\to C:(m,\sigma)$

如果

$$\mathrm{Pr}[\mathrm{Vrfy}_{\mathrm{pk}}(m,\sigma)=1,m\notin\mathcal{Q}]<\mathrm{negl}$$

称该方案是EUF-CMA安全的。

若 $\mathcal{Q}$一开始不是空集而是多个签名对 $\{(m_1,\sigma_1),\dots\}$，则称为强不可伪造选择信息攻击(Strongly Unforgeable under Chosen-Message Attack,SUF-CMA)。若 $A$访问 $C$的签名应答器仅仅一次，则称之为 EUF-OTCMA，其中OT是One Time。

##### Lamport一次签名

设 $f:\\{0,1\\}^n\to\\{0,1\\}^n$为单向函数。

(1) $\mathrm{sk}=(x_{1,0},x_{1,1},x_{2,0},x_{2,1},\dots,x_{l,0},x_{l,1})$
(2) $\mathrm{pk}=(y_{1,0},\dots,y_{l,1}),y_{i,j}=f(x_{i,j})$
(3) $m=m_1m_2\dots m_l\in\\{0,1\\}^l$
(4) $\sigma\leftarrow\mathrm{Sign}\_{\mathrm{sk}}(m):\sigma=(x\_{1,m\_1},x\_{2,m\_2},\dots,x\_{l,m\_l})$
(5) $\mathrm{Vrfy}\_{\mathrm{pk}}(m\_1\dots m\_l;\sigma=(x\_1,\dots,x\_l))=1\Leftrightarrow f(x\_i)=y_{i,m\_i},\forall 1\leq i\leq l$

Lamport一次签名的公钥和签名长，除此之外可以通过对比bit的方式得到 $\mathrm{sk}$的部分，例如：得知 $\mathrm{Sign}(000)=(x_{1,0},x_{2,0},x_{3,0});\mathrm{Sign}(111)=(x_{1,1},x_{2,1},x_{3,1})$。

$f$的单向性可以证明 Lamport一次签名EUF-OTCMA安全。 $f$是单向置换推出SUF-OTCMA安全。

##### Hash-and-Sign

设含参Hash函数 $H_s$,并且存在一个已有的签名方案 $(\mathrm{Gen,Sign,Vrfy})$。定义其Hash-and-Sign方案

$\mathrm{pk}'=(s,\mathrm{pk}),\mathrm{sk}'=(s,\mathrm{sk})$;
$\sigma'\leftarrow\mathrm{Sign}'\_{\mathrm{sk}'}(m)=\mathrm{Sign}\_{\mathrm{sk}}(H_s(m))$
$\mathrm{Vrfy}'\_{\mathrm{pk}'}(m,\sigma')=1\Leftrightarrow\mathrm{Vrfy}\_{\mathrm{pk}}(H\_s(m),\sigma')=1$

已有签名方案EUF-CMA且 $H$抗碰撞则Hash-and-Sign是EUF-CMA的。

##### 一次签名框架

设 $F$为PRF，$\mathcal{K}$为私钥空间。

Setup:

$\mathrm{sk}\leftarrow\mathcal{K}$
$x_i\leftarrow F(\mathrm{sk},i);y_i=f(x_i),\mathrm{pk}=(y_1,\dots,y_n)$

Sign:

$P(m)=\{s_1,\dots,s_l\}\subset\{1,\dots,n\}$
$x_{s_i}\leftarrow F(\mathrm{sk},i)$
$\sigma=(x_{s_1},\dots,x_{s_l})$

Verify:

$\{s_1,\dots,x_l\}=P(m),\sigma=(x_1,\dots,x_l)$
$\mathrm{Vrfy}(m,\sigma)=1\Leftrightarrow y_{s_i}=f(x_i),\forall i$

若 $P(m)\not\subset P(m'),\forall m\neq m'$，而且 $f$为单向函数，则该方案EUF-OTCMA。

##### Winternitz一次签名

我们使用 $f^{[n]}(x)$表示 $f$的自我复合，即 $f^{[1]}(x)=f(x),f^{[n]}(x)=f(f^{[n-1]}(x))$。 $G$为一个PRG, $H$为Hash。

Setup:

$\mathrm{sk}\leftarrow\mathcal{K}$
$(x_1,\dots,x_n)\leftarrow G(\mathrm{sk}),y_i=f^{[d]}(x_i)$
$\mathrm{pk}=H(y_1,\dots,y_n)$

Sign:

$(x_1,\dots,x_n)\leftarrow G(\mathrm{sk})$
$P(m)=(s_1,\dots,s_n)\in\{0,1,\dots,d\}^n$
$\sigma=(f^{[s_1]}(x_1),\dots,f^{[s_n]}(x_n))$

Verify:

$(s_1,\dots,s_n)=P(m),\sigma=(z_1,\dots,z_n)$
$\mathrm{Vrfy}_{\mathrm{pk}}(m,\sigma)=1\Leftrightarrow \mathrm{pk}=H(f^{[d-s_1]}(z_1),\dots,f^{[d-s_n]}(z_n))$

定义 $P(m')\geq P(m)\Leftrightarrow s_i'\geq s_i,\forall i$，若 $f$的任意 $d$次或者更少的自复合是单向的， $H$抗碰撞， $P(m)\not\geq P(m'),\forall m'\neq m$，则该签名时EUF-OTCMA的。

##### 状态签名

有时候签名需要加入状态。

$(\mathrm{pk,sk},s_0)\leftarrow\mathrm{Gen}(1^n)$
$(\sigma,s_i)\leftarrow\mathrm{Sign}\_{\mathrm{sk},s\_{i-1}}(m)$

$\mathrm{Vrfy}\_{\mathrm{pk}}(m,\sigma)=1\Leftrightarrow (\sigma\_i,s\_i)\leftarrow\mathrm{Sign}\_{\mathrm{sk},s\_{i-1}}(m\_i),1\leq i\leq l$

##### 链式签名

设存在一个一次签名方案 $\Pi_{\mathrm{OT}}=(\mathrm{Gen,Sign,Vrfy})$，则可以定义一个链式签名过程：

(1) $(\mathrm{pk}\_1,\mathrm{sk}\_1)\leftarrow\mathrm{Gen}(1^n)$
(2) $(\mathrm{pk}\_2,\mathrm{sk}\_2)\leftarrow\mathrm{Gen}(1^n),\sigma\_1\leftarrow\mathrm{Sign}\_{\mathrm{sk}\_1}(m\_1||\mathrm{pk}\_2),s\_1=(m\_1,\mathrm{pk}\_2,\mathrm{sk}\_2,\sigma\_1)$
(3) $\dots$
(4) $(\mathrm{pk}\_{i+1},\mathrm{sk}\_{i+1})\leftarrow\mathrm{Gen}(1^n),\sigma\_i\leftarrow\mathrm{Sign}\_{\mathrm{sk}\_i}(m_i||\mathrm{pk}\_{i+1}),s\_i=\{(m\_j,\mathrm{pk}\_{j+1},\mathrm{sk}\_{j+1},\sigma\_j)|1\leq j\leq i\}$

每一个 $m_T$的签名以 $(\sigma_T,\mathrm{pk}\_{T+1},\{(m_j,\mathrm{pk}\_{j+1},\sigma_j)|1\leq j\leq T-1\})$的形式送出。

##### 树式签名

设 $(\mathrm{Gen,Sign,Vrfy})$为一次签名， $m=m_1m_2\dots\in\{0,1\}^{\ast},m|_i=m_1\dots m_i$。构造树式签名 $(\mathrm{Gen}^{\ast},\mathrm{Sign}^{\ast},\mathrm{Vrfy}^{\ast})$如下：

Setup:

$(\mathrm{pk}\_{\emptyset},\mathrm{sk}\_{\emptyset})\leftarrow\mathrm{Gen}^{\ast}(1^n):(\mathrm{pk}\_{\emptyset},\mathrm{sk}\_{\emptyset})\leftarrow\mathrm{Gen}(1^n),s=[\mathrm{sk}\_{\emptyset}]$

Sign:

$(\{\sigma_{m|\_i},\mathrm{pk}\_{m|\_i0},\mathrm{pk}\_{m|\_i1}|0\leq i\leq n-1\},\sigma_m)\leftarrow\mathrm{Sign}^{\ast}\_{\mathrm{sk}\_{\emptyset},s}(m),m\in\{0,1\}^n$:
(1) 对 $i\leq n-1$，若 $\sigma_{m|\_i},\mathrm{pk}\_{m|\_i0},\mathrm{pk}\_{m|\_i1}$不在 $s$中，则有
$(\mathrm{pk}\_{m|\_{i}0},\mathrm{sk}\_{m|\_i1}),(\mathrm{pk}\_{m|\_i1},\mathrm{sk}\_{m|\_i1})\leftarrow\mathrm{Gen}(1^n);$
$\sigma\_{m|\_i}\leftarrow\mathrm{Sign}\_{\mathrm{sk}\_{m|\_i}}(\mathrm{pk}\_{m|\_i0}||\mathrm{pk}\_{m|\_i1})$
$s=[\{\sigma\_{m|\_i},\mathrm{pk}\_{m|\_i0},\mathrm{sk}\_{m|\_i0},\mathrm{pk}\_{m|\_i1},\mathrm{sk}\_{m|\_i1}\}]$
(2) $\sigma\_m\leftarrow\mathrm{Sign}\_{\mathrm{sk}\_m}(m)$

Verify:

$\mathrm{Vrfy}^{\ast}\_{\mathrm{pk}\_{\emptyset}}(m,(\{\sigma_{m|\_i},\mathrm{pk}\_{m|\_i0},\mathrm{pk}\_{m|\_i1}|i\leq n-1\},\sigma\_m))=1$当且仅当：
$\mathrm{Vrfy}\_{\mathrm{pk}\_{m|\_i}}(\mathrm{pk}\_{m|\_i0}||\mathrm{pk}\_{m|\_i1},\sigma_{m|\_i})=1,0\leq i\leq n-1;\mathrm{Vrfy}\_{\mathrm{pk}\_m}(m,\sigma\_m)=1$

若其的基础的一次签名EUF-OTCMA，则树签名为EUF-CMA。

