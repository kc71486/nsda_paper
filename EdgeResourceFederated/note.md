# Resource Allocation in IoT Edge Computing via Concurrent Federated Reinforcement Learning

# Abstract
# Introduction
對於 IoT 相關的運算，主流的方法有用中央和用邊緣兩種。中央的方法用一個全知的中央進行運算，但是會造成隱私問題，邊緣的方法沒有隱私問題，但是整體效率通常不是很好。
結合聯邦學習與深度強化學習能圖時解決高隱私及高複雜度問題，然而目前聯邦式強化學習必須將本地模型上傳至中央，一篇論文顯示惡意人士能透過模型梯度還原出輸入資料，因此能接觸到中央模型的人都能竊取資料。
:::info
論文為 Inverting gradients—How easy is it to break privacy in federated learning?
:::
本方法邊緣只分享輸出結果，因此中央或其他邊緣都不知道輸入資料或參數。中央也只分攤與該邊緣相關的獎勵，因此中央或其他邊緣都不知道各自的策略。
本方法做出以下貢獻:
1. 第一個同時考慮邊緣與中央隱私的資源分配方法。
2. 提出同步聯邦學習的概念，邊緣與中央只分享輸出及獎勵，不分享各自模型。
3. 結果顯示表現優異。

# Related work
# Preliminaries
## reinforment learning
在每一步，程式觀察狀態 $s\in S$，並根據轉換函數 $Q_{\pi}(s,a)$ 從 $s$ 選擇行為 $a\in A$，轉成 $s'$ 及獎勵 $r$，程式再透過 $s'$ 及 $r$ 更新 $Q_{\pi}$。
$Q_{\pi}$ 可表示成 $Q_{\pi}(s,a)=E[\sum_{t=1}^{\infty}(\gamma\cdot r(s,a))]$。$E$ 為期望值。
$Q_{new}(s_t,a_t)=(1-\alpha)\cdot Q(s_t,a_t)+\alpha\cdot(r(s_t,a_t)+\gamma\max_{a'}Q(s_{t+1},a_t))$
## federated learning
## federated reinforment learning
# Proposed scheme
## overview
一伺服器對多邊緣節點，每個邊緣節點都有服務一些 IoT 設備，這些設備會固定上傳任務。這些任務會被儲存在一個佇列等待被執行。這些邊緣節點和中央伺服器共同合作監管資源分配，決定是否分配到伺服器上執行。這裡的目標是盡快完成任務。
## learning process
每輪有三階段:
 1. 建立策略: 每個邊緣產生一個自己的資源管理策略並傳給中央，中央再產生整體資源分配策略。這兩邊都是基於深度強化學習的策略。
 2. 執行策略: 邊緣根據策略分配資源給自己或丟給中央。若本地任務成功被執行，邊緣獲得獎勵。若中央成功執行，中央獲得獎勵。
 3. 模型更新: 中央和邊緣根據獎勵更新模型。
## state
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/fig3.png" width="60%" alt="states"><br>
各邊緣的狀態 $s$ 可表示為 $s=\{C,\tau\}$，$C$ 為資源分配狀況 ({0,1}矩陣，資源 $c$ 和時間段 $t$ )，$\tau$ 為各任務資源用量 (陣列)
## action
### action of edge
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/fig4.png" width="60%" alt="action of edge"><br>
各邊緣的狀態 $a$ 可表示為 $a_{\tau i}=\{c_{\tau i},t_{\tau i}\}$。
假設任務 $\tau_i$ 需要 6 資源，其中一種可能的狀態為 $\{3,1\}$，代表在時間 1 上的最小座標分配分配 3 資源，並往時間 2 延伸 ( $6/3=2$ )。
若不分配資源，則狀態為 $\{0,0\}$，代表丟給伺服器。
:::info
一次只會分配一個任務。
:::
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/action1.png" width="50%" alt="action 1"><br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/action2.png" width="50%" alt="action 2"><br>
### action of server
中央的狀態 $a_s=\{d_1,...,d_{n_e}\}$，$n_e$ 為邊緣數量，其中這些總和不能高於伺服器資源上限 $n_r$。
假設 $n_e=2,n_r=10$，其中一種可能的狀態為 $\{3,7\}$，代表用 3 資源處理邊緣 1，用 7 資源處理邊緣 2。
## reward
邊緣的獎勵為
$r_e=\left\{\begin{array}{l}u^{\tau_i}-(t^{\tau_i}+\frac{\tau_i}{c^{\tau_i}})&\text{if allocated successully}\\-1&\text{if allocated unsuccessully}\\u^{\tau_i}-t_0&\text{if sent to the server.}\end{array}\right.$。
$u^{\tau_i}$ 為任務的功能性，$(t^{\tau_i}+\frac{\tau_i}{c^{\tau_i}})$ 為任務的停留時間，$t_0$ 為送到中央的時間。
中央的獎勵為
$r_s=\sum_{i=1}^{n_e}((u^{\tau_i}-\frac{\tau_i}{d_i})-d_i)$。
$u^{\tau_i}$ 為任務的功能性，$\tau_i$ 該時間段邊緣丟給中央的任務所需資源，$d_i$ 為中央分配給該邊緣的任務量。
:::info
因為一個時間段只會丟一個任務，$u^{\tau_i}$ 和 $\tau_i$ 只會是有或沒有。
:::
## algorithms
<style>
    .img_area {
        display: flex;
        flex-direction: row;
        width: 100%;
    }
    .img_area img {
        object-fit: contain;
        width: 50%;
    }
</style>
<span class="img_area"><img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/alg1.png" alt="edge algorithm">
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/alg2.png" alt="server algorithm"><br><br></span>
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/alg_both.png" width=100% alt="algorithm flow chart"><br>
邊緣傳給中央目前狀態 (但是不傳權重)，使中央擁有所有邊緣狀態。
中央傳給邊緣的獎勵為 $r_s=u^{\tau_i}-\frac{\tau_i}{d_i}-d_i$。其中 $d$ 為中央資源，$\tau$ 為上傳任務的總資源。
:::info
這獎勵的意義是地方必須適度丟任務給中央 (利用任務獎勵)，但是不能過度 (中央處理時間過長)。
沒空間時有兩種方案:
留著: $-1-d$，虧。
丟給中央: $2u-t-\frac{\tau}{d}-d$，根據丟給中央的總工作量，可能賺或虧。
:::
:::warning
中央不會有前一個時間段沒做完導致這個時間段不夠接收的問題嗎?考慮到前面也沒說中央有自己的狀態，可能是當作中央能馬上做完。
:::
# Theoretical analysis
## utility analysis
邊緣使用到獎勵塑造 (reward shaping) 技術。
:::info
獎勵塑造是透過額外獎勵引導學習。
其中一個例子是facebook訓練玩德軍總部的測試，殺敵人會得到positive reward，被殺則是negative reward，而額外獎勵是只要存活就會扣分，希望訓練機器可以主動殺敵人，而不是躲避。
:::
一般的獎勵函數會是 $F(s,s')=\gamma\Phi(s')-\Phi(s)$，
但是獎勵必須根據邊緣與中央的行為，因此本文的獎勵函數為
$\hat{F}(a_i^t,a_s^t,a_i^{t+1},a_s^{t+1})=\gamma(\Phi(a_s^{t+1})+\Phi(a_i^{t+1}))-(\Phi(a_s^t)+\Phi(a_i^t))$。
$a_s^t$ 代表伺服器行為，$a_i^t$ 代表邊緣 i 行為。
## privacy analysis
原本聯邦學習方法因為原始數據可以由參數的更新推得，因此不安全。
本方法因為只給結果，不會有參數更新推斷的問題，但是如果洩漏模型就會從結果推得。
本方法每個邊緣模型各自獨立，本來就沒必要洩漏模型。
# Experiments
## setup
單個時間給邊緣的任務數為平均 $\lambda$ 的 poisson 分布，每個任務需要 $\tau$ 資源，為在一定區間的均勻分布。
兩個比較對象，DRL-based 和 IP-based。
:::info
DRL-based 為 Resource allocation based on deep reinforcement learning in IoT edge computing。
IP-based 為 Resource allocation strategy of edge systems based on task priority and an optimal integer linear programming algorithm。
IP-based 的只有兩個 citation。
:::
四種評估指標:
1. 平均功能性: 各邊緣的獎勵。
2. 平均時間: 平均完成任務花費的時間。
3. 平均資源使用率: 邊緣分配成功的資源與邊緣分配的資源的比值。
4. 平均資料傳輸量: 邊緣傳給中央的平均任務數。
:::info
實際上除了任務，還要傳輸邊緣狀態及中央獎勵，但是平均資料傳輸量都沒算到。
:::
## results
邊緣有 10 資源，中央有 30 資源，邊緣滑窗時間包含 5 時間段 (時間內共可用 50 資源)。<br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/result1.png" alt="result 1"><br>
固定一個邊緣需處理的任務數 8，任務需求 \[3,5\]，改變邊緣數 (2~4)。
左三，邊緣數越多資源利用率更高，因為整體可運用資源更多，利用彈性也更大。
:::warning
但是邊緣相對的資源不變，中央相對的資源變少。
:::
左四，邊緣數不影響傳輸成本，因為邊緣不會互相影響，因此邊緣決定是否傳送與節點數無關。
<br><img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/result2.png" alt="result 2"><br>
固定邊緣數 3，任務需求 \[3,5\]，改變任務數 (6~14)。
:::warning
大幅下降應該是因為邊緣容量到臨界點，必須做出抉擇。
:::
<br><img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/result3.png" alt="result 3"><br>
固定邊緣數 3，任務數 8，改變任務需求 (\[3,5\], \[4,6\], \[5,7\])。
左一，平均任務需求越高差距越大，這是因為需求越高，對資源的競爭也越激烈。CFRL 主要受益整體，而 DRL 環境非常競爭 (適者生存類型)。
:::warning
平均任務需求越高總功能性和資源利用率都越高，不知道為何。
我猜是因為功能性和資源需求成正比。
:::
在任何情況下，本方法都比 DRL 有更高資源利用率、時間更短、通訊成本更低。這可能是因為 DRL 只考慮自身，不考慮整體。
<br><img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/result4.png" alt="result 4"><br>
<br><img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/result5.png" alt="result 5"><br>
<br><img src="https://github.com/kc71486/nsda_paper/raw/main/EdgeResourceFederated/img/result6.png" alt="result 6"><br>
訓練初期，CFRL 時間極長，這是因為每個節點擁有知識不多，單個節點行為可能對其他節點產生負面影響。DRL 因為各自獨立，所以沒啥負面影響。
訓練初期，CFRL 利用率較高，這是因為前期邊緣都在試水溫，沒分配很多資源，也因此時間較長。
:::info
看獎勵函數，沒伺服器給的獎勵塑造的情況下，處理不了一定直接往伺服器丟，而伺服器一定能完成工作，只是時間長短問題。
:::
# Conclusion
 * 用新方法做 federated learning，結果看起來不錯，但是感覺過於中央。
 * 感覺中央沒狀態是有問題的
# reference
 * [獎勵塑造](https://hackmd.io/@shaoeChen/Bywb8YLKS/https%3A%2F%2Fhackmd.io%2F%40shaoeChen%2FrktZw3xhH)
