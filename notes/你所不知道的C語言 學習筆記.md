---
title: 你所不知道的C語言 學習筆記

---

# 你所不知道的C語言 學習筆記  
> 本筆記的目的是為了幫助自身對Jserv的課程文章理解所寫的補充說明,補充的來源會包括網路上的文章以及ChatGPT等大型語言模型的內容,內容會盡可能的精準正確,如文章中有解釋錯誤或是引用錯誤等問題還請聯繫我修正：`music0227@gmail.com`  

[規格書](https://www.open-std.org/jtc1/sc22/wg14/www/docs/n1256.pdf)(PDF)  

## [指標篇](https://hackmd.io/@sysprog/c-pointer)  
+ `&` -- address-of operator `C99 [2.5.3.2]`  
+ object -- 執行期間儲存資料的區域`C99 [3.14]`  
    + pointer type 可透過 function type, object type, incomplete type 衍生(derived from)而來  

+ Types `C99 [6.2.5]`
    + function type, object type, incomplete type通稱為參考型別(referenced type)  
    + pointer type 提供存取(?)參考型別之值的入口  
    + pointer type derived from referenced type T = "pointer to T"  
    + construction of pointer type = "pointer type derivation"  
        + ```c
            int tmp = 5; // int is referenced type
            int *ptr = &tmp;
            // ptr is a pointer to int (pointer type derivation)
            ```    
    + C語言只有call-by-value (I don't know why)  
    + arithmetic type 和 pointer type 被稱為 scalar type  
        + `++`, `--`, `+=`, `-=` 等操作都是對 scalar type  
    + array 和 structure type 被稱為 aggregate type  
    + 未定義大小的 array type 是 incomplete type  
    + 未定義的 structure/union type 也是 incomplete type
        + C/C++ 的 forward declaration, 因為未定義, 所以是 incomplete type, 無法建立實體(instance), 卻可以使用指標表示  

+ array, function and pointer types 通稱為 derived declarator types  

(**練習題**) 設定絕對地址為 `0x67a9` 的 32-bit 整數變數的值為 `0xaa6`，該如何寫？  
```c=
*(int32_t *const)(0x67a9) = 0xaa6;
// 0x67a9原本為 void *
// 先將其轉型成 32-bit int pointer
// 最後取值並修改為0xaa6
// 其中const表示左側的敘述(?)不改變
// 即0x67a9為int32_t之型態的指標不能改變
```  
+ A pointer to void shall have the same representation and alignment requirements as a pointer to a character type. 
    + `void *` 和 `char *` 彼此可以互換
        + ex:`void *memcpy(void *dest, const void *src, size_t n);` 字串處理的函式在規格書中使用的卻是void  

+ 從 `void *` 讀取 uint16_t 時為了對應 alignment 所需的操作  

    ```c=
    #include <stdio.h>
    #include <stdint.h>

    int main() {
        int v = 0x1234;
        void *ptr = &v;

        uint16_t pvalue = *(uint16_t *) ptr;
        // 將 ptr 看成 8-bit pointer, 先取出 0x34 (即 *(uint8_t) ptr), 
        // 再從 ptr 的下個 index(ptr + 1) 取出 0x12(*(uint8_t) (ptr + 1)) 後將其左移 8 個 bits 至前 8 bits, 
        // 最後 bit-wise or 得到 16-bits value
        uint16_t value = *(uint8_t *) ptr | ((*(uint8_t *) (ptr + 1)) << 8);

        printf("Value: 0x%04x, 0x%04x\n", pvalue, value); // Output will be 0x1234

        return 0;
    }
    ```
> :question: 為什麼 0x1234 拆成 8-bits 後在記憶體中是儲存成 `uint8_t ptr[] = { 0x34, 0x12 }` 呢 => 請參考[記憶體管理、對齊及硬體特性](https://hackmd.io/@sysprog/c-memory)  

+ 如果 pointer 要在不同 type 之間轉換, 則轉換前後的結果必須相同, 若兩者轉換的型態不相容 (not compatible `C99 [6.7.5.3 (15)]`) 則該行為為 undefined behavior (UB) `C99 [6.3.2.3 (8)]`  
    +  `int` 轉 `long long` 因結果相同 => 轉換沒問題  
    +  `struct my_struct *` 轉 `void *`  因表示方式跟 alignment 不相同, 所以定義上是 not compatible => undefined behavior -- [參考](https://stackoverflow.com/questions/559581/casting-a-function-pointer-to-another-type)  


+ C語言中只有 ==call-by-value==  
    + 若需從 Function call 修改 pointer 則需使用 ==指標的指標== 作為輸入  

## Points vs. Arrays  
+ in declaration  
    + extern `extern char x[];` 不能轉為 pointer 形式  
    + definition/statement `char x[10]` 不能轉為 pointer 形式  
    + parameter of function `func(char x[])` 可以轉成 `func(char *x)`  

+ in expression  
    + array 與 pointer 可互換  

```c=
int main() {
    int x[5] = {0, 1, 2, 3, 4};
    printf("%d %d %d %d\n", x[4], *(x + 4), *(4 + x), 4[x]);
}
```

>[!Note] 
> `x[i]` 總是被編譯器改寫為 `*(x+i)`  

+ C語言提供多維陣列，但實際上只有一維的資料存取 `C99 [6.5.2.1] Array subscripting`  
    + `int x[3][5];` 表示 x 是一個大小為 3 且儲存一個大小為 5 且型態是指向 int 的陣列，表示法中 `x[i]` 等同於 `(*((x) + (i)))` 且值為大小為 5 的陣列起始位址，而 `x[i][j]` 則取出一個 int 值  

    > 在上述 `int x[3][5]` 的範例中只能得知 x 是一段連續的記憶體，無法準確計算位址的原因是各個 compiler 有不同的實作方式，必須知道該 compiler 是 `row major` 還是 `column major` 才能計算。  

+ array subscripting 在**編譯時期**只確認兩件事：  
    + 得知 size  
    + 取得陣列第0個元素的指標  

+ 兩者以外的操作只能透過 pointer  
    + array subscripting => syntax sugar  

+ Array declaration:  

```c=
int a[3];
struct {double v[3]; double length;} b[17];
int calendar[12][31];

int main() {}
```
問：  
```c=
sizeof(calendar) = ?
sizeof(b) = ?
```  

> calendar = 12 * 31 * 4bytes
b = 17 * (8bytes * 3 + 8bytes) = 17 * 4 * 8bytes [name=Michael Kao]  

![result](https://hackmd.io/_uploads/rkGMU4Xc0.png)  

使用GDB，避免 `printf()` 格式輸出錯誤而誤以為自己程式寫錯的狀況發生。  
:::info
不清楚指令時可在GDB中使用 `help` 查詢，例如不懂 `x` => `help x`。  
:::
+ 進行compile `gcc -Og -g main.c`  
+ 對 main 下中斷點 -- `b main`  
+ 執行 `r`  
+ ![example](https://hackmd.io/_uploads/r1hIU47q0.png)  
+ 利用 `whatis` 查看型態  
    + ![image](https://hackmd.io/_uploads/SkLKI4QcC.png)  
    + ![image](https://hackmd.io/_uploads/r1vhUEmc0.png)  

+ 使用 `x/FMT ADDRESS` 來顯示記憶體內容  
    + 將 b 的記憶體內容拆成每 4 個部分顯示: ![image](https://hackmd.io/_uploads/BkdFA4Qq0.png)  
        + `&b == &b[0] == 0x555555558620`  
    + 對 `b` 的記憶體位置 `+1` 後得新的位置  ![image](https://hackmd.io/_uploads/BJhxyBQcR.png) 可以看見兩者相差 `0x220` 將其轉為 decimal 後可得 `0x220 = 544` 即 `sizeof(b)`  
    + 若要找到 `array b` 中的第一個 value ，則需使用 `&(b[0]) + 1`  ![image](https://hackmd.io/_uploads/ryBRyBXcR.png) 可見兩者相差 `0x20 = 32 = sizeof(b[0])`  

+ 使用GDB修改記憶體內容  
    + 

