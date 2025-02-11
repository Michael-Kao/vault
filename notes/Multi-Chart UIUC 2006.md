---
title: Multi-Chart UIUC 2006

---

# Multi-Chart UIUC 2006

## Algorithm
:::info
整體演算法由 greedy flattening bounded-distortion parameterization 和 iterative k-means mesh clustering 組成  
什麼是 k-means clustering ? : 類似SOM但不是，本身是greedy演算法  
[k-means 文章點我](https://chih-sheng-huang821.medium.com/%E6%A9%9F%E5%99%A8%E5%AD%B8%E7%BF%92-%E9%9B%86%E7%BE%A4%E5%88%86%E6%9E%90-k-means-clustering-e608a7fe1b43)  
[wiki](https://en.wikipedia.org/wiki/K-means_clustering)
什麼是 bounded-distortion parameterization ? : 還不知道  
[paper 點我](https://igl.ethz.ch/projects/parameterization/BDPMP/sorkine02.pdf)
:::


## 前沿面參數化(Frontier Face Parameterization)
什麼是前沿面 ?  
每一個cluster都有各自的邊界，而形成邊界的點同時也是形成**外部**三角形的點，再將前沿面加入cluster前必須先攤平到參數化區域中，這樣才能計算:  
1. 該面與cluster中心的參數距離  
2. 該面對cluster造成的扭曲程度  
3. 該面對cluster造成摺疊的可能性  
4. 以及對邊界平滑度的影響  
  
前沿面的可能情況 :   
![](https://hackmd.io/_uploads/ByJU5Nr-p.png)

1. 三角面的兩點由邊界提供
    > 此情況下，作者將參數化的座標平均值assign給與邊界無關的頂點，然後將前沿面接上該參數座標，根據[SCOGL02]，這樣做可以減少扭曲並且能產生更大且更有效率的群集。  
2. 三角面三點與邊界共享
    > 該情況下，會選擇最近加入的邊然後將其化簡為Case 1，其可能結果為兩條共享邊產生接縫連起來。  
3. 三角面由前沿面包起來
    > 根據最近加入的邊作為基準然後化簡成Case 2，此狀況出現的頻率可以減少，只要符合Case 2則優先處理，同時也能使群集邊界更平滑。  


## Glossary
+ chessboard $L^\infty$ distance = [Chebyshev distance](https://zh.wikipedia.org/zh-tw/%E5%88%87%E6%AF%94%E9%9B%AA%E5%A4%AB%E8%B7%9D%E7%A6%BB)  
+ 