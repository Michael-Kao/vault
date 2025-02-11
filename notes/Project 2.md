---
title: Project 2

---

# Project 2
## Scene Graph
![](https://i.imgur.com/mty8Ykp.jpg)

---
## Degree of freedom of the joints
+ 大腿 : rotation, translate
+ 小腿 : rotation, translate
+ 上臂 : rotation, translate
+ 前臂 : rotation, translate
+ 手肘 : translate, scaling
+ 頭部 : translate
---
## 操作手冊
+ 滑鼠滾輪 - 前滾放大下滾縮小，預設值為最小不得更小
+ 方向鍵 - 僅左右有效，改變腳色方向
+ 空白鍵 - 跳躍動作，單壓小跳，長壓可大跳(小跳的5倍高)
+ . - 蹲下按鍵，可切換是蹲下or站立
+ p - 取消裝備手中物品
+ o - 改變手中物品持握的手
+ g - 擺動作(僅一種)
+ w - 行走mode，腳色朝著自己面向的方向持續行走
+ r - 跑步mode，由行走中改為跑步姿勢，每次按壓可增加0.4倍速度，最大速度為行走的3倍
+ s - 停止mode，讓腳色從移動中停止
+ d - 選擇角度增或減 (status 1 表減少)
+ q - 改變角色的左手旋轉角度(肩膀以下)
+ e - 改變角色的右手旋轉角度(肩膀以下)
+ z - 改變角色的左前臂旋轉角度(手軸以下)
+ c - 改變角色的右前臂旋轉角度(手軸以下)
+ 數字鍵 1, 2, 3 - 由小到大分別改變角色的(x, y, z)，用來移動角色用
+ 數字鍵 4, 5, 6 - 由小到大分別改變所有座標系統對(x, y, z)軸的旋轉角度，讓camera可看側面
+ esc - 退出程式
---
## ideas
> 想法是想做成minecraft的第三人稱的感覺，原本有要把跳躍跟蹲下都做成有動畫的感覺，但測試過程中一直穿模所以最後沒有實現，物品往角色身上吸也沒有實現，只做成靠近就自動撿起的效果。