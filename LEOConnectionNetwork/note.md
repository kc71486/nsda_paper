# User-Driven Flexible and Effective Link Connection Design for Mega-Constellation Satellite Networks

# Abstract

# Introduction
本篇做了以下貢獻:
 * 針對不平均分佈的使用者的衛星網路連接設計。該解法非常彈性，能套用在很多不同場景。
 * 提出拓樸結構與使用者分佈匹配程度的指標，來評估拓樸演算法的有效程度。
 * 根據真實衛星星鏈數據做模擬，傳輸跳數至少能改善 41%。
# Related work

# Prelimary

# Algorithm design
## Network model
### Timeslice
將連續時間拆成多個時間段。本篇假設兩個相鄰時間段之間的地對空拓樸結構相同。本篇將一個時間段訂為三分鐘，在這個時間段，地面會選擇與最近的衛星連接。
### Max ISL length
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/maxisl.png" width="60%" alt="max ISL length img"><br>
ISL:Inter-Satellite Link (衛星間的連結)
衛星間的連結主要受到兩者之間的"視線"影響，衛星間的通訊必須在熱氣層之上(水蒸氣會阻礙通訊)。因此，最長衛星間連結的距離為 5014 公里。
### Network model
衛星間的網路可以視為一個無向圖 $G=(V,E)$，其中 V 為衛星，E 微衛星中的連結。以 Starlink 為例，總共有 72 個公轉平面，每個公轉平面有 22 個衛星，因此 V=1584，由於物理上的限制，多數衛星最多只能搭載 4 個傳輸裝置，因此 $d(V)\le4$，並且 $E_{ij}\le5014$。
### Hops and stretch between ground stations
本模型的衡量標準為地對空相對傳輸距離及傳輸跳數。這裡先將地面與最近衛星對應，接著使用 Dijkstra 演算法。$h_{ij}$ 代表傳輸跳數，$d_{ij}$ 代表地對空相對傳輸距離。
## Problem formulation
$\begin{matrix}&min\sum_{i,j\in\wedge}(\alpha h_{i,j}+d_{i,j}))\\s.t.&d(v_i)\le 4\ (v_i\in V)\\&|e_{i,j}\le L|\ (e_{i,j}\in E)\end{matrix}$
$\alpha$ 是衡量距離和跳數重要度的參數，$\wedge$ 是流量對的集合。
## Complexity analysis
本文三個演算法複雜度都是 $O(TPN^2)$，T 為時間段數，P 為流量對，N為節點數，這個時間複雜度算很小了。
就算兩個衛星最大距離只有 2000km，一個衛星還是能與 40 個不同衛星連接。若要找出最佳解，這種問題是 NP-hard 的。
# User-driven networking algorithm design
## Ideal Networking for Satellite Network
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/insn.png" width="80%" alt="insn algorithm"><br>
理想衛星網路 (INSN) 能產生理想 (但不現實) 的網路，通常用作理論框架。
:::info
步驟基本上是:
1. 建立無向圖 $G_{dense}$，其中 V 為衛星，E 為所有可能連線，weight 為連線距離。
2. 建立無向圖 $G_i$，暫時只包含 V。
3. 用 Dijkstra 演算法找出各通訊對的最短路徑，並將該路徑放在 $G_i$。
4. 

該演算法每個時間段都計算一次，因此可能因狀態不同而產生震盪。
Dijkstra 演算法在衛星之間能導出較好結果原因是因為星鏈是位於一個曲面，同樣方向上距離較長的衛星會比距離較短的衛星整體距離短，如下圖。
:::
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/isldijkstra.png" width="50%" alt="isl dijkstra"><br>
:::warning
不知道對於一個通訊對，起點和終點衛星是否固定 (可能影響最佳化難度及結果)。
理論上 $G_i$ 有可能有衛星有大於 4 個連線，不知道演算法怎麼對應。
:::
## Dynamic Matching Networking for Satellite Network
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/dmnsn.png" width="80%" alt="dmnsn algorithm"><br>
動態配對衛星網路 (DMNSN) 是為了防止 INSN 在相鄰時間段的連結產生震盪。
:::info
步驟基本上是:
1. 建立無向圖 $G_{dense}$，其中 V 為衛星，E 為所有可能連線，沒有 weight。
2. 建立集合 $\{E'_N\}$，E 為所有可能連線，weight 為 0。
3. 建立無向圖 $G$，暫時只包含 V。
4. 對一個周期內的所有時間段: (進行5~6)。
5. 建立無向圖 $G_{minute}$，架構就是 $G_{dense}$加上 weight (連線距離)。
6. 用 Dijkstra 演算法找出各通訊對的最短路徑，並將該路徑的 weight 加進對應 $\{E'_N\}$ 內。
7. 由大到小排序 $\{E'_N\}$ 。依照順序進行: 若兩端連線數都小於4，則將該元素加入 $G$。

該演算法優勢是遵守連線數限制的情況下，關鍵連線不容易斷開 (關鍵連線重疊較多，下個時段有重疊的機率較高)。
:::
## Dynamic Matching Networking for Satellite Network
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/mcsn.png" width="80%" alt="mcsn algorithm"><br>
衛星網路匹配標準 (MCSN) 是衡量理想及現實拓樸結構差距的方法。
:::info
$k=\frac{1}{P}\sum_{j\in P}(\frac{count(p\cap G)}{len(p_i)})$
:::
:::warning
len 感覺怪怪的，單位不一樣，下面還能 0.7。應該是 count？
:::
# Performance evaluation
## setup
2 星鏈(starlink, kuiper)、3 架構(+Grid, motif, DMNSN)、2 組最大距離 (2000、3500)、2 情境 (住戶之間(100 主要城市)、基地之間(163 starlink station))、(?)，共 48 種不同情境，每種模擬一天，每個時間段 3 分鐘，共 480 時間段。
## link connection design comparison simulation
跟其他相比改善 41~59%。
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/comparison1.png" width="60%" alt="comparison1"><br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/comparison2.png" width="60%" alt="comparison2"><br>
# Conclusion
(我的想法)
 * 比較圖表中第三個項目沒意義。
 * 沒考慮到無人機頻寬上限 (ISL 之類)。