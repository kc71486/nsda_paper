# Joint UAV Deployment and Power Allocation for Secure Space-Air-Ground Communications

# Abstract

# Introduction
太空-空中-陸地通訊網路 (Space-air-ground Communications networks, SAGCN) 被視為吸引人的架構，尤其對於遠程物聯網 (Internet of Remote Things, IoRT)，考量基地台 (BS) 很少被架設在偏遠地區。
目前很多研究在 SAGCN 上，包括服務品質 (QoS) 和資安議題。
就本文所知，多層無人機協助 SAGCN 安全網路還在萌芽階段。
# Related work

# Prelimary

# System model
<img src="https://github.com/kc71486/nsda_paper/raw/main/UAVPowerSecure/img/SAGCNmodel.png" width="80%" alt="model(Fig 1)"><br>
Eves: 竊聽者(eavesdropper)
本篇假設 CSI (通道狀態資訊)，如位置資訊，能被無人機知道。
:::info
知道的方法在
Detecting passive eavesdroppers in the MIMO wiretap channel. 和
Unmanned aerial vehicle meets vehicle-to-everything in secure communication.
:::
本篇傳輸分兩階段，第一階段是 IoRT-UAV，第二階段是 UAV-衛星，兩個階段使用頻段不ㄧ樣。
本篇假設無人機具有全雙工傳輸能力 (同時傳輸和接收訊號)。
無人機的天線分別有:
1. 用來進行 IoRT-UAV 通訊的向下天線。
2. 用來轉發訊號到衛星的全向天線。
3. 用來干擾 IoRT-Eves 訊號的向下天線。
4. 用來干擾 UAV-Eves 訊號的向下天線。

第一階段通道為 $h^{I\to U}=k_{I,U}d^{-\alpha}$。
第二階段通道為 $h^{U\to S}=G^{U}G^{S}c/(4\pi fd)$。
其中 $G$ 為無向增益，$k$ 為有向增益( $k=k_{0}/\Theta^2$ if $0\le[\theta,phi]\le\Theta$ otherwise 0)，$d$ 為距離，$\alpha$ 為通道衰減率
:::info
第一階段訊號強度與直線距離及通道衰減率有關。
第二階段訊號強度與距離及載波波長有關。
:::
## Data Transmission Capacity Model
在第一階段，i 節點的數據收集率為 $R_{i}^{I\to U}(p_{i}^{I},u)=B_{I,i}log_{2}(1+p_{i}^{I}h_{i}^{I\to U}(u)/N)$
在第二階段，數據收集率為 $R^{U\to S}(p_{tr}^{U})=B_{S}log_{2}(1+p_{tr}^{U}h^{U\to S}(u)/N)$
:::info
通道傳輸上限 $C=B\cdot log_{2}(1+SNR)$
:::
這裡 p 表示功率，N 表示雜訊，$B$ 表示該單位占用的頻寬。

本篇文章假設所有節點占用頻寬皆相同。
## Jamming model
節點 I 與 竊聽者 E 間的通道為 $h^{I\to E}=k_{I,E}d^{-\alpha}g^{I\to E}$。
$g^{I\to E}$ 為符合準靜態瑞利分布的小幅度衰減項。

無人機 U 與 竊聽者 E 間第一階段的通道為 $h^{U\to E}(u)=k_{U,E,\text{I}}d^{-\alpha}$。

無人機 U 與 竊聽者 E 間第二階段的通道為 $h^{U\to E}(u)=k_{U,E,\text{II}}d^{-\alpha}$。
## Secrecy Rate Model
階段一的保密率為:
$R_{s,i}^{I\to U}(p_{i}^{I},p_{J,I}^{U\to E},u)=[B_{I,i}\log_{2}\big(1+\frac{p_{i}^{I}h_{i}^{I\to U}(u)}{|\tilde{h}_{LI,J}|p_{J,I}^{U\to E}+N_{0}^{U}}\big)-R_{je,i}^{I\to E}(p_{i}^{I},p_{J,I}^{U\to E},u)]^{+},j\in \{u\}$，
其中 $R_{ue,i}^{I\to E}(p_{i}^{I},p_{J,I}^{U\to E},u)=B_{I,i}\max_{m\in\mathcal{M}}\log_{2}\Big(1+\frac{p_{i}^{I}h_{i,m}^{I\to E}}{N_{J,I,m}^{U\to E}+N_{0}^{E}}\Big)$。
階段二的保密率為:
$R_{s,i}^{U\to S}(p_{tr}^{U},p_{J,II}^{U\to S},u)=[B_{S}\log_{2}\big(1+\frac{p_{re}^{U\to S}} {N_{0}^{S}}\big)-R_{je}^{U\to E}(p_{tr}^{U},p_{J,II}^{U\to E},u)]^{+},j\in \{u\}$，
其中 $R_{ue}^{U\to E}(p_{tr}^{U},p_{J,II}^{U\to E},u)=B_{S}\max_{m\in\mathcal{M}}\log_{2}\Big(1+\frac{p_{tr}^{U}h_{m,II}^{U\to E}(u)}{N_{J,II,m}^{U\to E}+N_{0}^{E}}\Big)$。
$p$ 表示傳輸或干擾的能量，$N$ 表示接收到的額外雜訊，$N^{U\to E}$ 表示竊聽者接收到的干擾訊號。
階段一上面的 $|\tilde{h}_{LI,J}|p_{J,I}^{U\to E}$ 為干擾抑制後的迴路干擾，本文使用的全雙工傳輸可以有效抑制干擾，因此 $\tilde{h}_{LI,J}$ 可視為非常小的數值。
:::info
基本上訊雜比最高的竊聽者訊雜比越低，或是設備/衛星訊雜比越高，都能有效提升這兩個階段的數值。通道品質越好的竊聽者越需要主動發出干擾訊號。
:::
# Problem formulation
## Initial problem
本文重點是最大化總保密率。
總保密率公式為 $R^{I\to S}(u)=\min\{\sum_{i=1}^{I}R_{s,i}^{I\to U}(p_{i}^{I},p_{J,I}^{U\to E},u),\ R_{s}^{U\to S}(p_{tr}^{U},p_{J,II}^{U\to E},u)\}$。
:::info
作者說第一階段的保密率不能高於第二階段 (第一階段優化效果較明顯，第一階段和第二階段優化方向完全不同)。
第一階段在最佳化問題上會成為最佳化問題目標。
:::
因此該最佳化問題可定義成:
$\begin{matrix}\mathcal{P}_{1}:&\max_{u,p_{tr}^{U},p_{J,I}^{U\to E},p_{J,II}^{U\to E}}\sum_{i=1}^{I}R_{s,i}^{I\to U}(p_{i}^{I},p_{J,I}^{U\to E},u)&(a)\\s.t.&\sum_{i=1}^{I}R_{s,i}^{I\to U}(p_{i}^{I},p_{J,I}^{U\to E},u)\le R_{s}^{U\to S}(p_{tr}^{U},p_{J,II}^{U\to E},u),&(b)\\&x_{U}\in[0,X_{U,max}],&(c)\\&y_{U}\in [0,Y_{U,max}],&(d)\\&h_{U}\in [H_{min},H_{max}],&(e)\\&p_{tr}^{U}+p_{J,I}^{U\to E}+p_{J,II}^{U\to E}\le P_{max},&(f)\\&p_{tr}^{U}\ge 0,&(g)\\&p_{J,I}^{U\to E}\ge 0,&(h)\\&p_{J,II}^{U\to E}\ge 0,&(i)\end{matrix}$。
:::info
限制分別為第一階段的保密率不高於第二階段、無人機在一定範圍內、總功耗小於限制、個別功耗為正。
:::
這個問題的 (a) 和 (b) 不是凸問題，因此若要有效解出，必須將此問題轉換。
## Problem reformulation
展開 (a) 和 (b)，並利用輔助變數 $R_{0}$ 及 $r_{i}$，可將上面問題轉成以下凸問題:
$\begin{matrix}
\mathcal{P}_{1}:&\max_{u,p_{tr}^{U},p_{J,I}^{U\to E},p_{J,II}^{U\to E},r_{i},\forall i\in \{0,I\}}R_{0}&(a)\\
s.t.&\sum_{i=1}^{I}(B_{I,i}\log_{2}\big(1+\frac{p_{i}^{I}h_{i}^{I\to U}(u)}{|\tilde{h}_{LI,J}|p_{J,I}^{U\to E}+N_{0}^{U}}\big)-Br_{i})=R_{0},&(b)\\
&B_{S}\log_{2}\big(1+\frac{p_{tr}^{U}h^{U\to S}} {N_{0}^{S}}\big)-r_{0}\ge R_{0},&(c)\\
&R_{0}\ge 0&(d)\\
&B_{I,i}\log_{2}\Big(1+\frac{p_{i}^{I}h_{i,m}^{I\to E}}{N_{J,I,m}^{U\to E}+N_{0}^{E}}\Big)\le r_{i},r_{i}\ge 0,\forall m&(e)\\
&B_{S}\log_{2}\Big(1+\frac{p_{tr}^{U}h_{m,II}^{U\to E}(u)}{N_{J,II,m}^{U\to E}+N_{0}^{E}}\Big)\le r_{0},r_{0}\ge 0,\forall m&(f)\\
&x_{U}\in[0,X_{U,max}],&(g)\\
&y_{U}\in [0,Y_{U,max}],&(h)\\
&h_{U}\in [H_{min},H_{max}],&(i)\\
&p_{tr}^{U}+p_{J,I}^{U\to E}+p_{J,II}^{U\to E}\le P_{max},&(j)\\
&p_{tr}^{U}\ge 0,&(k)\\
&p_{J,I}^{U\to E}\ge 0,&(l)\\
&p_{J,II}^{U\to E}\ge 0,&(m)
\end{matrix}$
:::info
最大化總保密率 (a)
限制:
總保密率是第一階段保密率 (b)
第一階段保密率小於第二階段保密率 (c\)
保密率
第一階段對任意竊聽者而言干擾小於某個大於0的數字 (e)
第二階段對任意竊聽者而言干擾小於某個大於0的數字 (f)
水平座標在一定範圍之內 (g, h)
高度在一定高度區間 (i)
總功耗不大於一個指定數值 (j)
傳輸功耗、一階段干擾、二階段干擾功耗都大於 0 (k, l, m)
:::
# Proposed Scheme
<img src="https://github.com/kc71486/nsda_paper/raw/main/UAVPowerSecure/img/mainalgorithm.png" width="80%" alt="algorithm 1"><br>
## UAV Transmit Power Allocation Design
這部分的目標在給定座標及 $u,p_{J,I}^{U\to E},p_{J,II}^{U\to E}$ 的情況下最佳化 $p_{tr}^{U}$ ，以達到最大化保密率。此時 dghiklm 部分可省略，a 部分可減少參數。
另外，$|\tilde{h}_{LI,J}|p_{J,I}^{U\to E}$ 部份因為使用全雙工傳輸，此值小到可忽略，此時 b 部分稍微簡化。
接著這裡使用 SCA（successive convex approximation）將 f 展開，再用一階泰勒展開將 f 轉換成凸函數(下面沒改這部分)。
此時最佳化問題可以簡化成:
$\begin{matrix}
\mathcal{P}_{1-1}:&\max_{p_{tr}^{U},r_{i},\forall i\in \{0,I\}}R_{0}&(a1)\\
s.t.&\sum_{i=1}^{I}(B_{I,i}\log_{2}\big(1+\frac{p_{i}^{I}h_{i}^{I\to U}(u)}{N_{0}^{U}}\big)-Br_{i})=R_{0},&(b1)\\
&B_{S}\log_{2}\big(1+\frac{p_{tr}^{U}h^{U\to S}} {N_{0}^{S}}\big)-r_{0}\ge R_{0},&(c1)\\
&B_{I,i}\log_{2}\Big(1+\frac{p_{i}^{I}h_{i,m}^{I\to E}}{N_{J,I,m}^{U\to E}+N_{0}^{E}}\Big)\le r_{i},r_{i}\ge 0,\forall m&(e1)\\
&SCA\Big(B_{S}\log_{2}\Big(1+\frac{p_{tr}^{U}h_{m,II}^{U\to E}(u)}{N_{J,II,m}^{U\to E}+N_{0}^{E}}\Big)\le r_{0},r_{0}\ge 0,\forall m\Big)&(f1)\\
&p_{tr}^{U}+p_{J,I}^{U\to E}+p_{J,II}^{U\to E}\le P_{max},&(j1)
\end{matrix}$。
## Jamming Power Design at Phase I
這部分的目標在給定座標及 $u,p_{tr}^{U},p_{J,II}^{U\to E}$ 的情況下最佳化 $p_{J,I}^{U\to E}$。
這裡因為目標和限制已經是凸函數，因此不用額外更改。
此時最佳化問題可以簡化成:
$\begin{matrix}
\mathcal{P}_{1-2}:&\max_{u,p_{J,I}^{U\to E},r_{i},\forall i\in \{0,I\}}R_{0}&(a2)\\
s.t.&\sum_{i=1}^{I}(B_{I,i}\log_{2}\big(1+\frac{p_{i}^{I}h_{i}^{I\to U}(u)}{|\tilde{h}_{LI,J}|p_{J,I}^{U\to E}+N_{0}^{U}}\big)-Br_{i})=R_{0},&(b2)\\
&B_{S}\log_{2}\big(1+\frac{p_{tr}^{U}h^{U\to S}} {N_{0}^{S}}\big)-r_{0}\ge R_{0},&(c2)\\
&B_{I,i}\log_{2}\Big(1+\frac{p_{i}^{I}h_{i,m}^{I\to E}}{N_{J,I,m}^{U\to E}+N_{0}^{E}}\Big)\le r_{i},r_{i}\ge 0,\forall m&(e2)\\
&B_{S}\log_{2}\Big(1+\frac{p_{tr}^{U}h_{m,II}^{U\to E}(u)}{N_{J,II,m}^{U\to E}+N_{0}^{E}}\Big)\le r_{0},r_{0}\ge 0,\forall m&(f2)\\
&p_{tr}^{U}+p_{J,I}^{U\to E}+p_{J,II}^{U\to E}\le P_{max},&(j2)
\end{matrix}$
## Jamming Power Design at Phase II
若已經最佳化座標及 $p_{tr}^{U},p_{J,I}^{U\to E}$，則可以透過
$p_{tr}^{U}+p_{J,I}^{U\to E}+p_{J,II}^{U\to E}= P_{max}$ 最佳化 $p_{J,II}^{U\to E}$。
## UAV Altitude Design
給定 $[x_{u},y_{u}],p_{tr}^{U},p_{J,I}^{U\to E},,p_{J,II}^{U\to E}$ 的情況下，
(用到SCA)
$\begin{matrix}
\mathcal{P}_{1-4}:&\max_{h_{U},r_{i},\forall i\in \{0,I\}}R_{0}&(a4)\\
s.t.&h_{U}\in[H_{min},h_{max}]&(b4)\\
&B_{S}\log_{2}\big(1+\frac{p_{tr}^{U}h^{U\to S}} {N_{0}^{S}}\big)-r_{0}\ge R_{0},&(c4)\\
&...&\\
\end{matrix}$
## UAV Horizontal Position Design
(用到SCA)
## Main Algorithm
<img src="https://github.com/kc71486/nsda_paper/raw/main/UAVPowerSecure/img/Algorithm1.png" width="80%" alt="Algorithm 1"><br>
# Initialization settings
為了加速收斂，這裡使用低複雜度的初始化方法，這裡的初始化基本可以視為保密率下限。根據階段一與階段二的關係，可以分成下面三種情況:
1. 階段一保密率下限大於階段二保密率上限。這種情況下，本方法無解。
2. 階段一保密率上限大於階段二保密率下限。這種情況下，最佳解就是階段一保密率上限。
3. 階段一保密率和階段二保密率相交。這種情況下，最佳解在兩者相交區域內。

針對這三種情況，我們可以將初始化步驟分為下面三步:
### System Feasibility Solution in Case 1
給定第一階段干擾能量 $\epsilon_{p}$，獲得階段一保密率下限方法為:
$max_{l_{U}\in D_{U}}\left\|l_{U}-l_{i}\right\|^{2}$ 或 $min_{l_{U}\in D_{U}}max_{m\in M}\left\|l_{U}-l_{M}\right\|^{2}$。
:::info
達到階段一保密率下限需同時 (最大化無人機與設備間的距離) 和 (最小化無人機與最遠竊聽者間的距離)
:::
階段二保密率上限方法為:
$R_{s,U,E}^{U\to S}=\max_{u,p_{tr}^{U},p_{J,II}^{U\to E}}\log_2(\frac{1+p_{tr}^{U}h^{U\to S}/N_0^S}{1+\frac{p_{tr}^{U}k_{U,E,II}}{p_{J,II}^{U\to E}k_{U,E,II}+N_0^E(d_{U,m})^2}})$。
假設竊聽者 SNR 極高，則可發現階段二保密率上限幾乎只與 $p_{J,II}^{U\to E}$ 有關。
### UAV State Initialization in Case 2
階段一保密率上限需同時 (最小化無近機與設備間的距離) 和 (最小化無人機與最遠竊聽者間的距離)，此時 $p_{j,I}^{U\to E}=P_{max}^U-2\epsilon_p$ ， $\epsilon_p$ 為最小能量。
### UAV State Initialization in Case 3
#### Position Initialization Step
<img src="https://github.com/kc71486/nsda_paper/raw/main/UAVPowerSecure/img/positioninit.png" width="80%" alt="algorithm 2"><br>
給定能量分配的情況下，原先初始化位置問題為 NP-hard，因此需先做轉換。
演算法步驟如下:
1. 初始化三者能量為最小。
2. (a)(b) 將所有裝置集合成一個質心 $l_{\bar{i}}$，將所有竊聽者集合成一個質心 $l_{\bar{m}}$，並將這兩點連成一個直線。
3. (c\) 在這條直線上的點從頭到尾帶入保密率公式，找到 $R_{I,min,3}$、$R_{I,max,3}$、$R_{II,min,3}$、$R_{II,max,3}$。
4. 計算比例因子 k (k<1)，並利用最小位置、最大位置、根號 k 算出初始位置。
:::info
保密率幾乎與距離平方成正比，因此初始位置理想上會是第一階段與第二階段保密率交叉的地方。
:::
#### Power initialization step
<img src="https://github.com/kc71486/nsda_paper/raw/main/UAVPowerSecure/img/powerinit.png" width="80%" alt="algorithm 3"><br>
演算法步驟如下:
1. 由演算法二得到初始位置。
2. 將 $p_{J,I}^{U\to E}$ 由小到大測試，若階段一保密率小於階段二保密率，並且階段一保密率較前一組高，更新三個保密率($p_{J,I}^{U\to E}=\text{tested}$，其他補剩下的)。
:::info
對 $p_{J,I}^{U\to E}$ 而言，這樣的能量分配是最佳解。
:::
# Numerical results
# Conclusion
(作者提出)
未來可能的改進有: 動態無人機調度、串通的竊聽者等。
(我的想法)
1. 階段二使用全向天線，並且訊號強度大到低軌衛星也能接收，感覺階段二容易成為瓶頸。
2. 若階段二使用安全通道，就可以在不影響裝置的情況下，讓階段二安全性會大幅提升，若能上升到階段二永遠較階段一安全，本演算法複雜度也會下降不少。
3. 因為 CSI 使用直線距離計算，若裝置與無人機間有障礙物，可能會導致演算法失真，這或許可以透過限制最低高度解決。
4. 沒有考慮到多無人機協作，或許可以透過無人機間通訊解決。
5. 確切點出偏遠地區裝置遇到的問題，解法也使用最佳化理論，理論上較機器學習快速、精準且可重現。
6. 沒有 related work 比較對象，可能是因為這個方面 (偏遠地區、無人機、純硬體方面安全) 還沒有人做過。