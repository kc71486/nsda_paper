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
<img src="https://github.com/kc71486/nsda_paper/raw/main/PrivacyReputationVehicle/img/beta_distrib.png" width="50%"> <br>
Beta Distribution 的公式如下:
$f(x|a,b)=\frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)}x^{\alpha-1}(1-x)^{\beta-1}$
其中 $0\le x\le 1,\alpha>0,\beta>0$
該分布 $\alpha$ 越大，圖形偏左邊， $\beta$ 越大，圖形偏右邊，兩者同時變大時圖形偏中間，同時為1時為平均分布
該分布的期望值$E(x)=\frac{\alpha}{\alpha+\beta}$
## Paillier Algorithm
# Problem Statement
## System Architecture
## Security Model
## Design Goals
# Design of proposed scheme
# Theoretical analysis
# Evaluation and performance analysis
# Conclusion