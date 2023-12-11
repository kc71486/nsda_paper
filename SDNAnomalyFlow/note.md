# A Flow-Based Anomaly Detection Approach With Feature Selection Method Against DDoS Attacks in SDNs

# Abstract
# Introduction
# Related work
# Prelimary
## SDN operation
<img src="https://github.com/kc71486/nsda_paper/raw/main/SDNAnomalyFlow/img/traditional_vs_sdn.png" width="80%" alt="traditional vs sdn"><br>
軟體定義網路包含三種不同平面，應用平面、控制平面、資料平面。
應用平面將一些網管人員會用到的系統，如入侵檢測 (IDS)、監視、維持服務品質等等，包裝成 API 的形式。
控制平面以集中化的形式控制整個網管系統。
資料平面為底層網路，像是網路交換器 (switch) 之類的網路轉送 (forwarding) 裝置就位於資料平面。
軟體定義網路的特色就是分離控制平面與資料平面，兩者之間的溝通用南向介面 (southbound interface) 溝通。
:::info
northbound interface 是控制平面與應用平面溝通的媒介，而 southbound interface 是控制平面與資料平面溝通的媒介。
:::
對於一個流量，交換器先檢查是否有在流量表中，
 * 如果有，交換器直接轉送訊號到對應目的地。
 * 如果沒有，交換器將該 header 包裝成訊號傳給控制器，控制器經由上層決定是否轉送，決定轉送的話再將訊號傳給交換器，交換器會將該資訊存在表中。
## DDoS attack overview
<img src="https://github.com/kc71486/nsda_paper/raw/main/SDNAnomalyFlow/img/DDoS_operation.png" width="100%" alt="ddos operation"><br>
DDoS 為阻斷服務式攻擊。DDoS 通常不會突然發生，攻擊方會先透過一些系統漏洞在其他電腦植入病毒，使其能遠端控制這些殭屍，再對目標主機進行埠掃描以確認攻擊目標。這些殭屍網路往往能在短時間癱瘓目標主機。
## DDoS attack in SDN
集中化的控制器能提升整體網路安全 (經由北向介面)，但是將控制平面和資料平面分離也產生新的弱點，像是攻擊控制器或攻擊南向連接，同時，一些針對應用層的攻擊也依舊存在。不像SDN，傳統攻擊往往只能對網路的一小部分造成影響，因此攻擊 SDN 造成的影響往往比傳統攻擊的影響大。
針對 SDN 的其中一個攻擊為 DDoS，攻擊者能輕易的從假冒 IP 中產生大量流量。以下為幾種攻擊:
 * 緩衝區飽和攻擊: 當交換器收到沒紀錄的封包，交換器會先暫存該封包，並將表頭送至控制器。攻擊方能送出大量假冒 IP 的封包短時間飽和緩衝區使正常流量無法進入。
 * 流量表溢位攻擊: 交換器流量表儲存在三態內容可定址記憶體 (TCAM) 中，每筆流量包含空閒超時 (時間內不再有相同流量) 和硬超時 (硬上限)。攻擊方能送出大量不在流量表的流量將記憶體填滿使正常流量無法進入。值得注意的是因為 TCAM 又貴 (400x) 又耗電 (100x)，交換器流量表通常不會太大。 
 * 連結洪泛攻擊: 當攻擊方送出大量封包使緩衝區飽和，有些交換器會選擇將整筆封包經由南向介面傳至控制器，這會使頻寬快速被占用導致正常流量無法流通。
 * 控制器飽和攻擊: 控制器是一個在虛擬環境上的應用程式，因此資源 (記憶體、計算能力) 有限，當攻擊方送出大量假封包，過多的請求會造成計算能力下降。因為控制器影響整個網路，這種攻擊會對 SDN 控制器產生巨大影響。
<!--  -->
因為所有不在表中的流量都會送給控制器，不管控制器性能如何，高流量一定會使控制器無法負荷。
## Flow-based IDS in SDN
<img src="https://github.com/kc71486/nsda_paper/raw/main/SDNAnomalyFlow/img/anomaly_detection.png" width="80%" alt="fig5"><br>
 * 流量收集和提取模組: 控制器每固定時間收集流量統計 (用 OFPT_FLOW_MOD)。這時間間隔太長的話請求瞬間對雙方負擔都很大，且攻擊方更容易攻擊，而間隔太短的話整體頻寬用量及計算資源用量會上升。有些論文正試著減少控制平面上的開銷。
 * 流量辨識模組: 本架構的核心，如: IDS。辨識特徵並判斷是否為惡意流量。該模組辨識是否惡意流量，是的話就會送到下一個緩解管理模組，不是的話就照一般流程。該模組分成基於簽名與基於異常兩類。基於簽名的系統是根據比對資料庫中惡意簽名，具有高準確度(99%)，但是無法判斷零時差攻擊。基於異常的系統理論上能判斷新型態攻擊，但是偽陽性較高。基於異常的系統品質好壞與訓練及高度相關，因此需定期更新。
 * 緩解管理模組:當一個流量被判定為惡意，控制器馬上根據攻擊做出相對應的舉動。目前最熱門的作法是增加新的動作為 Drop 的流量表。另一種做法是將所有多餘的惡意流量導至蜜罐伺服器進行近一步的分析，結果顯示能降低控制器流量，而提升蜜罐流量。另外，判定為惡意的流量也會透過 OFPFC_DELETE 刪除交換器上對應流量。
:::info
蜜罐通常偽裝成看似有利用價值的網路、資料、電腦系統，並故意設定了bug，用來吸引駭客攻擊。由於蜜罐事實上並未對網路提供任何有價值的服務，所以任何對蜜罐的嘗試都是可疑的。蜜罐中還可能裝有監控軟體，用以監視駭客入侵後的舉動。 (@wiki)
:::
# Methodology
# Proposed scheme
# Security analysis
# Experimental result
# Network performance analysis
# Discussion and limitation
# Conclusion
# Reference
 * [Northbound interface and southbound interface](https://www.techtarget.com/whatis/definition/northbound-interface-southbound-interface)
