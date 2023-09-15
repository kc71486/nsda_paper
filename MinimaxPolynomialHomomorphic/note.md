# Paper titleMinimax Approximation of Sign Function by Composite Polynomial for Homomorphic Comparison

# Abstract

# Introduction

# Related work

# Prelimary
## Fully Homomorphic Encryption
全同態加密 (FHE) 在一般非對稱加密上定義同態加法及同態乘法，也就是滿足: 
存在一個函數 Add 及 Mul，使 $\text{Add}(Enc(u),Enc(v))=Enc(u+v)$ 和 $\text{Mul}(Enc(u),Enc(v))=Enc(u*v)$ 成立。
## CKKS Scheme
<img src="https://github.com/kc71486/nsda_paper/raw/main/MinimaxPolynomialHomomorphic/img/CKKSdiagram.jpg" width="80%" alt="CKKS encode and decode"> <br>
解釋來自 <a href="https://blog.openmined.org/ckks-explained-part-1-simple-encoding-and-decoding">https://blog.openmined.org/ckks-explained-part-1-simple-encoding-and-decoding</a>
CKKS 是一種特別的加密法，主要用於明文為浮點數的同態運算，有以下幾點特色:
 * 明文為浮點複數
 * 加密解密過程會喪失一些精準度
 * 支援全同態加密 (加法、乘法、縮放)
### CKKS Encode and Decode:
<img src="https://github.com/kc71486/nsda_paper/raw/main/MinimaxPolynomialHomomorphic/img/roots.png" width="40%" alt="roots when M=8" title="roots when m=8"> <br>
encode 和 decode 基本上就是把訊息從 $z\in\mathbb{C}^{N/2}$ 陣列轉成多項式 $m(X)=\mathbb{Z}[X]/(X^{N}+1)$。<br>
encode部份，需要找出過 $(\omega^{1},z[0])$、$(\omega^{3},z[1])$、...、$(\omega^{2*(N/2)-1},z[N/2-1])$ 的點。因為多項式必須為 $\mathbb{R}$，共扼複數的 z 值必須相同，下半圓順序與上半圓相反。因為多項式必須為 $\mathbb{Z}$，必須先將 z 陣列所有值乘上一個倍數。
:::info
以上圖為例，先進行 $\left\{ \begin{array}{cl}z[0]=\text{round}(z[0]*\text{scale})\\z[1]=\text{round}(z[1]*\text{scale})\\\end{array} \right.$ ，再找出過 $(\omega^{1},z[0])$、$(\omega^{3},z[1])$、$(\omega^{5},z[1])$、$(\omega^{7},z[0])$ 的點 (高斯消去法解聯立方程式)，得到的答案會非常接近整數，最後再四捨五入即可。
:::
decode部份，先還原比例，再將 $\omega^{n}$ 代入多項式，得到的答案即為對應陣列內容。
:::info
以上圖為例，先進行 $\left\{ \begin{array}{c}p[0]=p[0]/\text{scale}\\p[1]=p[1]/\text{scale}\\p[2]=p[2]/\text{scale}\\p[3]=p[3]/\text{scale}\end{array} \right.$，再進行 $\left\{ \begin{array}{c}z[0]=p(\omega^{1})\\z[1]=p(\omega^{3})\end{array} \right.$ 。
:::
### Learning with Error
Learning with Error (LWE) 是一種特殊的全同態加密技術，目前沒有任何量子演算法能有效解決此問題，它使用一定程度的誤差讓計算難度大幅上升。例如:
$\left\{ \begin{array}{l}14\cdot s_{1}+15\cdot s_{2}+5\cdot s_{3}+2\cdot s_{4}\cong 8\text{ mod }17\\13\cdot s_{1}+14\cdot s_{2}+14\cdot s_{3}+6\cdot s_{4}\cong 16\text{ mod }17\\6\cdot s_{1}+10\cdot s_{2}+13\cdot s_{3}+1\cdot s_{4}\cong 3\text{ mod }17\\\end{array} \right.$，
這裡誤差的引入，導致如果使用高斯消去會讓噪音過大而無法分辨。密碼學的應用主要使用公鑰 $p=(b, A)=(-A\cdot s+e,A)$ 及密鑰 $s$，$A$ 為均勻分布的矩陣，$s$、$e$ 為陣列。加密及解密分別為:
$c=(\mu,0)+p=(\mu-A\cdot s+e,A)=(c_{0},c_{1})$
$\tilde{\mu}=c_{0}+c_{1}\cdot s=\mu-A\cdot s+e+A\cdot s=\mu+e\cong \mu$
### Ring Learning with Error
LWE 主要問題是公鑰 A 大小為 $O(n^{2})$，且乘法運算時間複雜度為 $O(n^{2})$，當 n 變大時這將變得不切實際。
Ring Learning with Error (RLWE) 將工作範圍從矩陣變成多項式。這裡使用公式相同，只是 $A$ 為均勻分布的多項式，$s$、$e$ 為小多項式。
這樣做的好處有:
 * 公鑰 A 大小變成 $O(n)$，明文、密文、公鑰、密鑰大小都是 $O(n)$。
 * 利用<a href="https://faculty.sites.iastate.edu/jia/files/inline-files/polymultiply.pdf">多項式乘法的快速傅立葉轉換</a>，我們可以將乘法運算的時間複雜度變成 $O(n\cdot\log(n))$。

RLWE 還是一個安全的加密技術，目前也沒有任何量子演算法能有效解決此問題。
CKKS 要經過 Encode 和 Decode 就是為了要套用 RLWE 演算法。
### Homomorphic operations
#### Homomorphic addition
首先看加法，假設密文 $c=(c_{0},c_{1})$、$c'=(c'_{0},c'_{1})$，則 $c_{add}=c+c'=(c_{0}+c'_{0},c_{1}+c'_{1})$。
證明:
:::info
$\large c_{add,0}+c_{add,1}\cdot s=c_{0}+c_{0}'+c_{1}\cdot s+c_{1}'\cdot s=\mu+\mu'+2e\cong \mu+\mu'$
:::
#### Ciphertext-Plaintext multiplication
接著看密文與明文間的乘法，假設明文 $\mu'$ 和密文 $c=(c_{0},c_{1})$，則 $c_{mult}=\mu'\cdot c=(\mu'\cdot c_{0},\mu'\cdot c_{1})$。
證明:
:::info
$\large c_{mult,0}+c_{mult,1}\cdot s=\mu'\cdot c_{0}+\mu'\cdot c_{1}\cdot s=\mu'\cdot\mu+\mu'\cdot e\cong \mu'\cdot\mu$
:::
#### Homomorphic multiplication
接著看密文與密文間的乘法，像前兩種方法直接乘會發現密文從兩項便三項 ($(c_{0},c_{1})$ 變成 $(c_{0},c_{1},c_{2})$)，而這是不理想的。因此要找一個方法變回兩項，該方法為 $\text{Relin}((d_{0},d_{1},d_{2}),evk)=(d_{0},d_{1})+\text{round}(p^{-1}\cdot d_{2}\cdot evk)$，其中 $evk=(-a_{0}\cdot s+e_{0}+p\cdot s^{2},a_{0})\text{ mod }(p\cdot q)$、p 為大整數、q 為模數、$a_{0}$ 為隨機數。
<img src="https://github.com/kc71486/nsda_paper/raw/main/MinimaxPolynomialHomomorphic/img/ckks_rescaling.PNG" width="60%" alt="multiplication and recaling image" title="rescaling explained"> <br>
密文與密文間的乘法與其他方法不同的是乘法會導致誤差越來越大，因此必須重新縮放。重新縮放有次數限制，如上圖，每次縮放都會消費一個縮放次數，因此必須事先知道重新縮放次數。能給重新縮放的次數越多，準確度越低(q 越大)，計算時間也越長 (N 越大)。
<img src="https://github.com/kc71486/nsda_paper/raw/main/MinimaxPolynomialHomomorphic/img/ckks_security_degree.png" width="60%" alt="Security as a function of polynomial degree and modulo" title="Security as a function of polynomial degree and modulo"> <br>
整個密文與密文間的乘法流程為:
1. $\text{CMult}(c,c')=(d_{0},d_{1},d_{2})$
2. $\text{Relin}((d_{0},d_{1},d_{2}),evk)=(d_{0},d_{1})+\text{round}(p^{-1}\cdot d_{2}\cdot evk)$
3. $RS_{l\ \rightarrow\ l-1}(c)=\text{round}(\frac{q_{l-1}}{q_{l}}c)\text{ mod }(q_{l-1})$

證明:
:::info
第三步對結果沒影響，前兩步 消掉 $p$ 之後，$(c_{relin,0},c_{relin,1})=(d_{0},d_{1})+d_{2}\cdot (s^{2},a_{0})\text{ mod }q=(d_{0}+d_{2}\cdot s^{2},d_{1})$，這樣 $\large c_{relin,0}+c_{relin,1}\cdot s=d_{0}+d_{2}\cdot s^{2}+d_{1}\cdot s$
:::
## Comparison Operation in Fully Homomorphic Encryption
首先定義
$comp(u,v)=\left\{ \begin{array}{cl}1 & \text{if }u\gt v\\1/2 & \text{if }u=v\\0 &\text{if }u\lt v\\\end{array} \right.$，$sgn(x)=\left\{ \begin{array}{cl}1 & \text{if }x\gt 0\\0 & \text{if }x=0\\-1 &\text{if }x\lt 0\\\end{array} \right.$。
$sgn(x)$ 可以簡單推導到 $comp(u,v)$，因此只講前面部分。
定義 2。對於 $\alpha\gt0$ 且 $0\lt\epsilon\lt1$，如果在 $[-1,1]$ 範圍 $p$ 滿足:
> $\parallel p(x)-sgn(x)\parallel_{\infty,\tilde{R_{\epsilon}},1}\le2^{-\alpha}$，

則 p 被稱作 $(\alpha,\epsilon)\text{-}close$ to $sgn(x)$。
其中 $\parallel x\parallel _{\infty }$ 為在 $\tilde{R}_{\epsilon},1$ 域下的下圖範圍，$p(x)$ 為趨近多項式。
<img src="https://github.com/kc71486/nsda_paper/raw/main/MinimaxPolynomialHomomorphic/img/infnorm.jpg" width="30%" alt="infinity norm image"> <br>
比較部分，式子變成:
> $|\tilde{p}(x)-comp(u,v)|\le2^{-\alpha}\text{ for any }u,v\in[0,1]\text{ satisfying }|u-v|\ge\epsilon$，

這種情況下 p 被稱作 $(\alpha-1,\epsilon)\text{-}close$
:::info
上面式子我的理解是，$p(x)$ 在 $x$ 與 0 差距大於 $\epsilon$ 時，誤差必須小於 $2^{-\alpha}$。需要注意的是上面式子只定義 $[-1,1]$ 範圍。
下面式子我的理解是，$p(x)$ 在兩數差距大於 $\epsilon$ 時，誤差必須小於 $2^{-\alpha}$。
:::
因為自助抽樣法(bootstrapping) 非常耗時，最小化同態加密的計算深度非常重要，本文重點就在趨近 $sgn(x)$
## Approximation Theory
多項式 p (在 D 區間、次數 n) 被稱為函數 f 的 minimax 趨近，如果在所有次數 $\le n$ 的多項式中 $max_{D}\parallel p(x)-f(x)\parallel_{\infty}$ 的值最小。
由其他論文已知該多項式 (在 D 區間、在次數 n) 是唯一的，並且該趨近能用下面的方法求得。
### Haar's condition:
一組函數 $\{\phi{1},...\phi{n}\}$ 符合 Haar's condition，如果
$D[x_{1},...,x_{n}]=\begin{bmatrix}\phi_{1}(x_{1})&\cdots&\phi_{n}(x_{1})\\\vdots &\ddots&\vdots \\\phi_{1}(x_{n})&\cdots&\phi_{n}(x_{n})\end{bmatrix}$
在任何 $x_{1},...,x_{n}$ 皆為線性獨立。
線性組合 $\{\phi{1},...\phi{n}\}$ 也被稱作廣義多項式。
### Chebyshev alternation theorem
## Algorithms for Minimax Approximation
# Model and design

# Proposed scheme

# Results

# Conclusion