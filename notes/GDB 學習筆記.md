---
title: GDB 學習筆記

---

# GDB 學習筆記
+ [video](https://www.youtube.com/watch?v=svG6OPyKsrw)  

## TODO
+ 提供範例
+ 簡化敘述

## Log
+ 20240715
    + 簡單紀錄影片中學到的指令

## 隨手記
+ using `-g` flag to compile if you want to run gdb  
    + 讓compiler不要最佳化程式碼,保留原始程式撰寫的規則  

+ `-Og` to compile source code -- 針對Debuger做最佳化  
+ run gdb with `-q` flag -- flag q mean `quiet` 使用後不會顯示GDB版本等等資訊  

## GDB Command
> :bangbang: Treat GDB like C/C++ Programming
+ `run [r]` -- 執行程式(runs like bash)
+ `break` -- 設定break point
    + usage: `break main` -- 在main()設定break point

+ `next [n]` -- 執行程式並且在下一行停止(不進入function)
+ `list` -- 列印出所有的code和行數,用來對應現在位置
+ `print [p] $var` -- 列印出變數的值
+ `quit [q]` -- 離開GDB模式
+ `up` -- 回到(?)call stack中的上一步
+ `down` -- 回到(?)call stack中的下一步
+ `display $var` -- 每一步驟都印出$var(只印出執行當下的值)
+ `undisplay $displayID` -- 解除display
+ `backtrace` -- 印出所有的call stack
+ `step` -- 一行一行執行程式(會進入function)
+ `continue [c]` -- 繼續執行程式直到下個break point
+ `finish` -- 結束程式
+ `watch $var` -- 監看特定變數,會顯示該變數舊的值以及執行後的新值(與display差別在會有新舊的顯示)
+ `info breakpoints` -- 列印出所有的設定點
+ `delete $num` -- 移除break point或是watch point(如果不指定NUM ID, 則刪除所有設定點)
+ `whatis [what] $var` -- 會列印出型態
+ `target record-full` -- 執行後run的程式步驟可以透過reverse反轉步驟(?
+ `set var $var = ?` -- 可在程式執行階段修改參數值