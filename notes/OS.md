---
title: OS

---

# OS 
[toc]

---
## A1-6
:::spoiler 特權指令(Priviledged Instructions)是甚麼？只能在何種模式下執行？為甚麼？
> def. 為了保護作業系統不被惡意使用者破壞，所以將那些會對作業系統造成傷害的指令作為特權指令

只能在kernel mode下執行。  
因為作業系統和使用者共用電腦的軟體和硬體資源，為了要確保錯誤的或是惡意的程式不會使其他程式不正常運行，所以必須要能區分作業系統的程式和使用者的程式差別。  
大部分電腦用來區分差別的方法是讓硬體提供多種模式來對應不同的程式，要達到上述條件**最少**需要兩種模式，分別是kernel mode跟user mode，透過新增mode bit就能辨別是哪種mode(kernel (0), user (1))。  
特權指令是為了避免使用者去操作到這些能對系統造成損害而設計的，所以這些指令只能kernel mode來執行。
:::

---

## A2-17
:::spoiler 請簡單說明 I/O Protection, Memory Protection, CPU Protection 的目的以及做法
![](https://i.imgur.com/eiXNot9.png)

1. I/O Protection
    + 為了避免使用者非法執行I/O指令來打斷系統操作
    1. 使用者請求執行I/O指令
    2. OS trap 切換到 kernel mode
    3. I/O subsystem執行指令
    4. I/O將結果回傳給 I/O subsystem
    5. I/O subsystem回傳結果給使用者並切回user mode
2. Memory Protection
    + 避免process存取不屬於他的記憶體
    + 需要使用到limit register 和 relocation register
    + 當CPU選擇一個process要執行時，dispatcher會載入limit register 和 relocation register作為部份的context switch，因為在這些register 內的 address都會被檢查過，藉此來保護系統。
     ![](https://i.imgur.com/qGUACkD.png)

3. CPU Protection
    + 用來避免使用者程序造成無窮迴圈，長時間占用CPU資源
    + 利用Timer，Timer有個固定的期間，每次clock觸發時Timer的值都會減少，當Timer值 = 0，Timer就會送出interrupt，此時控制權將回到OS上。
:::


## Chapter 6
* 本章重點
    * Race condition
    * Critical section

### Race condition
> 發生在共享記憶體、共享資料時
> 當有多個process在存取相同資料時，該資料的結果由process完成的順序決定，表示先前process的值會被覆蓋掉
    ![](https://i.imgur.com/CteC0hQ.png)
    圖中...虛線代表context switch發生，從2轉換到3表示process 1轉到process 2，此時在process 1的x是4，但並還沒有儲存至x中，然後context switch就發生了，接著3~5表示process 2的執行，要注意是這裡load x並非4而是3，因為process 1的結果還未儲存至x，所以當process 2結束時x的值是5，此時context switch發生，回到process 1，但此時暫存的x是4，並且process 1將其值存至x，所以x的結果會是4，但顯然結果要得是6而非4，這個狀況稱為Race condition。

### Critical Section
> 在multiple process的環境中，每個process有部分的code是共享資料的，稱為critical section。  
> 當有一個procee在執行critical section時，其他的process將不被允許執行critical section的片段
> ![](https://i.imgur.com/UdhYZK1.png)
圖中可看到Process 1已經先進入Critical section了，所以Process2 & 3只能在自己的Cirtical section外等待，直到不再有Process在Critical section內才能進入。

:::info
透過排隊的方式就能解決資訊不同步的問題，所以接下來的內容在於如何實作Critical section
:::

+ 基於中斷的解法
    + 當共享資料在被存取時就禁止中斷
    + 一旦結束存取後就開始中斷功能
    :::spoiler 問題:
    如果這個critical section需要跑一小時則會占用資源
    :::

### Peterson's Solution
+ software-based
+ 現在的架構上不保證能work
    + Load/Stroe instructions
    + Reordering(主因)
+ Two process share tow varialbe
    + int turn;
        + 代表現在是誰正在cirtical section中
    + boolean flag[2];
        + 代表誰現在想進入critical section

![](https://i.imgur.com/pTvuqBK.png)

:::spoiler 正確性
1. Mutual exclusion
    + 如果Process Pi在critical section中，則沒有其他process可以執行其critical section
2. Progress
    Pj要執行會遇到以下三個情況:  
    1. Pi不想進critical section
    2. Pi、Pj都想

    當沒有 process 要在 critical-section 中執行時，不能阻擋其他想要進入 critical section 工作的 process 進入 critical-section，要選擇其中一個候選 process 進入 critical-section，不能空在那邊。(延遲：postponed)
3. Bounded-Waiting(有限等待)
    + 等待 critical-section 的時間，不能是無窮大的時間，是有個界線。也就是說，不能佔住了critical-section 就不出來了。
:::

:::warning 
問題: 一樣無法解決context switch製造出的問題
將turn = j轉成組合語言，可以拆成兩步驟
1. LOAD j
2. STORE TURN

但是如果在這兩步驟中發生context switch，則會有turn不同步的問題
:::


### Hardware Support

+ Solution
    + Hardware instruction(test_and_set(), compare_and_swap)
        + 指令之間不會發生context switch(就像context swtich不會發生到一半又有context switch發生一樣)
    + Memory Barrier
    + Atomic Variables

#### Memory Barrier
+ 當執行memory barrier時，系統會確保當前所有的載入和儲存都完成了才會讓接下來的儲存載入操作進行
+ 可以解決reordering的問題
![](https://i.imgur.com/HnMshWX.png)

### Hardware Instruction