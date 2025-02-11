---
title: Science Visualization

---

# Science Visualization
TextBook : The Visualization Toolkit An Object-Oriented Approach To 3D Graphics
[TOC]

## Data Types & Visualization Methods
+ Scalar field
    + iso-surfaces
    + volume rendering
    + slicing
+ Vector field
    + streamlines
    + stream-ribbons
    + stream-tubes
    + texture-based method
+ Tensor field
    + hyper-streamlines
    + hyper-streamtubes
+ Information V
    + projection
    + parallel coordinates
    + focus+text
    + graph drawing
+ mobile device
    + layout of table/chart
    + networks
    + cloud-computing

### 流程圖
1. 獲取資料
2. 探索和消化資料
3. 分類且表示資料(實體性質)
4. 給予光學性質和視角參數
5. 畫出來
6. 互動式調整和探索資料
![](https://hackmd.io/_uploads/SJWerRIya.png)

### Pipeline of Visualization
1. 取得資料
2. 資料前處理
    - Filtering
    - classification
    - segmentation
3. Proxy geometry construction ([什麼是Proxy geometry](https://docs.unrealengine.com/4.27/en-US/TestingAndOptimization/ProxyGeoTool/ProxyGeoOverview/))
    - Iso-surface
    - ROI segmentation
    - streamlines
    - principal axes of tensor field
4. Mapping and Optical property assignment
    - colors
    - opacities
    - texture mapping
5. 設置視角和投影方法
6. 畫出來
7. 回到4調整

## 獲取的資料來源
### 醫學影像
+ X-ray
+ CT-scan
+ MRI
    + fMRI
+ Ultrasound
    + Doppler Ultrasound

### Scientific computing
:::danger
:warning:施工中 
:::
+ Finite Element Analysis
+ Finite Difference Method (FDM)
+ Finite Volume Method (FVM)


## Interpolation(內插)
+ 動機 : 計算給予資料(分散的點座標)的中間資訊
+ 應用 : 創造資料間的幾何資訊
    + iso-surface
    + streamline
    + query function value
+ e.g. Input: {($x_i$, $f(x_i)$)}, $i=0,...,N-1, f(x)=?$ (x為任意位置)
![](https://hackmd.io/_uploads/BkDYO1P1a.png)

> :warning: **Fitting vs. Interpolation**
> given $\{x_i,f_i=f(x_i),i=0,1,2,...\}$ find a function $p(.)$ to approximate $f(x)$ : 
> :bangbang: The Diff between two methods : 
> Interpolation -> $p(x_i)=f_i$
> Fitting -> approximate $f(x_i)$
>![](https://hackmd.io/_uploads/H1Jr21DyT.png)

### Data Types
+ 根據 mesh/grid structure 可以分成以下幾種資料
    + Regular grid data
    + Structure grid data
    + Unstructured grid data
    + Scattered data
    + High dimensional data
        + Structure or unstructured
        + No embedded grid
+ 不同的資料類別用不同的interpolation method
![](https://hackmd.io/_uploads/rJYqwzO1p.png)

#### Regular Data Sets
:::info
什麼是Regular grid data :question: 
不嚴謹的講，regular grid就是固定大小的網格資料，可以用在**Finite Element Analysis**、**Finite Volume Methods**和**Finite Difference Methods**，通常會在離散的參數空間(discretization of parameter space)
Regular data的好處在於，原始資料中存在固有關係，意思是資料間有規律，這樣在存資料時只需存起始間格、資料間的空間大小以及總共有多少點即可推算整個資料集，省下記憶體空間
ref.
[wiki](https://en.wikipedia.org/wiki/Regular_grid)
http://tiny.cc/jlabvz
http://tiny.cc/klabvz
**The Visualization Toolkit An Object-Oriented Approach To 3D Graphics** (Book)
:::

## Glossary
+ Tetrahedra 四面體