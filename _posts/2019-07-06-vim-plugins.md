---
layout: post
title: vim 插件
category: 技术
tags: vim editor coding
keywords: vim-plugin
description: vim plugin usage
---
* contents
{:toc}

## vim-run-interactive
``` bash
Bundle "christoomey/vim-run-interactive"
nnoremap <Leader>ri :RunInInteractiveShell<space>
```
use `leader + ri` to run shell commands, use `fg` to get back to vim

## rename
``` bash
Bundle "danro/rename.vim"
nnoremap <Leader>re :Rename<space>
```
use `leader + re` to change file name of current buffer

## ctrlp
```bash
Bundle "kien/ctrlp.vim"
```
use `ctrl + p` to trigger ctrlp to jump to any file / buffer; use `ctrl + f` or `ctrl + b` to switch between file / buffer / mru file

## vim-mkdir
``` bash
Bundle "pbrisbin/vim-mkdir"
```
use `e new_path/new_file` to mkdir and new file directly

## vim-fugitive
``` bash
Bundle 'tpope/vim-fugitive'
```
use `Gstatus, Gdiff, Gblame` etc to work with git.

## vim-surround
``` bash
Bundle 'tpope/vim-surround'
```
use `cs'"` to replace ' with ", use `ds'` to delete ', use `ys + (iw/s) + '` to add ' to word / all line, use `v` to select region, and `S + '` to and ' to selected region

## matchit.zip
``` bash
Bundle 'vim-scripts/matchit.zip'
```
use `%` to jump with matched labels like xml/html in addition to {}[]() etc.

## tComment
``` bash
Bundle 'vim-scripts/tComment'
```
use `gcc` to comment current line, `gc + k / j` to comment current and upper / lower line, use `gc + num + c + k / j` to comment num of upper / lower lines, `shift + v` select lines and comment it by `g>`, uncomment by `gc`

# tabular
``` bash
Bundle 'godlygeek/tabular'
nnoremap <Leader>tb :Tabular/
```
use `leader + tb` to trigger Tabular, use `/=` to align with =, use `/\/\+/r0c1l2` to algin with `\/+` and do something more complicated

## easy-motion
``` bash
Bundle 'Lokaltog/vim-easymotion'
```
use `s` to search, use `leader + leader + w / b / e / j / k / w / f(alpha)` to search

## ultisnips
``` bash
Bundle "SirVer/ultisnips"
let g:UltiSnipsExpandTrigger="<c-g>"
let g:UltiSnipsJumpForwardTrigger="<tab>"
let g:UltiSnipsJumpBackwardTrigger="<s-tab>"
```
use `ctrl + g` to trigger snips expand, use `tab` and `shift + tab` to switch with any parameters. snippets locate at `.vim/bundle/vim-snippets/snippets`

## vim-multiple-cursors
``` bash
Bundle 'terryma/vim-multiple-cursors'
```
use `ctrl + n` to trigger and select current word and then next, `ctrl + x` to omit current word, `ctrl + p` to previous word.

## previm & open-browser
``` bash
Plugin 'kannokanno/previm'
Plugin 'tyru/open-browser.vim'
nnoremap <Leader>pv :PrevimOpen
```
use `leader + pv` to trigger preview in browser.