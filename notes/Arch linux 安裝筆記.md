---
title: Arch linux 安裝筆記

---

---
title: Arch linux 安裝筆記
date: 2024/01/31
---
# Arch linux 安裝筆記

+ check UEFI bitness
    > cat /sys/firmware/efi/fw_platform_size  
    > Output: 64 (代表為x64)  

+ 確認網路有連線(非wifi環境)
    > ping 8.8.8.8  

+ 更新時間
    > timedatectl  

+ 分割硬碟
    + `fdisk -l` 列出所有硬碟，目標是sd"x"或nvme"0n1"或mmcblk"0"的編號
    + `fdisk /dev/sda` sda為任意硬碟編號，選擇要安裝linux系統的那個
    + 進入fdisk系統
    + 如果該硬碟原本有partition，則使用d刪除partition
    + 新增partition則使用n
    + ![image](https://hackmd.io/_uploads/SJMm-wA5a.png)
    + linux主要partition只能有4個，分別是給boot、swap、root以及home
    + boot大小最少要300MiB、swap通常是記憶體容量的1.5~2倍，用於電腦休眠時儲存用，而root跟home分開則是為了避免kernel玩壞時要全部重灌，所以將其分開。
    + 可以用t更改區塊類別(標示用)
    + 設定好後w儲存並退出

+ `lsblk`查看硬碟內容
    ![image](https://hackmd.io/_uploads/rJ1AGPAqT.png)  
    切割好可看見個區塊的大小
    
+ 格式化分割區
    + For root dir `mkfs.ext4 /dev/sda3` 以上述例子我的root dir是sda3
    + For swap `mkswap /dev/sda2`
    + For EFI `mkfs.fat -F 32 /dev/sda1`

    ![image](https://hackmd.io/_uploads/rkjhQPR9p.png)  
    
+ 掛載分割區(mount)
    + For root `mount /dev/sda3 /mnt`
    + For EFI `mount --mkdir /dev/sda1 /mnt/efi`
    + For swap `swapon /dev/sda2`
    ![image](https://hackmd.io/_uploads/B1zhNPRca.png)
    最後利用`lsblk`查看mountpoints是否正確  
    
+ 安裝必要軟韌體
    + `pacstrap -K /mnt base base-devel linux linux-firmware grub networkmanager efibootmgr neovim vim`

+ fstab
    + `genfstab -U /mnt >> /mnt/etc/fstab`  

+ go into arch
    + `arch-chroot /mnt`

+ Update Time Zone
    + `ln -sf /usr/share/zoneinfo/Asia/Taipei /etc/localtime`
    + `hwclock --systohc`
    + `date`確認是否設定完成

+ localization
    + `vim /etc/locale.gen`
    + uncomment en_US*
    + run `locale-gen`
    + ![image](https://hackmd.io/_uploads/HyxgiwAqT.png)
    + `vim /etc/locale.conf`
    + type `LANG=en_US.UTF-8`
    + set LANG variable  

    > [arch linux 中文字形](/3g95IrktTN6s9b-UmolD3A)

+ Host name set up
    + `vim /etc/hostname`
    + type `Your pc name`

+ give root password
    + `passwd`

+ adding new user
    + `useradd -m -G wheel -s /bin/bash username`
    + add password
        + `passwd username`
    + add root privilege
        + `visudo`
        + uncomment wheel group can execute any command  

+ enable networkmanager
    + `systemctl enable NetworkManager`

+ grub install
    + `grub-install --efi-directory=/efi /dev/sda`
    + `grub-mkconfig -o /boot/grub/grub.cfg`
    + **dual boot need to configure later**

+ reboot !
    + exit to usb
    + `umount -a`
    + `reboot`

+ login with user
+ set up yay aur helper
    + `git clone https://aur.archlinux.org/yay.git`
    + `cd yay/`
    + `makepkg -si`
