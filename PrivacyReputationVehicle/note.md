# A Lightweight Privacy Preservation Scheme With Efficient Reputation Management for Mobile Crowdsensing in Vehicular Networks

# Abstract

# Introduction

# Related work

# Prelimary
## Hadamard Product
矩陣 $A,B\in \mathbb{C}^{m\times n}$ 且 $A=[a_{ij}]$, $B=[b_{ij}]$，則 $A\circ B$ (Hadamard Product，逐項乘積) 為:<br>
$\begin{bmatrix}a_{11}b_{11} & a_{12}b_{12} & ... & a_{1n}b_{1n} \\a_{21}b_{21} & a_{22}b_{22} & ... & a_{2n}b_{2n} \\\vdots  & \vdots  & \ddots  & \vdots  \\a_{m1}b_{m1} & a_{m2}b_{m2} & ... & a_{mn}b_{mn}\end{bmatrix}$
## DLP
離散對數 (DLP)。循環集 $\mathbb{G}$ 中，給定質數 a, b，找到一個整數 k 使得 $b^{k}=a$，我們目前還沒有找到多項式時間能計算它。
## DDH
Diffie-Hellman (DDH)，循環集 $\mathbb{G}$ 中，給定 $g$, $g^{x}$, $g^{y}$ (x, y 為整數)，我們難以使用隨機生成的 z，使得 $g^{z}=g^{ab}$ 並再區分出 a、b。
## Beta Distribution
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/beta_distrib.png" width="50%" alt="plots of several beta distributions"> <br>
Beta Distribution 的公式如下:
$f(x|a,b)=\frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)}x^{\alpha-1}(1-x)^{\beta-1}$，
其中 $0\le x\le 1,\alpha>0,\beta>0$。
該分布 $\alpha$ 越大，圖形偏左邊， $\beta$ 越大，圖形偏右邊，兩者同時變大時圖形偏中間，同時為1時為平均分布。
該分布的期望值 $E(x)=\frac{\alpha}{\alpha+\beta}$。
## Paillier Algorithm
# Problem Statement
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/scheme.png" width="100%" alt="scheme(Fig 2)"> <br>
## System Architecture
本系統包含以下5種不同單位:
 * data requester
   數據請求者: 數據請求者可能是管理部門或服務部門，甚至是被授權的個人。它分配感測任務來獲取感測數據或有價值的信息，例如交通資訊、空氣質量指數值等。
 * cloud server
   雲端伺服器: 雲端伺服器具有強大的存儲和計算能力，主要負責密文的收集和處理。此外，它還評估感測數據並向名聲中心發送名聲反饋報告。
 * reputation center
   名聲中心: 名聲中心負責驗證、存儲、分發和更新每個感測車輛的名聲值和假名。
 * roadside unit (RSU)
   RSU: RSU作為雲端伺服器與感測車輛，或名聲中心與感測車輛之間的橋樑，部署在路邊，主要負責數據傳輸。
 * sensing vehicles
   感測車輛: 這裡假設傳感車輛配備有多種可以感測數據的感測裝置。此外，每個傳感車輛都擁有一個可信平台模組（trusted platform module, TPM），用以保存私鑰、位置和其他秘密資訊。

如Fig 2，本系統包含以下幾步:
1. 分配任務。數據請求者將感測任務內容、位置和名聲要求分配給雲端伺服器。接著雲端伺服器透過RSU向感測車輛廣播感測任務。
2. 募集車輛。每個願意感測數據的感測車輛透過RSU將其位置和名聲值發送到雲端伺服器，然後雲端伺服器檢查位置和名聲值是否滿足感測任務的位置和名聲要求。
3. 選擇車輛。雲端伺服器選擇位置和聲譽值滿足位置和聲譽要求的感測車輛。
4. 提交資料。感測車輛經由RSU將感測數據提交至雲端伺服器。
5. 處理資料。雲端伺服器收集並整合感測數據。
6. 回傳結果。雲端伺服器將感測結果回傳給數據請求者。
7. 發送名聲回饋報告。雲端伺服器生成感測車輛的名聲反饋報告並將這些報告發送至名聲中心。
8. 更新名聲值和化名。名聲中心根據名聲回饋報告更新每台感測車輛的名聲值，同時也會更新每輛車的化名。
## Security Model
來自各方可能的攻擊:
名聲中心: 完全信任，無串通；
數據請求者、雲端伺服器、RSU: Honest-but-Curious
 * inference attack (推理攻擊)
 * location inference attack (推理位置)
 * identity inference attack (推理真實身分)

感測車輛: 多數誠實，少數惡意
 * data pollution attack (數據污染攻擊): 提供錯誤感測數據
 * Sybil attack (女巫攻擊): 利用不同的假名多次提交相同的感測數據
 * data replacement attack (數據替換攻擊): 用不正確的感測數據替換正確的感測數據以通過數據評估
 * reputation tamper attack (名聲篡改攻擊): 篡改自己的名聲值來滿足名聲要求

## Design Goals
### Privacy Goals
 * 位置隱私
 * 身份隱私
 * 感測數據隱私
 * 名聲值隱私
### Security Goals
# Design of proposed scheme
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/notation.png" width="80%" alt="scheme(Fig 2)"> <br>
## Overview
## Basic Algorithm Construction
### RVEV Algorithm
來源:"Efficient proofs that a committed number lies in an interval"。
一個只提供一方加密後數值，判斷加密前的兩數是否相同的演算法。
Setup:
一個模數為 p($p\in$ 質數) 的循環群，g、h 為 generator。p、g、h 為公共參數。
t 代表名聲中心的內名聲值，t' 代表車輛上交的名聲值(可能不合法)。
Commitment:
感測車輛傳送 $Cm'_{vi}=g^{t'}\cdot h^{r'}\text{ mod }p$，其中 r 為隨機數。
Challenge:
雲端伺服器將 $Cm'_{vi}$ 送至名聲中心。
Response:
名聲中心隨機選擇 $\omega$、$\eta$、$\eta'$，並計算:
$Cm_{1}=g^{\omega}\cdot h^{\eta}\text{ mod }p$、
$Cm_{2}=g^{\omega}\cdot h^{\eta'}\text{ mod }p$，
$H=Hash(Cm_{1}||Cm_{2})$。再計算:
$D=\omega+H\cdot t'$，
$D_{1}=\eta+H\cdot r'$，
$D_{2}=\eta'+H\cdot r$，
最後名聲中心將 \{$H,D,D_{1},D_{2},Cm_{vi}$\} 送至雲端伺服器。
Verification:
雲端伺服器檢查
$H=Hash(g^{D}h^{D1}(Cm'_{vi})^{-H}||g^{D}h^{D2}(Cm_{vi})^{-H})\text{ mod }p$
是否成立。若成立，伺服器相信加密前兩個名聲值相同。
:::info
整體式子化簡後變成(忽略 mod p):
$1||1 == g^{H\cdot t'}h^{H\cdot r'}(Cm'_{vi})^{-H}||g^{H\cdot t'}h^{H\cdot r}(Cm_{vi})^{-H}$，
若 $Cm'_{vi}=g^{t'}\cdot h^{r'}$，右邊可正負對消變成 $1||1$。
:::
### RVRP Algorithm
來源:"PACE: Privacy-preserving and quality-aware incentive mechanism for mobile crowdsensing"。
一個判斷指定名聲是否在指定區間的演算法
$t$ 代表車輛的名聲值，$t_{0}$ 代表名聲值下限，$b$代表名聲值上限。
若 $t_{0}\le t\le b$，t 位於指定名聲區間。
Setup:
一個模數為 p($p\in$ 質數) 的循環群，g、h 為 generator。p、g、h 為公共參數。
Commitment:
伺服器計算 $Cm_{r}=g^{(t-t_{0})}\cdot h^{(r-r_{0})}\text{ mod }p$，其中 $r$、$r_{0}$ 為隨機數。
Challenge:
雲端伺服器將 $C_{t_{0}}$, $C_{r_{0}}$, $b$ 送至名聲中心，其中 $C_{t_{0}}$、$C_{r_{0}}$。為使用名聲中心公鑰加密 $t_{0}$、$r_{0}$ 的密文。
Response:
名聲中心解密 $C_{t_{0}}$、$C_{r_{0}}$ 並判斷 $t_{0}\le t\le b$ 是否成立，若不成立，則回傳 $\bot$。若成立，則計算
$Cm_{v1}=g^{|b-(t-t_{0})|}\cdot h^{-(r-r_{0})}\text{ mod }p$、
$Cm_{v2}=g^{|t-t_{0}|}\cdot h^{(r-r_{0})}\text{ mod }p$，
最後名聲中心將 $Cm_{v1}$、$Cm_{v2}$ 送至雲端伺服器。
Verification:
雲端伺服器檢查
$\left\{ \begin{array}{l} Cm_{v1}=g^{b}\cdot(Cm_{r})^{-1}\text{ mod }p\\Cm_{v2}=Cm_{r}\text{ mod }p\end{array}\right.$
是否成立。若成立，伺服器相信名聲值 t 位於指定名聲區間。

### TFRU Algorithm
來源:"BTRES: Beta-based trust and reputation evaluation system for wireless sensor networks"。
一個根據名聲報告更新名聲值的演算法，更新 beta distribution 參數而不是單純線性更新。
s 代表正回饋，u 代表負回饋，
Rf 代表 forgetting factor，
Rg 代表 group weight。
演算法如下:
$\left\{\begin{array}{l}\alpha_{Rn}=Rf\cdot\alpha_{Rn-1}+Rg\cdot s_{Rn}\\\beta_{Rn}=Rf\cdot\beta_{Rn-1}+(1-Rg)\cdot u_{Rn}\\t=E(x)=\frac{\alpha_{Rn}}{\alpha_{Rn}+\beta_{Rn}}\end{array}\right.$。
## Detail Design
數據請求者擁有 $(Pk_{d_{a}}, Sk_{d_{a}})$ 及 $(Pk_{d_{h}}, Sk_{d_{h}})$，其中後者支援同態加密。
名聲中心擁有 $(Pk_{r}, Sk_{r})$。
名聲中心計算暱稱 $P_{vid}=g^{vid}\oplus R_{vi}\text{ mod }p$，其中 $vid$ 為真實身分，$R_{vi}$ 為隨機數。
名聲中心加密名聲 $Cm_{vi}=g^{t}\cdot h^{r}\text{ mod }p$。
名聲中心透過安全管道將 $\{P_{vid},Cm_{vi}\}$ 傳給各感測車輛。
### Assign tasks
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/locationmatrixtask.png" width="80%" alt="location matrix for task (Fig 3)"> <br>
#### Define standard value
數據請求者定義:
感測內容 $m$，加密 $m\longrightarrow C_{m}$；
序列號 $T$；
標準值 $d_{0}$，選擇 $\varepsilon_{d0}$ ，加密 $C_{d0}=g^{-d0}\cdot\varepsilon_{d0}^{n}\text{ mod }n^{2}$；
誤差閾值 $\Delta d$，選擇 $\varepsilon_{\Delta d}$ ，加密 $C_{\Delta d}=g^{-\Delta d}\cdot\varepsilon_{\Delta d}^{n}\text{ mod }n^{2}$。
#### Generate location matrix
數據請求者生成一個空白位置矩陣，將想指派任務的區塊變成非 0，形成 Ml (如圖 3b)，可以有多個任務。
:::info
為了模糊化地點，指派任務地點會被分散在一個涵蓋 k 個元素 (k > 1) 的區塊。
:::
接著將這些非 0 填上 $Cm_{T}=(g^{t_{0}}\cdot h^{r_{0}})^{-1}\text{ mod }p$，其中 $t_{0}$ 為該區塊任務的名聲下限，$r_{0}$ 為隨機數，形成 **Mdl** (如圖 3c)。
接著用名聲中心公鑰加密 $t_{0}$ 及 $r_{0}$ :
$t_{0}\overset{PK_{r}}{\longrightarrow}C_{t0}$
$r_{0}\overset{PK_{r}}{\longrightarrow}C_{r0}$
#### Broadcast task
數據請求者將 $I_{d}=\{T,Pk_{d_{a}},Pk_{d_{h}},C_{m},\textbf{Mdl},C_{t_{0}},C_{r_{0}},C_{d0},C_{\Delta d}\}$  傳給雲端伺服器，
將 $I_{cl}=\{T,Pk_{d_{a}}\}$ 傳給感測車輛。
### Recruit vehicles
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/locationmatrixcar.png" width="80%" alt="location matrix for car (Fig 4)"> <br>
#### Generate location matrix
感測車輛在收到 $I_{cl}$ 後，生成相同大小的空白位置矩陣 Ml'，將想做感測任務的區塊填上 $Cm_{vi}$，形成**Mvl** (如圖 4c)。
#### Submit information
感測車輛將 $I_{sl}=\{P_{vid},\textbf{Mvl}\}$ 經由 RSU 傳給雲端伺服器。
### Select vehicles
#### Match reputation and location
雲端伺服器在收到 $I_{sl}$ 後，計算逐項乘積 $\textbf{Mr}=\textbf{Mdl }\circ\textbf{ Mvl}$，並分成以下兩種狀況:
 * 若 $\textbf{Mr} = 0$，感測車輛不在感測任務範圍內。
 * 若 $\textbf{Mr} \ne 0$，感測車輛在感測任務範圍內，非零元素 $C_{mr}=C_{mT}*C_{m_{vi}}$。

當 $\textbf{Mr} \ne 0$，(RVEV) 雲端伺服器將 $I_{r2}=\{P_{vid},C_{t0},C_{r0},C_{m_{vi}},b\}$ 送至名聲中心，名聲中心回傳 $I_{r1}=\{H,D,D_{1},D_{2},Cm_{vi}\}$，雲端伺服器判斷是否相同。
若相同，(RVRP)  雲端伺服器將 $I_{r2}=\{P_{vid},C_{t0},C_{r0},C_{m_{vi}},b\}$ 送至名聲中心，名聲中心回傳 $\{Cm_{v1},Cm_{v2}\}$ 至雲端伺服器，雲端伺服器判斷是否在範圍內。
若在範圍內，雲端伺服器判斷該車能執行任務，進行下一步驟。
#### Reassign task
雲端伺服器再加密 $C_{m}$ 得到 $C'_{m}$，計算 $Ack=g^{\alpha_{i}}\text{ mod }p$ ($\alpha_{i}$ 為隨機數)，並將 $I_{c3}=\{Ack,C'_{m},Pk_{d_{h}}\}$ 經由 RSU 傳給感測車輛。
:::info
再加密方法來自 "Enabling strong privacy preservation and accurate task allocation for mobile crowdsensing"，這裡不說明。
:::
### Submit data
#### Obtain task content
感測車輛在收到 $I_{c3}$ 後，解密 $C'_{m}$ 得到感測內容 m。
#### Encrypt and upload data
感測車輛感測資料 d 並加密 $C_{d}=g_{a}^{d}*\varepsilon_{d}^{n}\text{ mod }n^{2}$，其中 $C_{d}$ 為同態密文，$\varepsilon_{d}$ 隨機數。
感測車輛計算 $A_{vid}=(Ack,P_{vid})$ ，並將 $I_{s2}=\{C_{d},A_{vid}\}$ 經由 RSU 傳給雲端伺服器。
### Process data and return results
#### Verify sensing data
雲端伺服器先檢查 $A_{vid}$。若合法，計算
$C_{d'}=C_{d}\cdot C_{d0}=\left\{ \begin{array}{l}g_{a}^{d-d_{0}}\cdot\varepsilon_{d}^{n}\cdot\varepsilon_{d0}^{n},\text{if }d\ge d_{0}\\g_{a}^{d_{0}-d}\cdot\varepsilon_{d}^{n}\cdot\varepsilon_{d0}^{n},\text{otherwise}\end{array} \right.$。
雲端伺服器比較 $C_{d'}$ 與 $C_{\Delta d}$，若 $d'\le\Delta d$，感測資料為合法的。
:::info
比較方法來自 "iTAM: Bilateral privacy-preserving task assignment for mobile crowdsensing"，這裡不說明。
:::
#### Aggregate sensing data
雲端伺服器聚集所有合法資料並將 $I_{c4}=Aggre$ 傳給數據請求者。
:::info
聚集方法來自 "Toward incentivizing fog-based privacy-preserving mobile crowdsensing in the Internet of Vehicles"，這裡不說明。
:::
### Send reputation feedback reports
根據感測結果，數據請求者對各感測車輛生成名聲回饋，並將 $I_{c5}=\{P_{vid},F\}$ 傳給名聲中心。
在這裡，F=1 代表正回饋，F=0 代表負回饋。
### Update reputation values and pseudonyms
名聲中心在收到 $I_{c5}$ 後，名聲中心先確認 $P_{vid}$ 是否合法。
若合法，名聲中心檢查回饋數量是否達到 $G_{n}$ 或時間是否達到閾值 $\Delta t$。
若達到，名聲中心跟據 TFRU 更新 $Cm_{vi}^{*}$。同時，名聲中心更新化名 $P'_{vid}=g^{vid}\oplus R'_{vi}\text{ mod }p$，其中$R'_{vi}$ 為隨機數。
名聲中心將 $I_{r3}=\{P_{vid},Cm_{vi}\}$ 傳給指定感測車輛。
# Theoretical analysis
## Property Analysis
### RVEV Algorithm
若 $Cm'_{vi}=Cm_{vi}$，則雲端伺服器可以相信名聲沒被竄改。(證明略)。
若 $Cm'_{vi}\ne Cm_{vi}$，則名聲一定有被竄改。(證明略)。
若只有名聲中心知道兩個實際名聲值，則 RVEV 是零知識。(證明略)。
### RVRP Algorithm
若名聲值在範圍內，則雲端伺服器可以相信有通過名聲驗證。(證明略)。
若名聲值不在範圍內，則名聲驗證一定不會通過。(證明略)。
若只有名聲中心知道兩個實際名聲值，則 RVRP 是零知識。(證明略)。
## Privacy Analysis
### Location Privacy
### Identity Privacy
### Sensing Data Privacy
### Reputation Value Privacy
## Security Analysis
# Evaluation and performance analysis
## Performance of PPRM Scheme
## Evaluation of RVEV Algorithm
## Evaluation of RVRP Algorithm
## Evaluation of TFRU Algorithm
# Conclusion