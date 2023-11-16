# A Triple Real-Time Trajectory Privacy Protection Mechanism Based on Edge Computing and Blockchain in Mobile Crowdsourcing

# Abstract
# Introduction
# Related work
# Analysis of algorithms
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/fig1.png" width="80%" alt="fig 1"><br>
## Trajectory location privacy protection framework
整個流程可分成 7 步:
1. 請求者上傳任務至邊緣伺服器，邊緣伺服器再上傳至區塊鏈。
2. 區塊鏈上的任務被廣播到區塊鏈的各個角落。
3. 參與者從邊緣伺服器選擇要執行的任務。
4. 參與者利用區域差分隱私 (local differential privacy) 干擾軌跡上的特定點，接著使用多重機率擴展機制來擴展軌跡的機率，最後邊緣伺服器將不同參與者的軌跡分成多的不相關的子軌跡。
5. 參與者將結果透過邊緣伺服器上傳至區塊鏈。
6. 請求者經由邊緣伺服器獲得計算結果。
7. 請求者根據結果給予參與者獎勵。
<!--  -->
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/fig2.png" width="80%" alt="fig 2"><br>
上圖為第 3~5 步流程展示。原始軌跡 $T$ 被拆成多個片段並進行干擾及延伸成 $T_{pse_j}$，原始參與者 $u_i$ 也變成假名 $pse_j$。就像上圖，A 被拆成a 和 b，B 被拆成 c 和 d。
## Thread model
### External attacker attack model
參與者上傳結果後，外部攻擊者可能中途攔截上傳的結果，或從結果推斷參與者個人資訊，導致參與者隱私外洩。
### Third-party crowdsourcing platform attack model
群眾外包平台握有所有參與者上傳的結果，如果這些資料沒被保護，可能導致參與者隱私外洩。
### Other third-party attack model
做群眾外包時，部分參與者可能要依賴其他第三方程式完成任務，但是這會讓第三方程式得到結果，可能導致參與者隱私外洩。
## The first privacy protectiion: terminal task participants
### Local differential privacy technology
參與者上傳至群眾外包平台前會先做隱私方面處理。
> 局部差分隱私為:
> $\frac{Pr[M(t)=t^*]}{Pr[M(t')=t^*]}\le e^{\epsilon}$，其中資料集內不同的資料點。
<!--  -->
相較一般差分隱私:
$\frac{Pr[M(D)=t^*]}{Pr[M(D')=t^*]}\le e^{\epsilon}$，其中 D 和 D' 為相差 1 元素的資料集。
兩者不同的地方是觀點不同 (資料點與資料集)。<br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/local_DP.png" width="80%" alt="local differential privacy image"><br>
局部差分隱私可以保證就連外包平台也不知道個別用戶個資。<br>
$P(l_i|l_{i'})=\left\{\begin{array}{c}\frac{e^{\epsilon}}{h-1+e^{\epsilon}}&l_{i}'=l_i\\\frac{1}{h-1+e^{\epsilon}}&l_{i}'\ne l_i\end{array}\right.$
### Gaussian distribution
就算局部差分隱私能保護目前位置，整體軌跡仍然無法被保護，因此需要額外保護。
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/fig3.png" width="80%" alt="fig3"><br>
$d$ 為兩個時間段之間移動距離 (需要速度 $v$)，
$\eta=[(1-0.1*k)*d, (1+0.1*k)*d]$ (需要位置靈敏度 $k$)。
$\mu'=\mu+\beta$<br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/alg1.png" width="80%" alt="algorithm 1"><br>
:::info
大致流程為:根據速度決定 $\eta$ 範圍，選出至多 $\delta$ 個符合：(在邊緣伺服器附近、位置合理、在 $\eta$ 範圍內) 的點 (若有不符合的就會跳過)，最後從這些點中選出其中一個。
:::
## The second protection: edge server
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/alg2.png" width="80%" alt="algorithm 2"><br>
參與者上傳位置，若參與者位於邊緣伺服器 $E_x$ 範圍內，$E_x$ 配發一個沒用過的假名。
得到假名後，參與者就用該假名上傳資料，當參與者離開伺服器 $E_x$ 範圍後就釋出該假名供邊緣伺服器再利用。
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/alg3.png" width="80%" alt="algorithm 3"><br>
## The third protection: cloudsourcing platform
### blockchain introduction
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/fig4.png" width="80%" alt="fig 4"><br>
這裡的架構與其他區塊鏈相同。
header 包含版本號、前一個區塊的雜湊值、時間、計算難度、nonce、merkle tree root。
body 包含整個 merkle tree。
這邊 merkle tree root 所使用的雜湊函數為 $SHA256(SHA256(data))$。
### merkle tree
merkle tree 資料結構如下:
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/merkle_ds.png" width="80%" alt="algorithm 4"><br>
可以想成一個一般的二元樹加上 hash 作為下游節點的驗證，最上面的hash 值同時也會放在 header 中。有些實作不會把 hash 包括進 node 中 (例如區塊鏈)。<br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/alg4.png" width="80%" alt="algorithm 4"><br>
此部分為 merkle tree 演算法，跟其他區塊鏈相同。步驟: 將所有片段分別 hash 後，以二合一的方式逐漸組合，如果只有一個，那就那一個當兩個，最後得到的一個 hash 就是 merkle root。<br>
### advantages
使用區塊鏈的好處:
1. 快速廣播。請求者將任務丟到區塊鏈上，一旦被認可，就會以非常快的速度廣播到整個區塊鏈上，有問題的話則會直接被丟掉。
2. 資料以 merkle tree 的方式儲存在區塊鏈上，可達到快速查找 ($O(log_2(N))$，因為是二元搜尋數)、高擴充性(本方法不儲存 hash值，額外儲存空間很少)。
3. 上傳的資料只需要新增或查找，而不用刪除或修改，上傳之後也不需要怕被竄改。
4. 節點本身是匿名的。
# Experiments and result analysis
## Experiment settings
實驗使用六個城市 (紐約、北京、上海、成都、南京、西安) 的計程車資料集，這些資料集包括計程車 id、時間、經緯度。本實驗在這些資料集中，隨機選擇其中 100 計程車資料，這些資料為同時間段下 20 到 60 連續位置軌跡。
## Evaluation indicators and analysis
### Data quality loss
資料品質下降 (誤差) 指標為：
$DQL(T-LGEB)=\sum_{i_1}^{t_1}d(l_{i1},l_{i1}')+\sum_{i_2}^{t_2}d(l_{i2},ldi_2)$。
$d(l_{i1},l_{i1}')$ 為干擾前後位置之間的距離，
$d(l_{i2},ldi_2)$ 為延伸前後位置之間的距離。<br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/eva_quality.png" width="80%" alt=""><br>
誤差隨軌跡數量上升的原因誤差是隨軌跡而累加的。
本方法贏過所有其他方法。
### Privacy protection capability
保密能力指標為：
$\left\{\begin{array}P_L=a\cross p_{oa}+b\cross p_{plt}+c\cross p_{ot}\\a+b+c=1\\p_{oa}=\frac{q\cross S_{name}+e\cross S_{num}+f\cross S_{loc}+g\cross S_{dir}}{k}\\q+e+f+g=1\end{array}\right.$。
:::warning
reference 的 \[66\] 無法觀看，所在期刊 impact factor 為 1.9。
reference 的 \[67\] 找不到來源。
:::
$p_{oa}$ 為外部攻擊隱私洩漏的機率，
$p_{plt}$ 為群眾外包平台洩漏的機率，只有 1(會) 和 0(不會)，
$p_{ot}$ 為其他第三方平台洩漏的機率，只有 1(會) 和 0(不會)，
$S_{name}$ 為路徑和身分的相關性，只有 1(相關) 和 0(不相關)，
$S_{num}$ 為軌跡中上傳資料點數量和實際資料點數量的比值，
$S_{loc}$ 為上傳位置和實際位置的相似程度，0 為完全不同，
$S_{dir}$ 為上傳資料點方向變化和實際方向變化的相似程度，0 為完全一致，
$k$ 為上傳軌跡數量。
本篇設定 $a=0.6,b=0.2,c=0.2$。<br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/eva_privacy.png" width="80%" alt=""><br>
贏的原因有: 不依賴其他平台、使用動態假名、拆成多個子軌跡。
### Storage consumption
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/eva_storage.png" width="80%" alt=""><br>
### Algorithm running time
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/eva_time.png" width="80%" alt=""><br>
有使用到邊緣運算，因此較快。
### The influence of algorithm parameter N
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/impact_quality.png" width="80%" alt=""><br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/impact_privacy.png" width="80%" alt=""><br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/impact_storage.png" width="80%" alt=""><br>
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyEdgeBlockchain/img/impact_time.png" width="80%" alt=""><br>
# Conclusion
我的想法:
1. 保護隱私和資料完整度方面都遠勝其他。
3. 本方法泛化程度待考證，不確定對於與位置無關的資料集是否也有如此表現。
# Reference
 * [local differential privacy vs global differential privacy](https://blog.openmined.org/basics-local-differential-privacy-vs-global-differential-privacy/)