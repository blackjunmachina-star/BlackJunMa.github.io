### 攻击场景

目前根据严重程度可以对密码学攻击分为以下4个类别：

##### 唯密文攻击(Ciphertext-Only Attack):

最基本的攻击方式，敌手只能观察到密文并由此确认明文。

##### 已知明文攻击(Known Plaintext Attack)

敌手可以知道一个或者多个明文-密文对，并由此确认其他密文对应的明文。

##### 选择明文攻击(Chosen Plaintext Attack,CPA)

敌手可以选择明文并且获得相对应的密文，并试图确定其他密文的对应明文。

##### 选择密文攻击(Chosen Ciphertext Attack,CCA)

敌手可以选择部分密文并获得其对应明文，试图确认其他密文对应明文。

### 那些特殊函数：

##### 敌手窃听不可区分实验(Adversarial Indistinguishability Experiment) $\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{eav}}(n)$

(1)敌手 $\mathcal{A}$输出一对信息 $m_0,m_1\in\mathcal{M}$
(2)由 $\mathrm{Gen}(1^n)$产生密钥 $k$，并且选取 $b\leftarrow\{0,1\}$，敌手访问Oracle，可以获得 $c\leftarrow\mathrm{Enc}\_k(m\_b)$
(3): $\mathcal{A}$输出一个 $b'\in\{0,1\}$，如果 $b=b'$则称 $\mathrm{PrivK}\_{\mathcal{A},\Pi}^{\mathrm{eav}}=1$，否则为 $0$。

##### 多消息窃听实验(Multiple Message Eavesdropping Experiment) $\mathrm{PrivK}\_{\mathcal{A},\Pi}^{\mathrm{mult}}(n)$

(1)敌手 $\mathcal{A}$被输入 $1^n$，输出向量 $(m\_0^1,\dots,m\_0^t),(m\_1^1,\dots,m\_1^t)$，并且对应的 $m\_b^i$长度相等。

(2) $k\leftarrow\mathrm{Gen}(1^n),b\leftarrow\{0,1\}$，对于所有 $i$计算 $c^i\leftarrow\mathrm{Enc}\_k(m\_b^i)$

(3) $\mathcal{A}$接受 $(c^1,\dots,c^t)$，并输出 $b'$，若 $b'=b$则实验输出 $1$

##### CPA不可区分实验(CPA Indistinguishability Experiment) $\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{cpa}}(n)$

(1) $k\leftarrow\mathrm{Gen}(1^n),\mathcal{A}$在获得 $1^n$时可以访问Oracle $\mathrm{Enc}_k(\sim)$，输出一对 $m_0,m_1$

(2) $b\leftarrow\{0,1\},c\leftarrow\mathrm{Enc}_k(m_b)$提交给 $\mathcal{A}$

(3) $\mathcal{A}$可以继续访问Oracle $\mathrm{Enc}_k(\sim)$,输出 $b'$，若 $b=b'$，则输出 $1$。

##### CCA不可区分实验 $\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{cca}}(n)$

(1) $k\leftarrow\mathrm{Gen}(1^n)$, $\mathcal{A}(1^n)$可以调用Oracle $\mathrm{Enc}_k,\mathrm{Dec}_k$，输出一对 $m_0,m_1$。

(2) $b\leftarrow\{0,1\},c\leftarrow\mathrm{Enc}_k(m_b)$返回给 $\mathcal{A}$

(3) $\mathcal{A}$可以继续使用以上的Oracle，但不可以使用 $c$访问 $\mathrm{Dec}_k$，若猜中 $b$则实验输出 $1$

##### 消息鉴别码实验(Message Authentication Experiment) $\mathrm{Macforge}_{\mathcal{A},\Pi}(n)$

(1) $k\leftarrow\mathrm{Gen}(1^n)$, $\mathcal{A}(1^n)$可以调用Oracle $\mathrm{Mac}_k(\sim)$，输出一对 $m,t$

(2) $\mathcal{Q}$表示 $\mathcal{A}$使用Oracle的询问集合，实验输出 $1$当且仅当 $\mathrm{Vrfy}_k(m,t)=1,m\notin\mathcal{Q}$。

##### Hash函数碰撞发现实验(Collision-Finding Experiment) $\mathrm{Hashcoll}_{\mathcal{A},H}(n)$

(1)运行 $\mathrm{Gen}(1^n)$获取 $s$, $\mathcal{A}$输入 $s$输出 $x,x'$。

(2)当且仅当 $x\neq x',H^s(x)=H^s(x')$，输出为 $1$。

### 完善保密加密：

##### 完善保密(Perfectly Secret)的定义

一个方案 $(\mathrm{Gen,Enc,Dec})$是完善保密的，如果对于任意明文空间 $\mathcal{M}$的分布和$m\in\mathcal{M}, c\in\mathcal{C}, \mathrm{Pr}[C]>0$

$$
\mathrm{Pr}[M=m\|C=c]=\mathrm{Pr}[M=m]
$$

##### 完善保密的等价定义

可以把上式换成

$$
\mathrm{Pr}[C=c\|M=m]=\mathrm{Pr}[C=c]
$$

等价性可用Bayes定理证明。

初次之外，可以使用完美不可区分性(Perfect Indistinguishability)表示完善保密性：方案是完善保密的，当且仅当 $\forall m_0,m_1\in\mathcal{M}$，有

$$
\mathrm{Pr}[C=c|M=m_0]=\mathrm{Pr}[C=c|M=m_1]
$$

敌手不可取分性(Adversarial Indistinguishability)定义：完善保密当且仅当

$$
\mathrm{Pr}[\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{eav}}=1]=1/2
$$

##### 完善保密的性质与Shannon定理

完善保密必然是 $|\mathcal{K}|\geq|\mathcal{M}|$的，也就是密钥空间的基数大于明文空间的基数。

如果 $|\mathcal{K}|=|\mathcal{M}|=|\mathcal{C}|$，则当且仅当下列条件成立时方案完善保密：(1)任意密钥 $k\in\mathcal{K}$都是 $1/|\mathcal{K}|$概率。(2)对任意明文 $m\in\mathcal{M}$和密文 $c\in\mathcal{C}$，存在唯一的 $k\in\mathcal{K}$ 使得 $\mathrm{Enc}_k(m)=c$。

### PKE

##### 对称密钥加密/私钥加密(Private Key Encryption,PKE)

一个PKE加密方案 $(\mathrm{Gen,Enc,Dec})$均是多项式时间算法：

(1) $\mathrm{Gen}(1^n)$输出 $k$，WLOG，我们令 $|k|\geq n$。

(2) $\mathrm{Enc}_k(m)=c\Leftrightarrow\mathrm{Dec}_k(c)=m,m\in\{0,1\}^{\ast}$

如果该方案满足对 $k\leftarrow\mathrm{Gen}(1^n)$，算法 $\mathrm{Enc}_k(\sim)$只对 $\{0,1\}^{l(n)}$有定义，则称该方案消息长度为定长 $l(n)$。

##### 窃听者存在情况下的不可区分性(Indistinguishability in the Presence of an Eavesdropper,EAV-Secure)

对任意多PPT敌手 $\mathcal{A}$，有

$$
\mathrm{Pr}[\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{eav}}(n)=1]\leq 1/2+\mathrm{negl}(n)
$$

则称该方案具备窃听者存在情况下的不可区分性。这一定义又称作语义安全。该性质存在一个等价表述：如果我们使用 $\mathrm{OutputPrivK}_{\mathcal{A},\Pi}^{\mathrm{eav}}(n,b)$表示 $\mathcal{A}$给出的 $b'$，则窃听者存在下的不可区分可表示为

$$
|\mathrm{Pr}[\mathrm{OutputPrivK}_{\mathcal{A},\Pi}^{\mathrm{eav}}(n,0)=1]-\mathrm{Pr}[\mathrm{OutputPrivK}_{\mathcal{A},\Pi}^{\mathrm{eav}}(n,1)=1]|\leq\mathrm{negl}(n)
$$

##### EAV-Secure的性质

设 $m[i]$表示 $m$的第 $i$号bit，若 $i$索引不存在则为 $0$。若PKE方案在EAV-Secure，则对任意PPT敌手 $\mathcal{A}$都是(我们使用 $\mathcal{A}(\sim)$表示 $\mathcal{A}$基于输入 $\sim$的攻击结果)

$$
\mathrm{Pr}[\mathcal{A}(1^n,\mathrm{Enc}_k(m))=m[i]]\leq 1/2+\mathrm{negl}(n)
$$

EAV-Secure的方案对任意PPT  $\mathcal{A}$，存在另一个PPT $\mathcal{A}'$使得对任意多项式的可计算函数 $f$，有效可采样集合 $S$存在

$$
|\mathrm{Pr}[\mathcal{A}(1^n,\mathrm{Enc}_k(m))=f(m)]-\mathrm{Pr}[\mathcal{A}'(1^n)=f(m)]|\leq\mathrm{negl}(n)
$$

其中 $m$从 $S\cap\{0,1\}^n$中均匀选取。

##### 窃听者存在下语义安全(Semantically Secure in the Presence of an Eavesdropper,EAV-Semantically Secure)

一个方案是EAV-语义安全的，如果对任意PPT算法 $\mathcal{A}$，存在PPT算法 $\mathcal{A}'$，对任意有效可采样分布 $X_1,\dots$以及所有多项式可计算 $f,h$，有

$$
|\mathrm{Pr}[\mathcal{A}(1^n,\mathrm{Enc}_k(m),h(m))=f(m)]-\mathrm{Pr}[\mathcal{A}'(1^n,h(m))=f(m)]|\leq\mathrm{negl}(n)
$$

其中 $m$根据分布 $X_n$选取。一个PKE方案是EAV-Secure，当且仅当其EAV-语义安全。

##### 伪随机发生器(Pseudorandom Generator,PRG)

$G: \\{0,1 \\}^n\to \\{0,1 \\}^{l(n)}$为一个算法(其中 $l(n)\lt n$为一个多项式)，如果对任何PPT的区分器$D$均有

$$
|\mathrm{Pr}[D(r)=1]-\mathrm{Pr}[D(G(s))=1]|\leq\mathrm{negl}(n)
$$

则称 $G$是一个PRG，其中 $s$从 $\\{0,1 \\}^n$中， $r$从 $\\{0,1\\}^{l(n)}$均匀随机选取。

如果 $G$是一个PRG，则以下方案是EAV-Secure的：

$\mathrm{Gen}$:输入 $1^n$，均匀选择 $k\leftarrow\\{0,1\\}^n$; $\mathrm{Enc}_k(m)=G(k)\oplus m$; $\mathrm{Dec}_k(c)=G(k)\oplus c$

一个确定多项式时间算法 $G$是变长PRG， $l\lt l'$，如果：
(1)令 $s$是字符串，则 $G(s,1^l)$输出长度为 $l$

(2) $G(s, 1^l)$是 $G(s, 1^{l'})$的前缀。

(3)定义 $G_l(s)=G(s,1^{l(|s|)})$

称扩展因子为 $l$。

##### 伪随机函数(Pseudorandom Function,PRF)

设 $F_k(x):\{0,1\}^{\ast}\times\{0,1\}^{\ast}\to\{0,1\}^{\ast}$具有保留长度性质 $|F_k(x)|=|x|=|k|$，如果存在多项式时间区分器 $D$，有

$$
|\mathrm{Pr}[D^{F_k(\sim)}(1^n)=1]-\mathrm{Pr}[D^{f(\sim)}(1^n)=1]|\leq\mathrm{negl}
$$

则称 $F$是PRF。其中 $k\leftarrow\{0,1\}^n$随机选择，$D^{f}$代表$D$可以调用函数 $f$的Oracle， $f:\{0,1\}^n\to\{0,1\}^n$随机选取。

如果对于每一个 $k$， $F_k(\sim)$是单射，并且逆 $F_k^{-1}$也是多项式时间的，则称为伪随机(Pseudorandom Permutation,PRP)。若将上式改为

$$
|\mathrm{Pr}[D^{F_k(\sim),F_k^{-1}(\sim)}(1^n)=1]-\mathrm{Pr}[D^{f(\sim),f^{-1}(\sim)}(1^n)=1]|\leq\mathrm{negl}
$$

则称 $F$为强PRP。

##### 多加密的安全性(Security for Multiple Encryption)

一个方案是窃听者存在下不可区分多次加密的，如果

$$
\mathrm{Pr}[\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{mult}}(n)]\leq 1/2+\mathrm{negl}(n)
$$

存在方案，是EAV-Secure的，但是不是EAV-Multi Secure的。事实上，若 $\mathrm{Enc}$是一个密钥和消息的确定的函数，则方案无论如何不具备EAV-Multi Secure。所以，可以使用流密码解决这个问题。

##### CPA/CCA Security

一个方案是CPA-Secure的，如果

$$
\mathrm{Pr}[\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{cpa}}(n)=1]\leq 1/2+\mathrm{negl}
$$

类似可以定义CPA加密安全。类似可以使用 $\mathrm{PrivK}_{\mathcal{A},\Pi}^{\mathrm{cca}}(n)$定义CCA-Secure。

##### 基于PRF的CPA-Secure加密

构造一个方案：令 $F$为PRF， $\mathrm{Gen}(1^n):k\leftarrow\{0,1\}^n$均匀选取；

$r\leftarrow\\{0,1\\},\mathrm{Enc}_k(m)=(r,F_k(r)\oplus m)$; $\mathrm{Dec}_k(r,s)=F_k(r)\oplus s$

可以证明，若 $F$符合PRF的定义，则该构造方案作为消息长度为 $n$的定长PKE方案，是CPA-Secure。

##### 密码分组链接(Cipher Blowk Chaining)

该方案需要先选取一个长度为 $n$的初始值 $c_0$，最后密文包含 $c_0$。

$$
c_i=F_k(c_{i-1}\oplus m_i),1\leq i\leq l
$$

$$
m_i=F^{-1}_k(c_i)\oplus c_{i-1},1\leq i\leq l
$$

若 $F$为PRP，可以证明CBC是CPA-Secure。该方案不可并行处理。

##### 输出反馈模式(Output Feedback,OFB)

该方案需要先选取长度$n$的初值 $v_0$，并且定义 $v_i=F_k(v_{i-1})$，

$$
c_i=m_i\oplus v_i,m_i=c_i\oplus v_i,1\leq i\leq l
$$

相比于CBC,该方案不需要 $F$可逆，如果可以预先计算 $v_i$，则该方案可以并行处理。如果 $F$是PRF，则该方案是CPA-Secure。

##### 计数器模式(CTR)

先选取一个随机的 $v_0$，并且直接使用

$$
c_i=m_i\oplus F_k(v_0+i),m_i=c_i\oplus F_k(v_0+i),1\leq i\leq l
$$

该解密不要求 $F$可逆。若 $F$为PRF，则其为CPA-Secure。可以并行处理。

### 消息鉴别码(Message Authentication Code,MAC)

##### MAC

MAC是一个PPT算法三元组 $(\mathrm{Gen,Mac,Vrfy})$，满足： $\mathrm{Gen}$输入$1^n$，输出 $k,|k|\geq n$; $\mathrm{Mac}$输入 $k,m\in\\{0,1\\}^{\ast}$，输出 $t$； $\mathrm{Vrfy}$输入 $k,m,t$，输出 $b$； $b=1$当且仅当 $t=\mathrm{Mac}_k(m)$，否则为 $0$。

一个MAC如果对任意多项式的 $\mathcal{A}$存在

$$
\mathrm{Pr}[\mathrm{Macforge}_{\mathcal{A},\Pi}(n)=1]\leq\mathrm{negl}
$$

则称之为适应性选择消息下存在性不可伪造的(Existentially Unforgeable Under an Adaptive Chosen-Message Attack)

##### PRF-MAC

PRF可以用于构造MAC。假设 $F$是一个PRF，我们可以直接定义输出tag为 $t=F_k(m)$，并且令 $\mathrm{Vrfy}$为当且仅当 $t=F_k(m)$时输出 $1$。

可以证明，该MAC是存在性不可伪造的。可以由以下方法，将定长MAC $(\mathrm{Gen',Mac',Vrfy'})$构造变长 $(\mathrm{Gen,Mac,Vrfy})$，将长度为 $n$扩展到 $2^{n/4}$：
 
(1) $\mathrm{Gen'=Gen}$

(2) $\mathrm{Mac}$输入$k\in\\{0,1\\}^n,m\in\\{0,1\\}^{\ast}$，在用 $0$填充后令 $l\lt 2^{n/4}$，将 $m$分成 $n/4$长度的段 $m_1,\dots,m_d$，选取随机标识码 $r\leftarrow\\{0,1\\}^{n/4}$。计算 $t_i\leftarrow\mathrm{Mac}'_k(r||l||i||m_i)$，其中 $i,l$使用二进制编码。输出 $t=(r,t_1,\dots,t_d)$

(3) $\mathrm{Vrfy}$输入 $k$和 $m$，消息长度 $l\lt 2^{n/4}$，标记 $t=(r,t_1,\dots,t_{d'})$，将 $m$分块 $m_1,\dots,m_d$，当且仅当 $d=d'$时继续进行；若每一个 $\mathrm{Vrfy}'_k(r||l||i||m_i,t_i)=1$，则输出 $1$。

这种延长得到的MAC也可以继承存在性不可伪造的特性。

##### CBC-MAC

可以使用CBC模式来构造MAC。令 $F$为PRF，定义CBC-MAC如下： $\mathrm{Gen}$生成 $k\leftarrow\\{0,1\\}^n$； $\mathrm{Mac}$:输入 $k$和长度 $n\cdot l(n)$的 $m$，将 $m$解析成分块 $m_1,\dots,m_l$， $t_0=0^n$，并构造 $t_i=F_k(t_{i-1}\oplus m_i)$。输出 $t_l$作为tag。 $\mathrm{Vrfy}$:输入密钥 $k$，监测 $m$长度是否为 $n\cdot l(n)$，当且仅当 $t=\mathrm{Mac}_k(m)$时输出 $1$。

可以证明，CBC-MAC若有 $F$为PRF，则该MAC是存在性不可伪造的。

### Hash

##### 带密钥Hash函数

带密钥Hash函数是一对多项式时间算法$(\mathrm{Gen,}H)$，其中$\mathrm{Gen}$输出密钥$s$，$1^n$隐含在$s$中。而 $H^s(\sim)$是一个长度为 $l(n)$的Hash函数。如果输入$x\in\\{0,1\\}^{l'(n)},l'(n)\lt l(n)$，则称该函数为输入 $l'(n)$的Hash函数。

一个Hash函数是抗碰撞(Collision Resistant)的，如果

$$
\mathrm{Pr}[\mathrm{Hashcoll}_{\mathcal{A},H}(n)=1]\leq\mathrm{negl}
$$

##### HMAC

可以使用Hash函数构造MAC，即HMAC。令 $H$为一个定长抗碰撞Hash，令HMAC如下：

(1) $\mathrm{Gen}$:输入$1^n$得到$s$，并且随机选择 $k\leftarrow\\{0,1\\}^{n'}$。令 $\mathrm{opad,ipad}\in\\{0,1\\}^{n'}$。

(2) $\mathrm{Mac}:$对密钥 $(s,k)$和长度 $L$的消息 $m$，输出标记

$$
t=H^s((k\oplus\mathrm{opad})||H^s((k\oplus\mathrm{ipad})||m))
$$

(3) $\mathrm{Vrfy}:$直接验证 $\mathrm{Mac}_k(m)$是否为 $t$。

### 差分分析

##### Boolean函数

一个Boolean函数，指 $\mathbb{F}_2^{n}\to\mathbb{F}_2^m$的一个映射。如果 $m=1$，我们称该函数为二元Boolean函数。如果 $m=n$，则称其为一个Boolean变换。可逆的Boolean变换称作Boolean置换。

##### Parity与Correlation

一个bit串的parity是由其若干个bit的异或组成的二元Boolean函数，每一个parity $f(x)$都可以使用一个 $n$元 $\mathbb{F}_2$-向量 $w$来表示，具体表示形式为内积 $f(x)=w^Tx$。






