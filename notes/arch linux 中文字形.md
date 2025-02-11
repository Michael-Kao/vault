---
title: arch linux 中文字形

---

# arch linux 中文字形問題
## locale 設定
取消註解`/etc/locale.gen`中的`zh_TW.UTF-8`後執行`locale-gen`  

> `/etc/locale.conf`中的`LANG`變數是設定整體環境的，建議保留使用`en_US.UTF-8`  

## 中文字體安裝
~~`yay -S wqy-microhei`~~  
`yay -S tty-roboto`  
這東西能解決部分數字在 browser 無法渲染的問題。(xorg 環境下)  

> 其餘免費字體可參考[arch wiki](https://wiki.archlinux.org/title/Localization_(%E6%AD%A3%E9%AB%94%E4%B8%AD%E6%96%87)/Traditional_Chinese_(%E6%AD%A3%E9%AB%94%E4%B8%AD%E6%96%87))  

## 輸入法安裝
`yay -S fcitx5 fcitx5-configtool fcitx5-rime fcitx5-gtk`  
+ fcitx5 -> input method framework
+ fcitx5-configtool -> GUI Settings
+ fcitx5-rime -> input method editors (for customized input method)  

> [Reference](https://wiki.archlinux.org/title/Input_method#List_of_available_input_method_editors)

## 安裝客製化輸入法  
+ [Rime Onion Repo](https://github.com/oniondelta/Onion_Rime_Files)  
+ 下載後解壓找到適合自己的版本(注音plus)
+ 將版本資料夾中的所有檔案複製到`~/.local/share/fcitx5/rime`中  

## 設定輸入法  
+ 開啟`fcitx5-configtool`
+ 在輸入法選項找到Rime後啟用
+ 等待Rime Onion加載完即可使用

> `Ctrl + Space`可以切換輸入法  

## 進入Window Manager後自啟動
待補...