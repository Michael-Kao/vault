---
title: Computer Graphic

---

# Computer Graphic
[toc]

## Introduction

+ What is Computer Graphics(CG)?
    + 利用電腦產生的圖像的程序
    + Ex:
        + 幾何模型展示
        + 動畫
        + 電影中的特效
        + VR、AR、MR
        + 醫學影像處理
        + 資料視覺化
+ Key CG Areas
    + 逼真的渲染、動畫
        + 繪出場景越逼真越好
        + Phong Shading(一種著色法)、Ray-tracing(光追)、Radiosity(輻射度演算法)
        + 虛擬實境
    + 幾何模型
        + Computer Aided Geometry Design(CAGD) (電腦輔助幾何設計)
    + 影像處理
    + 視覺化
        + Scientific visualization, data visualization, medical imaging, ...
    + 文字編輯和處理
    + 遊戲和互動渲染(rendering)以及展示
    + 物理模擬
    + Manufacturing
        + 3D printing, Computer Aided Manufacturing

+ Motivation of CG
    + CAD, circuit design
    + CAGD, 幾何設計, 汽車外型
    + Illustration
    + Word Processing
    + Architecture blueprints

+ Important Comppanies
    + Evan & Sutherland : CG system company
    + SGI : CG systems
    + Pixar : Animation, visualization, Graphics programming libraries
    + Disney : Animation
    + Adobe : word processing
    + Nvidia, ATI(AMD), ARM, ... : GPU

---
## Graphic System

+ Hardware
    + Motherboard
    + Hard disk, network
    + Graphics card
    + Display devices
    + Input devices
+ Software
    + OS
    + Window System
        + Window manager
        + Display server
        + Client programs
    + Graphics libraries
        + **OpenGL, glu, glut, gles**
        + Shading languages and drivers(glsl)
        + Programming libraries(CUDA, OpenCL, ...)
        + GUI API(Qt, glui, ...)
+ Applications
    + Rendering programs
    + Visualization tools
    + HCI and GUI kits
    + Games
    + Simulators
    + Geometric design tools
    + Word processing software
---
## CG Architectures

![](https://i.imgur.com/ZJ9LYcC.png)
+ Graphic Card bus
    + PCI
    + AGP
    + ***PCI Express***
+ Usage of graphics cards
    + Rendering acceleration
    + Image processing
    + Geometrical computing
    + Massive parallel computing
    + General purpose computing
    + Artificial computing(deep learning)
+ Compuonents
    + GPU
        + Rendering
        + General computation
        + Geometrical computing
        + Image processing
    + Memory, registers
        + Keeping data & buffering
        + Displaying & buffering
        + Keeping programs
    + Internal bus & external bus
        + Data transfer
+ Graphics Card Connection
    + The graphics card is connected to north bridge via a bus
        + 單晶片系統不採用南僑、北橋架構(Ex. ADM, Intel)
    + The bus can be **PCI**, **AGP** or ***PCI express***
    ![](https://i.imgur.com/feFBwPZ.png)
+ Bus
    + PCI is the oldest
    + AGP is faster
    + PCI Express is the fastest
    + Transfer speed (clock rate)
    + Bandwidth (bits, wires)
+ PCI Express (PCIe)
    + released in 2002
        + Compatible with old PCI bus
    + Low power consumption
    + Serial ports (multiple ports)
        + Dual direction
    + Newest version: PCIe x 16
    + version 6.x
        + spped : 16GB/32GB for GPU
        + Shared with other peripheral devices
    ![](https://i.imgur.com/aFMH5ED.png)
+ General Architecture of GPU
    + Multiple processors
        + Stream Processor, Parallel computing
    + Multiple ALUs (cores) in each processor
        + Parallel computing, multiple threads
    + Hierarchical memory structure
        + Main memory in the mother board
        + Shared memory in the graphic card
        + Local memory for each core
        + Registers for ALUs
    ![](https://i.imgur.com/cbm3zOD.png)
+ Other GPU Functionalities
    + Contain multiple ALUs for shading purpose
    + Reder several pixels at the same time
    + General purpose computing
        + CUDA & OpenCL
    + Floating and integer arithmetic
    ![](https://i.imgur.com/qlNLMda.png)
---
+ CPU Manufacturers
    + Nvidia
    + ARM
    + Intel
    + AMD(ATI)
+ Characteristics of GPU
    + Graphics computing
    + ***Lower power consumption*** ~~(Nvidia重新定義低耗電)~~
    + Limited functionalities
    + Lower precision (numercally 數字上的)
    + Massively parallel
    + System on Chip (SoC)
    + smart-phone, game kits, AR, VR...
+ New Applications
    + Artificial intelligence
        + Pattern recognition
        + Deep learning
        + Video stream processing
    + Super-computing
    + Self-driving cars
    + Cloud computing
    + General-purpose computing on graphics processing units(GPGPU)
    + New Features
        + Thousands of thread processors
        + Multiple ports
        + May lacking of display(for super-computing)
        + Higher computing power
        + SoC architecture
        + AI computing
+ Graphics Memory
    + AKA video memory
        + Several Giga-byte DDR(Dual Data Rate) memory
    + For keeping information
        + Geometrical data: vertex, normal, connectivity, ...
        + <span style="color:red">***Texture***</span>
        + Images
        + Shading programs
        + tmeporary data: scalars, vectors, matrices, ...
    + On-board or in the graphic card
    + Large register file, fast accessing speed (serveral megabytes)
+ Graphics Libraries
    + Purposes and definition:
        + Sets of subroutines for designing graphics application programs
    + Famous graphics libraries
        + OpenGL, Web GL, GL es, Mini GL, Mesa, ...
        + DirectX
        + X window libraries, Xt, Xm
        + Vtk (for visualization)
+ Graphical User Interface libraries (GUI)
    + glut
    + glui
    + ImGui
    + GTK
    + Qt
    + X-window and MS window libraries
+ Shading languages
    + Use OpenGL to render objects (Executed in CPU)
    + Can replace OpenGL shading routines with our shading programs
        + Written by using shading languages
        + Executed in GPU
        + Vertex shaders
        + Pixel Shaders(fragment shaders)
        + Geometrical shaders
        + Tesselation shaders
        + Computational shaders
    + Shading languages
        + GLSL
        + Cg
+ OpenGL ES
    + For smartphone programming
        + User interface
        + Rendering and display
        + Image processing
        + More primitive than OpenGL
        + more flexible (can design rendering methods)
        + Limited instruction set
+ Web GL
    ![](https://i.imgur.com/EGEBVO3.png)
    + ***Javascript API***
    + Mixed with HTML elements
    + Accelerated by GPU
    + Developed from OpenGL ES & conventional OpenGL
    + Easy to use for smart phone applications
    + Limited functionalities
+ General Purpose GPU
    + GPU contain multiple ALU
    + Be used to perform
        + Scalar floating point calculation
        + Basic vector operations
        + Basic matrix operations
    + solve non-graphical problems
        + Massive parallel computing
        + Shared memory models
+ Massive Parallel Computation
    + A computer system may contain more than one GPU
    + A GPU contain many ALU
    + Graphic memory are geeting larger
    + A massive parallel computing environment is emerging
+ Parallel Programming language for GPU
    + CUDA
    + OpenCL
+ Deep Learning & GPU
    + AI chip = GPU (advanced GPUs)
    + Usage
        + Model training (learning)
        + Run-time patter detection
        + Run-time classification
        + 加速器
    + Multiple GPUs in a board
        + Many ALU in a GPU (Nvidia A10, A40, ...)

---
## 突然想寫的心得
### Camera Control
> 為了改變視角  

方法:
+ 移動相機
+ 改變攝影方向  

Viewing Transformation 三大主要元素:
+ eye position: E (相機擺放位置)
+ focus position: F (觀看的點)
+ [View up vector: Vup](https://stackoverflow.com/questions/21467225/how-do-you-determine-the-view-up-vector) (由v跟w軸所形成的***所有***向量都是，詳細定義看連結)

三軸旋轉名詞:
+ pitch -- 對著u軸(x軸)旋轉，會改變focus
+ roll  -- 對著-w軸(z軸負方向，通常表示指出畫面)旋轉，不會改變focus
+ yaw   -- 又稱heading，對著v軸(y軸)旋轉，會改變focus

:::info
這裡指的改變focus，表示看的目標點改變，而不是物體本身改變。
藉由選轉相機改變角度，所以看的中心點改變。
![](https://i.imgur.com/LwUi1Hv.png)

---

![](https://i.imgur.com/nYR8EuS.png)
下圖是經過pitch後的變化，除了右下角的視窗相機是可變的，其餘都是固定的，由圖可知真正的物體並未改變，focus的改變是由旋轉相機造成的。
:::

---
## Diffuse reflection 擴散反射
![](https://i.imgur.com/MMp3tXm.png)

* 效果(最明顯的反射):
    * 亮度
    * 表面的顏色
+ 與視角或眼睛的座標獨立
+ 相依於光源屬性和表面屬性(光強度/方向、表面顏色/粗糙與否/方向)
+ 傳送到表面內部後再反射
    + 在表面內的反射路徑是**隨機的**
    + 可能會從表面的任何方向射出
    + **結論**:可得知擴散到所有方向
![](https://i.imgur.com/J0jabRL.png)

+ 光能量分布
    + 在所有方向均勻分布
    + 在所有方向，光強度不變
    + **結論**:所有位置看到的光一樣亮

### Diffuse Intensity
+ 擴散強度與視線方向獨立
+ 與表面接收到的能量密度有關
+ 與表面的反射能力有關$K_{d}$
+ **Received Energy Density = Power / Coverd_Area**
    + 如果入射角垂直(從平面法向量射入)，其面積為單位面積
    + 若以斜角方式射入，則coverd_area變大
    + 面積提升(分母變大)，能量密度減少

+ 小入射角 -> 強擴散
+ 大入射角 -> 弱擴散
![](https://i.imgur.com/3DfKKp4.png)

#### 計算
+ 參數:
    + N = surface normal 平面法向量
    + L = direction from the position to the light source 光源的入射向量
    + I = energy of the ray
    + $K_{d}$ = diffuse reflection coefficient
+ Covered Area
    + 入射角和法向量的夾角為 $\theta$
    + $\Delta S=\Delta A/\cos(\theta)$
    + $\Delta A$為單位面積
    + $\cos(\theta)=$<N,L>
    + 在面積上的平均能量
        + $I/\Delta S=I/(\Delta A/\cos(\theta))$
         &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; $=I*\cos(\theta))$
+ Diffuse Intensity
    + 能量密度由$\cos(\theta)=$<N,L>決定
    + 表面反射能力會根據材質而定
        + 使用$K_{d}來模擬其反射能力(0\leq K_{d}\leq 1)$
+ Diffuse Reflection Coefficient $K_{d}$
    + 表面的係數RGB組成都不同
    + 他的顏色由3個反射係數決定
    + Example:
        + red surface's coef = (1, 0, 0)
        + grey surface's coef = (0.2, 0.2, 0.2)

+ 結論:
    + 大$\theta$ -> 面積大 -> 能量密度小 -> 弱擴散反射
![](https://i.imgur.com/4X3dryA.png)
![](https://i.imgur.com/Ccl7VAG.png)

:::warning
只要表面沒有光源就沒有發光效果
沒有發光效果=no diffuse, no specular
![](https://i.imgur.com/kVhPuDB.png)

:::



---
## Specular reflection 高光反射
![](https://i.imgur.com/QOeTkJ2.png)

+ 效果(亮點):
    + 高光/高亮
+ 相依於表面的方向/光滑度/曲率/視角/光源(光強度/方向)
+ 屬於直接反射
+ 入射角=反射角
+ 入射光、反射光和平面法向量為同一平面
+ 反射光方向為: $R=2<N,L>N-L$

<***注意L的方向***>

![](https://i.imgur.com/5M2JTub.png)
+ 不存在完美表面
+ 

---
## Types of light sources
+ Parallel light
    + distant light
    + directional light
+ point light
    + positional light
+ spot light
    + real light lamp

---
### Parallel light
+ 無限遠的燈
    + e.g. 太陽
+ 參數
    + Color
    + Direction
    + Intensity

---
### Point Light
+ 光源在固定點上
+ 由點發散出去
+ 所有方向有相同的強度
+ 參數
    + Position
    + Color
    + Intensity

---
### Spot Light
+ 光源在點尚且光發散的方向限制在一個conic內(冰淇淋餅乾)
+ 光強度為與中心點(?)的外積值(越靠近中心越亮)
+ 參數
    + Position
    + Direction
    + Color
    + Intensity
    + Cutoff angle
    + Exponent $\cos(\beta)^{m}$的$m$


---
## 有用文章
+ [What is glViewport for](https://gamedev.stackexchange.com/questions/147522/what-is-glviewport-for-and-why-it-is-not-necessary-sometimes)

+ [glOrtho](https://stackoverflow.com/questions/2571402/how-to-use-glortho-in-opengl)
    + [This is better](https://blog.csdn.net/shizhipeng/article/details/4939529)
+ [gluPerspective](https://blog.csdn.net/dcrmg/article/details/53106457)
+ [glFrustum](https://blog.csdn.net/chy19911123/article/details/45476821)
+ [放大](https://blog.csdn.net/chy19911123/article/details/48786933)
+ [計算View volume 座標](https://stackoverflow.com/questions/61628768/perspective-projection-canonical-viewing-volume)
    + [tuto](https://cgvr.cs.uni-bremen.de/teaching/cg_literatur/lighthouse3d_view_frustum_culling/index.html)
+ [OpenGL光源](https://blog.csdn.net/p942005405/article/details/103900321)
+ [New to Light](https://stackoverflow.com/questions/8494942/why-does-my-color-go-away-when-i-enable-lighting-in-opengl)

---
### 小抄
+ eye coordinate system:
    + 表示眼睛(相機)
    + 即gluPerspective的視角