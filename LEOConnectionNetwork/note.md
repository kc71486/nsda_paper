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
ISL:Inter-Satellite Link (衛星間的連結)
衛星間的連結主要受到兩者之間的"視線"影響，衛星間的通訊必須在熱氣層之上(水蒸氣會阻礙通訊)。
# User-driven networking algorithm design

# Performance evaluation

# Conclusion