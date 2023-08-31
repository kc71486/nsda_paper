# Achieving Privacy-Preserving Online Diagnosis With Outsourced SVM in Internet of Medical Things Environment

# Abstract

# Introduction

# Related work

# Prelimary

## Paillier Cryptosystem
$N=pq$, where $gcd(pq, (p-1)(q-1)) = 1$
*g* = random number, $gcd(g, N) = 1$
該演算法為 <span id="homomorphicEncrypt">homomorphic</span> (同態加密)，符合以下兩種關係:

$E(x_{1})*E(x_{2}) = E(x_{1}+x_{2})$
$E(x_{1})^b = E(x_{1}*x_{2})$

Plaintext: $0\le m\lt N$, Ciphertext in $\mathbb{Z}_{N^{2}}$
Encrypt:
1. Select random $0\le r\lt N$ with $gcd(r, N) = 1$
2. Compute the ciphertext to be $g^{m}\cdot r^{n}\ mod\ N^{2}$

Decrypt:
1. Compute the plaintext to be $m=L(c^{\lambda}\ mod\ n^2)\cdot \mu \ mod\ n$

## Ring
System that support 
* addition commutative (加法交換律)
* multiplication associative (乘法結合律)
* distributive (分配律)
## Support Vector Machine
<img src="https://hackmd.io/_uploads/HybPBSv6n.png" width="50%"> <br>
(上圖取自維基，與本篇相關性不高)<br>
SVM 的基本公式為 $d(t)=\sum_{i\in S}^{}(\alpha_{i}y_{i}x_{i}t)+b$ ，其中:
$S$ = support vector 的集合
$x_{i}$ = support vector (資料點的向量)
$y_{i}$ = label (資料點的標籤)，只能是 1 或 -1
$\alpha_{i}$ = Lagrange multiplier
$b$ = intercept (截距)
$t$ = 欲分類的 m 維特徵向量
$d(t)>0$ 時為 positive class，反之則 negative <br>
當然訓練樣本不會每次都能用線性區隔，此時就要用到kernal function $K$
此時方程式可以改寫成:
$d(t)=\sum_{i\in S}^{}(\alpha_{i}y_{i}K(x_{i},t))+b$
其中 $K(x_{i},t)=e^{-\gamma\left\|x_{i}-t\right\|_{2}^{2}}$ , $\gamma\gt 0$ <br>
:::info
本篇用到的 kernel function 為 RBF kernel function
:::
為了解決多類別問題，本篇採用 One versus Rest 方法，也就是先拿其中一類當作+1類，剩下的類別當作-1類，然後用二元分類器學習一次得到一個decision hyperplane；然後第二次繼續拿下一類當作+1類，剩下的類別當作-1類，學習一個decision hyperplane；直到所以的類別都有當作+1類為止，最後看decision value哪個比較大，資料就判給哪一類。
此時方程式變成:
<span id="coreFormula">$M_{c}=\large\substack{argmax \\i=1,...,k}\Big(\sum_{j\in S_{i}}(\alpha_{ij}y_{ij}e^{\gamma\left\| x_{ij}-t \right\|_{2}^{2}})+b_{i}\Big)$</span>
其中 $M_{c}$ 為具有最大決策函數值的對應類別標籤
<br>
後面用到的SVM參數有些不同，主要有以下四類:
$x_{ij}$ = support vector (資料點的向量)
$c_{ij}$ = $\alpha_{ij}$ (Lagrange multiplier) 與 $y_{ij}$ (label) 的乘積
$b_{i}$ = intercept (截距)
$l_{i}$ = label
# Model and design

## System model
<img src="https://hackmd.io/_uploads/B1cQfBva3.png)" width="80%"> <br>
The system consists of:
* 1 healthcare provider
* 2 non colluding cloud server
* Several users
## Threat model
Threat:
* Honest-but-curious attacks:
    任何參與者都有可能在不破壞系統的前提下竊取資料。
* Collusion attacks:
    伺服器與部分使用者之間的串通，不包含兩個伺服器之間的串通。
* External attacks
# Proposed scheme

<img src="https://hackmd.io/_uploads/BJ3IoHPah.png" width="100%"> <br>
## System initialization
伺服器1生成密鑰對 $(PK_{A},SK_{A})$
伺服器2生成密鑰對 $(PK_{B},SK_{B})$
合法使用者 $U_{i}$ 生成 $(PK_{U_{i}},SK_{U_{i}})$
## Data outsourcing
### Parameter split
HP 將 {x}, {c}, b, l 拆成 $\{(x^{A} + x^{B})\},\ \{(c^{A} + c^{B})\}$ ... 等.
### Data encrypt
(HP)
經由AES $K_{A}$, $K_{B}$ 隨機生成密鑰
$params\overset{K_{A}}{\longrightarrow}EP^{A}$
$K_{A}\overset{PK_{A}}{\longrightarrow}EK^{A}$
$params\overset{K_{B}}{\longrightarrow}EP^{B}$
$K_{B}\overset{PK_{B}}{\longrightarrow}EK^{B}$
將 $EP^{A}, EK^{A}, EP^{B}, EK^{B}$ 傳至雲端
:::info
使用 AES 處理大資料 (也就是實際資料) ，並用非對稱加密處理小資料 (這裡是AES密鑰) 能提高加密及解密速度
:::
### Data process
(兩個伺服器, 以 $CS_{A}$ 為例)
$EK^{A}\overset{SK_{A}}{\longrightarrow}K_{A}$
$EP^{A}\overset{K_{A}}{\longrightarrow}params$
Change ( {x}, {c}, b, l ) into ( {X}, {C}, B, L ), using scale and round
:::info
基本上只是將 float 轉成 int.
:::
## User request generating
(使用者 i)
將資料 *t* 拆成 $t^{A}$ 及 $t^{B}$
隨機生成 $K_{U_{i}}^{A}$ 和 $K_{U_{i}}^{B}$
$t^{A}\overset{K_{U_{i}}^{A}}{\longrightarrow}Et^{A}$
$K_{U_{i}}^{A}\overset{PK_{A}}{\longrightarrow}EK_{U_{i}}^{A}$
$t^{B}\overset{K_{U_{i}}^{B}}{\longrightarrow}Et^{B}$
$K_{U_{i}}^{B}\overset{PK_{B}}{\longrightarrow}EK_{U_{i}}^{B}$
將 $Et^{A}, EK_{U_{i}}^{A}, Et^{B}, EK_{U_{i}}^{B}$ 傳至雲端
## Online diagnosis
(兩個伺服器, 以 $CS_{A}$ 為例)
$EK_{U_{i}}^{A}\overset{SK_{A}}{\longrightarrow}K_{U_{i}}^{A}$
$EP^{A}K_{U_{i}}^{A}\overset{K_{A}}{\longrightarrow}t^{A}$
將 $t^{A}$ 轉成 $T^{A}$ (float 變 int)
<br>
兩個伺服器共同執行 SVM 生成分類結果。這裡主要分成三大步:
1. decision function computation
2. classification result generating
3. result returning.
### Decision function computation
<br><img src=https://hackmd.io/_uploads/ByJ97X5a3.png width="80%"><br>
演算法2為本區主要流程
本質就是基於<a href=#coreFormula>這個</a>的實作
k 為 label 數量
X 不隨 i 改變，只隨 j 改變
:::info
X 不隨 i 改變，因為不同 classifier 不會影響相同物件下的原本座標
X 隨 j 改變，因為不同 j 是不同物件
:::
:::warning
似乎可以把 $X_{ij}$ 變成 $X_{j}$
:::
C 隨 i 和 j 改變<br>
本演算法先根據 j (哪個物件)計算出 kernel 坐標，再將 kernel 坐標與 C 相乘並加總，最後加上截距得到對應 decision value ，此步驟重複至所有 classifier 全部輪完
:::info
用 $M_{A}$ $M_{B}$ 儲存 kernel ，因為相同座標下 kernel 坐標不會因為 classifier 的改變而改變
:::
<br><img src=https://hackmd.io/_uploads/B1fjX79p2.png width="80%"><br>
演算法3主要計算 kernal
<br><img src=https://hackmd.io/_uploads/SJqh7Q56n.png width="80%"><br>
演算法4為兩個伺服器共同計算兩數乘積，本篇是將 Z 拆成 $(X^{A}Y^{A}+X^{A}Y^{B}+X^{B}Y^{A}+X^{B}Y^{B})$，利用<a href="#homomorphicEncrypt">同態加密</a>將XY交叉的部分安全算出，過程中 B 為了不讓 A 得知自己的數值，將 $\gamma$ 混進 $ET$ 後給 A ，自己再減去做補償
:::info
若不加入 $\gamma$ ，A 在最後一步解密時可知道 $X^{A}Y^{B}+X^{B}Y^{A}$ 的值，其中 A 知道 $X^{A}$ 及 $Y^{A}$，B 的可能值只有平面上的一條線，這樣會大幅增加 B 洩漏的風險
:::
最終結果就是
$\begin{array}{rcl}
Z=Z^{A}+Z^{B}& = & (X^{A}Y^{A}+\gamma+X^{A}Y^{B}+Y^{A}X^{B}+X^{B}Y^{B}-\gamma) \\& = & (X^{A}+X^{B})(Y^{A}+Y^{B}) \\& = & XY\end{array}$
:::danger
論文寫的 $Z^{A}+Z^{B}=(X^{A}Y^{A}+\gamma+X^{A}X^{B}+Y^{A}Y^{B}+X^{B}Y^{B}-\gamma)=XY$ 是錯誤的，由演算法推演及式子化簡皆能得知
:::
```java
int plainSM(int XA, int YA, int XB, int YB) { // 無加密 & 簡化
    // CSA: () -> (XA, YA)
    // CSB: (XA, YA) -> (ET, ZB)
    int r = new Random().nextInt(NA);
    int ZB = modNA(XB * YB - r);
    int T = r + XA * YB + YA * XB;
    // CSA: (T) -> (ZA)
    int ZA = T + XA * YA;
    return ZA + ZB;
}
```
### Classification result generating
<br><img src=https://hackmd.io/_uploads/HyKwkOsan.png width="80%"><br>
$W_{i}$ 為最佳決策的值與目前決策的值之差
$Z_{i}$ 為最佳決策的標籤與目前決策的標籤之差
$U_{i}$ 為 1 時原本最佳決策比較好，為 0 時目前新的決策比較好，需要注意的是因為 SC 最後用 SM，A 與 B 不會都拿到 0.5 或 0
中間用兩個 SM 是為了保證 W 和 Z 不洩漏
最後兩行實際意思就是
$MD_{i}=U_{i}(MD_{i-1}-D_{i})+D_{i}$
$ML_{i}=U_{i}(ML_{i-1}-L_{i})+L_{i}$
:::info
也就是 $MD_{i}=(U_{i} == 1)?(MD_{i}):(D_{i})$ 和 $ML_{i}=(U_{i} == 1)?(ML_{i}):(L_{i})$
這種寫法是 branchless program 常用技巧
:::
```java
int plainSMCF(int[] DA, int[] LA, int[] DB, int[] LB) { // 無加密 & 簡化
    int MaxLA = LA[0];
    int MaxLB = LB[0];
    int MaxDA = DA[0];
    int MaxDB = DB[0];
    // skip first element
    for(int i : new Range(1, DA.length)) {
        // CSA
        int WA = MaxDA - DA[i];
        int ZA = MaxLA - LA[i];
        // CSB
        int WB = MaxDB - DB[i];
        int ZB = MaxLB - LB[i];
        // both, 實際上(SDA, SDB)和(SLA, SLB)不會平衡
        int U = plainSC(MaxDA, DA[i], MaxDB, DB[i]);
        int SDA = (WA + WB) * U / 2;
        int SDB = (WA + WB) * U - SDA;
        int SLA = (ZA + ZB) * U / 2;
        int SLB = (ZA + ZB) * U - SLA;
        // CSA
        MaxDA = SDA + DA[i];
        MaxLA = SLA + LA[i];
        // CSB
        MaxDB = SDB + DB[i];
        MaxLB = SLB + LB[i];
    }
    return MLA + MLB;
}
```
<br><img src=https://hackmd.io/_uploads/r1A51ujah.png width="80%"><br>
演算法6
S1 = A 和 B 的差的絕對值, S2 = 一個比 NA 小很多的數
S1 和 S2 符合以下關係:
$\forall S_{A}=A-B,S_{A}S_{B}+S_{B}\lt \frac{N_{A}}{2}$
r1 和 r2 的存在目的是為了防止 EH 在解密成 H 時洩漏 DB
因為 s2 的限制， r1 和 r2 的存在並不會影響最終答案，也就是說
$r1 * (D^{A} + D^{B}) + r2 < \frac{N}{2}\equiv (D^{A} + D^{B}) < \frac{N}{2}$
U1 的存在目的是為了不讓 CSA 知道結果 (CSB 本來就不知道)
```java
int plainSC(int AA, int BA, int AB, int BB) { // 無加密 & 簡化
    // CSA: () -> (DA)
    int DA = AA - BA;
    // CSB: (DA) -> (H)
    int DB = AB - BB; // DA + DB = A - B
    int r1 = new Random().nextInt(S2); // S2 << NA
    int r2 = new Random().nextInt(r1);
    int U1 = new Random().nextInt(2); // 1 = reverse
    int H;
    if(U1 == 0) {
        // S1 = maxdiff(A, B)
        // assert(S1 * S2 + S2 < NA / 2)
        H = modNA((r1 * (DA + DB) + r2));
    }
    else {
        H = modNA((r1 * (DA + DB) * -1 + r2)); // reverse
    }
    // CSA: (H) -> ()
    int U2;
    if(H < NA / 2) {
        U2 = 1; // A > B if not reverse
    }
    else {
        U2 = 0;
    }
    // equals (U2 - U1) * (U2 - U1)
    return plainSM(U2, U2, modNA(U1*-1), modNA(U1*-1));
}
```
### Result returning
(兩個伺服器)
$ML_{k}^{A}\overset{PK_{U_{i}}}{\longrightarrow}EML_{k}^{A}$
$ML_{k}^{B}\overset{PK_{U_{i}}}{\longrightarrow}EML_{k}^{B}$
將 $EML_{k}^{A}$ 和 $EML_{k}^{B}$ 傳給使用者 $U_{i}$
## Result reading
(使用者 $U_{i}$)
$EML_{k}^{A}\overset{SK_{U_{i}}}{\longrightarrow}ML_{k}^{A}$
$EML_{k}^{B}\overset{SK_{U_{i}}}{\longrightarrow}ML_{k}^{B}$
$L_{k}=ML^{A}+ML^{B}$
# Security analysis
## Against Honest-but-Curious Attacks
### Against Honest-but-Curious Client
在這個方法中，使用者再傳送資料後直到接收結果前都不會與伺服器聯繫，因此不會知道任何關於訓練模型或參數或任何中間結果
### Against Honest-but-Curious Cloud
演算法2、5本身除了呼叫別的演算法外兩個伺服器沒有任何互動，只有演算法3、4、6有互動<br>
演算法3...<br>
演算法4中，CSA 將 $X^{A}$、$Y^{A}$ 加密後給 CSB (CSB 無法得知內容)，CSB 利用同態加密算出 $X^{A}Y^{B}+Y^{A}X^{B}$ 的加密版得到 ET，中間加入 r 使 CSA 無法得出確切數值。在這過程中，CSA 和 CSB 都拿到 X、Y、XY 的部分數值，但是都沒有足夠資訊還原出 X、Y、XY<br>
演算法6中，CSA 將 $D^{A}$ 加密後給 CSB (CSB 無法得知內容)， CSB 利用同態加密算出 $D^{A}+D^{B}$ 的加密版得到 EH，中間加入 r1、r2 使 CSA 在無法得出確切數值的情況下仍有相同結果，再用隨機選擇的 U1 決定 U2 是否反向，最後由 CS1 解密 EH 得到 U2，在這過程中，CSA 只知道 $U_{2}$、$D^{A}$，CSB 只知道 $U_{1}$、$D^{B}$，兩者都沒有足夠資訊還原出其他訊息<br>
## Against Collution Attacks
## Against External Attacks
# Evaluation and performance analysis

# Conclusion
