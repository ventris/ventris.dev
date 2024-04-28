" Minimal vimrc config with quality of life stuff

scriptencoding utf-8
set encoding=utf-8

syntax on

set termguicolors
set background=dark

set nocompatible
set ruler

set vb
set t_vb=

set list
set list listchars=tab:».,trail:·,extends:€,eol:$

set tabstop=4
set softtabstop=4
set shiftwidth=4
set autoindent
set expandtab

set number
set relativenumber

" maps
set mapleader = " "
set("n", "<leader>pv", vim.cmd.Ex)

" -- Move text when highlighted
set("v", "J", ":m '>+1<CR>gv=gv")
set("v", "K", ":m '<-2<CR>gv=gv")

" -- Allows the cursor to stay in place when you append lines to the line
set("n", "J", "mzJ`z")
" -- Keeps the cursor in the middle
set("n", "<C-d>", "<C-d>zz")
set("n", "<C-u>", "<C-u>zz")
" -- allows search terms to stay in the middle
set("n", "n", "nzzzv")
set("n", "N", "Nzzzv")

" -- greatest remap ever
set("x", "<leader>p", [["_dP]])

" -- next greatest remap ever : asbjornHaland
set({ "n", "v" }, "<leader>y", [["+y]])
set("n", "<leader>Y", [["+Y]])

set({ "n", "v" }, "<leader>d", [["_d]])

set("n", "Q", "<nop>")

set("n", "<C-k>", "<cmd>cnext<CR>zz")
set("n", "<C-j>", "<cmd>cprev<CR>zz")
set("n", "<leader>k", "<cmd>lnext<CR>zz")
set("n", "<leader>j", "<cmd>lprev<CR>zz")

set("n", "<leader>s", [[:%s/\<<C-r><C-w>\>/<C-r><C-w>/gI<Left><Left><Left>]])
set("n", "<leader>x", "<cmd>!chmod +x %<CR>", { silent = true })

