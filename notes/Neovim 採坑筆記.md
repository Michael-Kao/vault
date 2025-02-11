---
title: Neovim 採坑筆記

---

# Neovim 採坑筆記  

## Workspace  
- cmake workspace 在 build 時需加上 `-DCMAKE_EXPORT_COMPILE_COMMANDS=1` 才會透過 lsp load workspace  
    - e.g. `cmake -B build -DCMAKE_EXPORT_COMPILE_COMMANDS=1`  