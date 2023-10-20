# Integrating LEO Satellite and UAV Relaying via Reinforcement Learning for Non-Terrestrial Networks

# Abstract
# Introduction
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOUAVRelay/img/fig1.png" width="80%" alt="fig 1"><br>
SAT = 低軌衛星，HAP = 高空平台 (UAV / drone，中文都稱為無人機)
本篇貢獻:
 * 在時變系統中同時最佳化低軌衛星分配及 HAP 動作控制來最大化長程通訊。本篇是第一個同時考慮兩系統的論文。
 * 使用 DQN 和動作降維技術，只考慮起點附近的低軌衛星。
 * 結果顯示平均資料傳輸率為不透過低軌衛星和 HAP 的 5.74 倍，不透過 HAP 的 3.99 倍。顯示低軌衛星和 HAP 的重要性。
# Related work
# System model
## Dynamics of HAP and SAT
考慮軌道上 22 個低軌衛星和軌道平面環境。假設固定飛行高度，只在 xy 平面上移動。本篇將連續時間 T 拆成多個時間段 N ($T=N\delta_t$)，其中同個時間段 HAP 位置假設固定。因此，HAP 的位置 $q_H(t)$ 及速度 $v_H(t)$ 函數可以變成向量形式 $q_H[t]$、$v_H[t]$、$a_H[t]$。
HAP 的變化函數為:
$\begin{matrix}v_H[n+1]=v_H[n]+a_H[n]\delta_t,&n=1,...,N\\q_H[n+1]=q_H[n]+v_H[n]\delta_t+\frac{1}{2}v_H[n]\delta_t^2,&n=1,...,N\end{matrix}$
而低軌衛星的變化函數為:
$q_L^i[n+1]=q_L^i[n]+v_L[n]\delta_t,n=1,...,N,\forall i$
## Multi-Hop Communication
### Channel model for RF link
假設通道為一個沒有都卜勒效應的直線連結。
通道增益 h 和距離 d 的關係為 $h_{rf}[n]=\sqrt{\frac{\beta_0}{d[n]^2}}$。
通道傳輸上限為 $C_{RF}[n]=B_{RF}\log_2\big(1+\frac{\gamma_0}{||d[n]||^2}\big)$ bps。
### Link configuration for vertical platform enabled-multi-hop communication
各通道的瞬時傳輸速度為:
$\begin{matrix}R_{S,i}\le C_{S,i},\forall n\\R_{i,k}\le\min\{C_{i,k}[n],Q_i+R_{S,i}\},\forall n\\R_{k,D}\le\min\{C_{k,D}[n],Q_k+R_{i,k}\},\forall n\end{matrix}$。
:::info
意思是各階段的瞬時傳輸速度不能大於通道傳輸速度上限或前一階段傳輸速度。
:::
其中 $Q_i$ 和 $Q_k$ 為 i 和 k 的緩衝區剩下的位元數。
帶入上面通道傳輸上限公式，$C$ 的部分可寫成:
$\begin{matrix}C_{S,i}[n]=\omega_{S,i}B_{RF}\log_2\big(1+\frac{\gamma_0}{||d_{S,i}[n]||^{\alpha}}\big)\\C_{i,k}[n]=\omega_{i,k}B_{RF}\log_2\big(1+\frac{\gamma_0}{||d_{i,k}[n]||^{\alpha}}\big)\\C_{k,D}[n]=B_{RF}\log_2\big(1+\frac{\gamma_0}{||d_{k,D}[n]||^{\alpha}}\big)\end{matrix}$。
其中 $\omega$ 為有沒有此通道 (0或1) ，因為起點一次只能連一個衛星且沒有衛星間通訊，$\omega_{S,i}$ 和 $\omega_{i,k}$ 的值會一樣。
# Proposed scheme
## Problem formulation
W 為地面與無人機的連接，A 為無人機加速度。
$\begin{matrix}P1:&\max_{W,A}&\sum_{n=1}^NR_{S,D}[n]\\&s.t.&v_H[n+1]=v_H[n]+a_H[n]\delta_t,&(a)\\&&q_H[n+1]=q_H[n]+v_H[n]\delta_t+\frac{1}{2}v_H[n]\delta_t^2&(b)\\&&q_L^i[n+1]=q_L^i[n]+v_L[n]\delta_t&(c)\\&&R_{S,i}\le C_{S,i}&(d)\\&&R_{i,k}\le\min\{C_{i,k}[n],Q_i+R_{S,i}\}&(e)\\&&R_{k,D}\le\min\{C_{k,D}[n],Q_k+R_{i,k}\}&(f)\\&&||a_H[n]||\le A_{max}&(g)\\&&\omega_{S,i}[n]\in\{0,1\}&(h)\\&&\sum_{i\in I}\omega_{S,i}[n]\le 1&(i)\end{matrix}$
:::info
講白話就是:
調整最大化傳輸率，限制:
無人機和低軌衛星遵守速度公式 (低軌衛星速度固定)
各階段的瞬時傳輸速度不能大於通道傳輸速度上限或前一階段傳輸速度。
無人機的加速度有上限。
起點一次只能和一個低軌衛星通訊。
:::
:::warning
無人機的速度沒上限？
不用調整無人機初始位置？
:::
## MDP Modeling for Deep Reinforcement Learning
最大化問題中的 (a)(b) 符合馬可夫性質，因此我們可以把此問題變成馬可夫決策過程，再將其轉成深度強化學習。
1. 環境
   環境包括任何太空/空域連結 (ISL)。HAP 是本模型的智慧體。本模型為一個中央化架構，智慧體能觀察全域狀態。
   :::warning
   看不出有 ISL 就是了，除非指的不是衛星間連結。
   :::
2. 行為
3. 獎勵
## Proposed Algorithm with DQN
# Numerical evaluation
環境在一個三維空間
# Conclusion
(我的想法)
 * 沒用到 ISL，這個無法應用於洲際連線。
 * 只考慮一起點對一終點，多起點對多終點沒被考慮到，考慮進去的話傳輸率會受影響。
 * 只有終點能連接無人機，可能代表使用情境是周邊基地台對偏遠地區。
 * 實際上因為無人機移動速度遠低於低軌衛星，HAP 的移動對整體傳輸速度的幫助有限 (感覺初始位置比較重要)，感覺光是就只是多一個中繼節點就讓資料傳輸率大幅提升。
 * 因為中繼節點的存在就讓資料傳輸率大幅提升，加入高度作為可變相可能可以進一步改善。
 * 使用 DQN 真的是最好的方法嗎？
(本篇想法)
 * 本篇同時考慮低軌衛星和無人機來最大化點對點傳輸速度。
 * 將一個一起點對一終點變成多起點對多終點會是未來的一項有趣研究。
 * 若要考慮多起點對多終點，可能要將模型升級為 actor-critic 或 多智能體強化學習 (MARL)