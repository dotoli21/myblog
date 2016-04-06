---
layout: post
title:  "'vundle'로 vim plugin 관리하기" 
date:   2015-01-03 17:26:00
categories: tools
---
vi는 visual editor의 약자로 Unix terminal환경을 접해본 사람이라면 한 번쯤은 써 보거나 들어본 경험이 있을것이다.
Unix계열의 OS에서 에디터는 emacs와 vi가 사실상 양대산맥인데(여기에 동의하지 않는 사람도 있을 수 있겠다) emacs의 가장 큰 장점이 lisp언어를 사용하여 확장할 수 있는 반면 vi는 그렇지 못한 것으로 알고 있는 사람이 많은것 같다. 하지만 vi(구체적으로 말하자면 vim)도 emacs 못지않게 Plugin을 사용하여 기능을 확장하여 사용할 수 있는 방법이 있다.

현재는 오리지널 vi를 사용하는 OS는 거의 없다. 대부분의 배포판에서는 vim(Vi IMproved) 이라는 clone이 사용되고 있는데, vim 7.x 버전부터는 이전 버전과는 달리 vim 내부의 스크립트 만으로 플러그인을 만들 수 있는 환경이 갖춰지게 되어 많은 Plugin들이 쏟아지고 있는 상황이다.

[vim-scripts](http://vim-scripts.org/vim/scripts.html)을 방문하면 수천개의 Plugin들을 찾아볼 수 있는데, 이런 Plugin들을 손쉽게 관리해 주는 Plugin인 `Vundle`을 소개한다.

### Requirements
* Vim 7.x or higher
* git

### Vundle plugin 설치
##### 1. 아래 명령어를 Shell에서 실행한다.
```
git clone https://github.com/gmarik/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

##### 2. clone이 완료되면 아래 내용을 `.vimrc` 파일에 추가한다.
```vim
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'gmarik/Vundle.vim'

" The following are examples of different formats supported.
" Keep Plugin commands between vundle#begin/end.
" plugin on GitHub repo
Plugin 'tpope/vim-fugitive'
" plugin from http://vim-scripts.org/vim/scripts.html
Plugin 'L9'
" Git plugin not hosted on GitHub
Plugin 'git://git.wincent.com/command-t.git'
" git repos on your local machine (i.e. when working on your own plugin)
"Plugin 'file:///home/gmarik/path/to/plugin'
" The sparkup vim script is in a subdirectory of this repo called vim.
" Pass the path to set the runtimepath properly.
Plugin 'rstacruz/sparkup', {'rtp': 'vim/'}
" Avoid a name conflict with L9
"Plugin 'user/L9', {'name': 'newL9'}

" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
" To ignore plugin indent changes, instead use:
"filetype plugin on
"
" Brief help
" :PluginList       - lists configured plugins
" :PluginInstall    - installs plugins; append `!` to update or just :PluginUpdate
" :PluginSearch foo - searches for foo; append `!` to refresh local cache
" :PluginClean      - confirms removal of unused plugins; append `!` to auto-approve removal
"
" see :h vundle for more details or wiki for FAQ
" Put your non-Plugin stuff after this line
```

##### 3. 그리고 vim을 실행한 후 아래 명령어를 입력하여 `.vimrc` 에 선언된 Plugin을 설치한다.
```
:PluginInstall
```

### 참고자료
* *Wikipedia, 'vi', http://ko.wikipedia.org/wiki/Vi*
* *Github, 'gmarik/Vundle.vim', https://github.com/gmarik/Vundle.vim*
* *KLDP, 'vim 사용자를 위한 플러그인 매니저 vundle 을 소개 합니다.', https://kldp.org/node/125263*
