---
tags:
- vi
title: "My Vim Configuration"
date: 2018-06-05T22:19:36+03:00
draft: true
---

Vim has thousands of plugins, scripts and addons and you can easily get lost on the net, trying to figure out which one suites you best,

But it can be simplified easily!

{{< asciicast OLwv3bwginqBjitU7COPe5vmg >}}

First, I suggest to use [Vim-Plug](https://github.com/junegunn/vim-plug) for easy plugin management,
Vim 8 supports plugin separation (out of the box) quite well but this tool make it even easier,

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

and here's a starting point of a clean `~/.vimrc`

```bash
" Plugins will be downloaded under the specified directory
call plug#begin('~/.vim/plugged')
" Declare the list of plugins.
" List ends here. Plugins become visible to Vim after this call.
call plug#end()
```

Here are my favorite plugins

```
" top/bottom status bar
Plug 'bling/vim-airline'
" make airline understand git
Plug 'airblade/vim-gitgutter'
" for git
Plug 'tpope/vim-fugitive'
" dark theme
Plug 'dracula/vim', { 'as': 'dracula' }
" support fuzzy search
Plug '/usr/local/opt/fzf'
Plug 'junegunn/fzf.vim'
" snippet support
Plug 'SirVer/ultisnips'
" json formatted
Plug 'elzr/vim-json'
" golang
Plug 'fatih/vim-go', { 'do': ':GoUpdateBinaries' }
```

let g:airline#extensions#tabline#enabled = 1
colorscheme dracula

Just run `PlugInstall` in normal mode and all plugins will be installed silently!


# Refs
https://github.com/junegunn/dotfiles/blob/master/vimrc
