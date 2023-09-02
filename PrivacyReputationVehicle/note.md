# A Lightweight Privacy Preservation Scheme With Efficient Reputation Management for Mobile Crowdsensing in Vehicular Networks

# Abstract

# Introduction

# Related work

# Prelimary
## Hadamard Product
Let matrices $A,B\in \mathbb{C}^{m\times n}$ and $A=[a_{ij}]$, $B=[b_{ij}]$, then $A\circ B$ (Hadamard Product) is:<br>
$\begin{bmatrix}a_{11}b_{11} & a_{12}b_{12} & ... & a_{1n}b_{1n} \\a_{21}b_{21} & a_{22}b_{22} & ... & a_{2n}b_{2n} \\\vdots  & \vdots  & \ddots  & \vdots  \\a_{m1}b_{m1} & a_{m2}b_{m2} & ... & a_{mn}b_{mn}\end{bmatrix}$
## DLP
離散對數 (DLP)。循環集 $\mathbb{G}$ 中，給定質數 a, b，找到一個整數 k 使得 $b^{k}=a$，我們目前還沒有找到多項式時間能計算它。
## DDH
Diffie-Hellman (DDH)，循環集 $\mathbb{G}$ 中，給定 $g$, $g^{x}$, $g^{y}$ (x, y 為整數)，我們難以使用隨機生成的 z，使得 g^{z}=g^{ab} 並再區分出 a、b。
## Beta Distribution
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/beta_distrib.png" width="50%" alt="plots of several beta distributions"> <br>
Beta Distribution 的公式如下:
$f(x|a,b)=\frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)}x^{\alpha-1}(1-x)^{\beta-1}$
其中 $0\le x\le 1,\alpha>0,\beta>0$
該分布 $\alpha$ 越大，圖形偏左邊， $\beta$ 越大，圖形偏右邊，兩者同時變大時圖形偏中間，同時為1時為平均分布。
該分布的期望值 $E(x)=\frac{\alpha}{\alpha+\beta}$
## Paillier Algorithm
# Problem Statement
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/scheme.png" width="100%" alt = "scheme"> <br>
## System Architecture
本系統包含以下5種不同單位:
 * data requester
 * cloud server
 * reputation center
 * roadside unit (RSU)
 * sensing vehicles

數據請求者: 數據請求者可能是管理部門或服務部門，甚至是被授權的個人。它分配感測任務來獲取感測數據或有價值的信息，例如交通資訊、空氣質量指數值等。<br>
雲端伺服器: 雲端伺服器具有強大的存儲和計算能力，主要負責密文的收集和處理。此外，它還評估感測數據並向聲譽中心發送聲譽反饋報告。<br>
聲譽中心: 聲譽中心負責驗證、存儲、分發和更新每個感測車輛的聲譽值和假名。<br>
RSU: RSU作為雲端伺服器與感測車輛，或聲譽中心與感知車輛之間的橋樑，部署在路邊，主要負責數據傳輸。<br>
感測車輛: 這裡假設傳感車輛配備有多種可以感知數據的感測裝置。此外，每個傳感車輛都擁有一個可信平台模組（trusted platform module, TPM），用以保存私鑰、位置和其他秘密資訊。<br>
## Security Model
## Design Goals
# Design of proposed scheme
# Theoretical analysis
# Evaluation and performance analysis
# Conclusion