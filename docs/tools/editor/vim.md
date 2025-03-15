# vim

## 常用配置

配置文件为`~/.vimrc`，以下是一份简单的配置文件示例：

```shell
set nocompatible
set nu!
filetype on
set autoindent
set cindent
set smartindent
set tags=./.tags;,.tags
set ai!
set showmatch
set ruler
set hlsearch
set list
set cul
set tabstop=4
set shiftwidth=4
set backspace=indent,eol,start
set ww=h,l

" set cc=81
set cc=0

" set listchars=tab:→\ ,trail:·,space:·
set listchars=tab:→\ ,trail:·

let mapleader = ","
let g:mapleader = ","

syntax enable

" plug
call plug#begin('~/.vim/plugged')
Plug 'morhetz/gruvbox'
Plug 'vim-airline/vim-airline'
Plug 'scrooloose/nerdtree', { 'on': 'NERDTreeToggle' }
Plug 'neoclide/coc.nvim', {'branch': 'release'}
call plug#end()

" colorscheme
set background=dark
let g:gruvbox_contrast_dark='hard'
colorscheme gruvbox
set laststatus=2
let g:airline_extensions = []

nmap <leader>w :w!<CR>
nmap <leader>l :set list!<CR>
nmap <leader>d :NERDTreeToggle<CR>
nmap <leader>s :set tabstop=4<CR>:set shiftwidth=4<CR>:set expandtab<CR>
nmap <leader>S :set tabstop=8<CR>:set shiftwidth=8<CR>:set expandtab<CR>
nmap <leader>t :set tabstop=4<CR>:set shiftwidth=4<CR>:set noexpandtab<CR>
nmap <leader>T :set tabstop=8<CR>:set shiftwidth=8<CR>:set noexpandtab<CR>
nmap <leader>c :CocStart<CR>

nmap <C-]> g<c-]>

autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTree") && b:NERDTree.isTabTree()) | q | endif

" stop CoC by default (but Coc is enabled)
let g:coc_start_at_startup=0

" Append other coc-nvim configs
```

## LSP配置

经过一段时间的使用，coc-nvim的使用体验还是不错的，配合clangd，即使在嵌入式开发中也可以得到十分舒服的自动补全体验。

clangd、coc-nvim以及coc-clangd的安装可以查看官方网站的描述，文档十分详细，基本无脑按照流程来便可以了。

使用`:CocLocalConfig`可以在工程目录打开相应的配置文件进行编辑，这里在配置clangd指定工具链时踩到了一个坑，一开始直接使用`--query-driver=/path/to/toolchain`这样的参数时始终无法让clangd正常工作，直到将coc-clangd的参数按照如下形式进行配置才开始正常工作。

```json
{
    "clangd.disableDiagnostics": true,
    "inlayHint.enable": false,
    "clangd.arguments": [
        "--query-driver",
        "/path/to/toolchain/bin/xxx-gcc",
        "--compile-commands-dir",
        "build",
        "--header-insertion",
        "never"
    ]
}
```

函数自动补全时使用`CTRL+J`和`CTRL+K`可以在函数参数间进行切换；`space+s`可以全局搜索符号。

有时候会出现clangd检索失败的问题，这个现象可能会出现在开发单片机的时候，主要原因是存在clangd无法识别的abi参数，要解决这个问题，可以在源码目录新建一个`.clangd`文件，在里面填入以下内容删除无法识别的abi参数。

```
CompileFlags:
    Remove: [-march=*, -mabi=*, -mcpu=*]
```
