---
title: Linux 小抄

---

# Linux 小抄  

## Output Redirection  
+ Create/Overwrite `>`  
    ```bash=
    # echo myhostname > /etc/hostname
    ```  
+ Append `>>`
    ```bash=
    # echo "[custom-repo]" >> /etc/pacman.conf
    ```    
## chmod  
+ make **TheScript** executable
    ```bash=
    chmod +x TheScript
    ```

## Source
+ Run the program to apply new changes in config  
    ```bash=
    $ source ~/.bashrc
    ```
    or  
    ```bash=
    $ . ~/.bashrc
    ```
## Reference  
+ https://wiki.archlinux.org/title/Help:Reading
+ [Source](https://superuser.com/questions/46139/what-does-source-do)