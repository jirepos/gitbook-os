# Vi

Linux에서 VI 에디터의 사용자 설정을 하는 방법을 살펴본다.

사용자 홈 디렉토리에 '.vimrc' 파일을 만들고 다음을 입력한다.

```
set autoindent
set smartindent
set cindent
set number
set showmatch
set tabstop=2
set shiftwidth=3
syntax enable
syntax on
```

yaml 설정을 위한 추가사항

```
syntax on
autocmd FileType yaml setlocal ts=2 sts=2 sw=2 expandtab autoindent
```

