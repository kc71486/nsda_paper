# Physical Layer Authentication for LEO Satellite Constellations

# Abstract
# Introduction
本文貢獻:
 * 使用都卜勒效應區別正常節點和竊聽節點。
 * 與其他PLA方法相比，本方法不須使用安全通道。因為所有衛星必須分享位置資訊 (為了安全)，都卜勒效應也就能被計算出來。
 * 結合多個衛星的標稱功率譜密度樣本(NPSDS)，就算有攻擊者想改變功率和頻率還是會被偵測到。對於不完美的 NPSDS 估計，計算偽造機率和偽陽性機率。
 * 比較在不完美的 NPSDS 估計下各種不同規則的表現。
# Related work
# System model
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOPLA/img/fig1.png" width="80%" alt="fig 1"><br>
本方法分為兩階段，第一階段各衛星識別傳輸者，第二階段這些衛星傳給統計中心做最終判斷。
N 為接收方數量，m(t) 為傳輸訊號。
A 為正常衛星， E 為偽造/竊聽衛星。
## Doppler frequency shift as a fingerprint
主張1:
當 $N\ge 6$ 時，接收方接受到的都卜勒頻移已足以識別傳輸方身分。(基本概念是六個未知數(三維座標、三維速度)要六個方程式去解)
## Channel model
假設各衛星配有一個全向式天線，並且訊號強度主要受路徑損耗及都卜勒頻移影響，且通道類型為 AWGN。假設 A 的位置及速度資訊都公開。在這些假設狀況下，接收方收到訊號為:
$y_i(t)=\left\{ \begin{array}{cl}h_{ai}(t)m(t)+w_i(t),H_0 \\h_{si}(t)m(t)+w_i(t),H_1\end{array} \right.$。
其中 $h(t)=\sqrt{l_{ki}}e^{j(\omega_{ki})cos(\delta_i)t}+\Phi_i$ 為訊號衰減項，包含:
$\omega_{ki}$ : 都卜勒頻移，
$\delta_i$ : 訊號抵達角造成的相位偏移，
$\Phi_i$ : 傳輸方相位差。
這裡假設接收者只知道訊號的變異數(好處:接收者不須要導頻訊號、載波頻率不影響估算)。
# Physical layer authentication model
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOPLA/img/alg1.png" width="80%" alt="alg 1"><br>
## NPSDS estimation
給定 $y=x+w=hm+w$，其離散傅立葉轉換形式 $Y=X+W$，因為高斯步驟在離散傅立葉轉換下保持不變，訊號 X 和雜訊 W 也是複高斯、平均為 0、正交步驟(互不相關)。Y 之間互不相關 (因為假定 X 固定)。
Y 相位與都卜勒頻移無關 (因為隨機)。
## Binary hypothesis test
$\hat{\Theta}_{ki}$ 為 gamma distribution，也就是
$\large\hat{\Theta}_{ki}=\frac{1}{\Gamma(T)\Theta_{ki}^TT^{-T}}\hat{\Theta}_{ki}^{T-1}e^{-\frac{\hat{\Theta}_{ki}T}{\Theta_{ki}}}$。
決策為:
$\left\{ \begin{array}{cl}|\hat{\Theta}_i-\hat{\Theta}_{ai}|\le\lambda_i,\ d_i=0 \\|\hat{\Theta}_i-\hat{\Theta}_{ai}|>\lambda_i,\ d_i=1\end{array} \right.$。
$\hat{\Theta}_{i}$ 為偵測到的，
$\hat{\Theta}_{ai}$ 為根據 A 的位置及速度估算出的，
$\lambda_i$ 為衛星 i 的決策閾值。<br>
註:就算 E 改變相位也不會有用。
偵測機率 $P_D$ 和偽陽性機率 $P_F$ 分別為:
$P_{d_i}(\alpha_i,\beta_i,T)=\frac{\gamma(T,\frac{T(1-\alpha_i)}{\beta_i})+\Gamma(T,\frac{T(1+\alpha_i)}{\beta_i})}{\Gamma(T)}$，
$P_{d_i}(\alpha_i,T)=\frac{\gamma(T,T(1-\alpha_i))+\Gamma(T,T(1+\alpha_i))}{\Gamma(T)}$。
其中 $0<\beta_i=\frac{\Theta_E}{\Theta_A}<\infty$，
$0<\alpha_i=\frac{\lambda}{\Theta_A}<\infty$。
## Decision fusion
假設各衛星判斷的集合 $D=[d_1,d_2,...,d_N]$，$D^+$ 為判斷為 E 的集合，$D^-$ 為判斷為 A 的集合，$D=\{D^+\cap D^-\}$。$P_D$ 為偵測機率，$P_F$ 為偽陽性機率。
### OR rule
一個覺得是偽造就是偽造。
$D^+=D-[d_0=0,d_1=0,...d_N=0]$，
$P_D=1-\prod_{i=1}^{N}(1-P_{d_i}^*)$，
$P_F=1-\prod_{i=1}^{N}(1-P_{f_i}^*)$。
這個方法偵測機率和偽陽性機率都最高。
### AND rule
全部都覺得是偽造才是偽造。
$D^+=[d_0=1,d_1=1,...d_N=1]$，
$P_D=\prod_{i=1}^{N}(1-P_{d_i}^*)$，
$P_F=\prod_{i=1}^{N}(1-P_{f_i}^*)$。
這個方法偵測機率和偽陽性機率都最低。
### Majority rule
過半覺得是偽造就是偽造。
$P_D=\sum_{d\in D^+}[\prod_{i=1}^N(P_{d_i}^*)^{d_i}(1-P_{d_i}^*)^{(1-d_i)}]$，
$P_F=\sum_{d\in D^+}[\prod_{i=1}^N(P_{f_i}^*)^{d_i}(1-P_{f_i}^*)^{(1-d_i)}]$。
# Numerical results
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOPLA/img/fig4.png" width="80%" alt="fig 4"><br>
圖 4 為不同 $\beta$ 下偵測機率和偽陽性機率的關係。
$\beta$ 越接近 1，越有偵測機率=偽陽性機率的趨勢 (兩者越像，偵測者得到的答案越像瞎猜)。
個人體感 $\beta$ 到 $\pm4db$ 就不錯了(因為還要交叉比對)。
<img src="https://github.com/kc71486/nsda_paper/raw/main/LEOPLA/img/fig5.png" width="100%" alt="fig 5"><br>
圖 4 為不同 N、T 下不同聚合方案對準確度的影響。
n=6 時，AND 方案命中率太低，OR 方案偽陽性太高，Majority 方案 $\beta$ 大約要在 $\pm3db$ 才有較好效果。
n=8 和 10 時， AND 和 OR 還是太爛，$\beta$ 大約也是在 $\pm3db$ 才有較好效果。
# Conclusion
(我的想法)
 * 頗新穎的想法，我喜歡。
 * 必須確定最後把節融合的地方是可信任的，這點可能有改善空間。
 * 實際上因為到處都有其他訊號，雜訊不會是 AWGN，不過就算不是，影響應該也不大 (而且有等化器這個東西)。

(本篇結論)
 * 使用在不同衛星見到的都卜勒效應作為獨立識別。
 * 未來可能要把這個方法結合到既定的 PLA 架構上。