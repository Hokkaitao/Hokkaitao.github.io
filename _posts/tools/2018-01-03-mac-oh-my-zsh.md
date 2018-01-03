---
layout: post
published: true
title: "Macbook终端配置oh-my-zsh"
description: oh-my-zsh, macbook
---
## 安装

```
# 安装on-my-zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 安装zsh-autosuggestion
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

# 编辑～/.zshrc配置文件
找到plugins=(git zsh-autosuggestions)
```

## 配置zsh主题

```
# 主题修改参考：https://github.com/robbyrussell/oh-my-zsh/wiki/themes
编辑~/.zshrc配置文件，修改theme即可。
```

## 参考
- [Macbook下终端配置(oh-my-zsh和zsh-autosuggestion)](http://www.jyguagua.com/?p=2512)
