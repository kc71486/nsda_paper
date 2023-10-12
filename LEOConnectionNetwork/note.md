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
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOConnectionNetwork/img/isldijkstra.png" width="50%" alt="max ISL length img"><br>
## Ideal Networking for Satellite Network
理想衛星網路 (INSN)
# Performance evaluation

# Conclusion