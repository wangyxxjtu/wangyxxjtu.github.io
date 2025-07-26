---
title: 'Bash Command'
date: 2025-07-26
permalink: /posts/2012/08/blog-post-4/
tags:
  - cool posts
  - category1
  - category2
---


###  1 在当前文件夹及其子文件夹下查找文件 
``` bash
find . -type f -name *vocab* 
```
上述的命令在当前文件夹及其子文件夹下查找包含vocab的文件
find 后面可以加目录，不写默认为当前目录
type换成d为查找目录

###  2 关于SCP命令 
使用方式为：
``` bash
scp local_file_dir usename@ip_address:remote_dir
```
第一个参数为本地的文件名，第二个参数为 先输入用户名，@之后输入IP地址或者url，远端的文件名
例如从本地mac往UTS机器上船文件
``` bash
Scp vocabulary.json yaxiwang@cluster.eng.uts.edu.au:~/Data/
```
如果传输的是文件夹，scp 后面加上-r选项

从远端传输文件到本地也可以使用scp命令
例如：
``` bash
scp yaxiwang@cluster.eng.uts.edu.au:~/Data/Flickr30k/data/f30k_precomp_vocab.json ./   
```
如果路径没错还是报 no such file错误，尝试将～换成绝对路径如 /home/yaxiwang

###  3。间隔指定时间将命令的执行结果写入到文件中 
例如每隔2秒将内存的使用信息写入指定的文件
``` bash
While true
do
	free -h > memory.out
	sleep 1
done:
```

###  查看当前用户的内存限制使用量 
``` bash

ulimit -a
```

### 4 列出文件夹下的所有文件并按照从大到小排序
``` bash
du -sh * | sort -nr
```
其中n表示按照数值大小进行排序，r表示反序即由大到小排序

###  5 xargs 命令相关 
Xargs是给命令传递参数的过滤器，也是组合多个命令的一个工具
 1）当你尝试删除太多文件，会得到一个错读：Argument list too long，可以使用xargs命令解决：
  ``` bash
  Find ./ -name'*.log' -print0 | xargs -0 rm -f
  ```
  这里print0是有必要的，xargs以空白字符来分割记录（空格，tag，换行等），如果文件名为‘file 1.log’，那么。xargs会解析为两个文件file 和1.log，但是这两个文件不存在，就会报错。print0在find命令打印文件名之后输出一个NULL，然后-0 告诉xargs用NULL来分割就不会报错了.
  2)查找所有的jpg文件并且压缩
  ``` bash
  Find / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz
  ```

  3)拷贝所有图片到一个外部驱动
  ``` bash
  ls . *.jpg | xargs -n1 -i cp {} /external-hard_drive/dir
  ```
  这里n1表示每次传入的argument数量是1，i 参数表示使用前面的输出替换 {}, 
  或者
  ``` bash
  ls . *.jpg | xargs -n1 -I {} cp {} /external-hard_driver/dir
  ```

  4)查找文件中是否按右某个单词test-word
  ``` bash
  Find . -type f -print | xargs grep 'test-word'
  ```

  * xargs命令格式
   ```
     Some command | xargs -item command
     常用参数
     -n 后面紧跟数字，表示执行command的时候，所用的前面穿过来的argument个数
     -p 每次 执行询问用户
     -I/I 将xargs的名称一行行赋值给{}，
     -d 后面紧跟一个字符，表示自定的分割符来区分不同的参数（前面传过来的），xargs默认分割符为回车，
     -t 表示执行前先打印
   ```

### tmux 常用命令 
  1）新建与杀死会话
  ``` bash
  tmux new -s session
  Thus kill-session -t 会话名称
  ```
  2）会话相关操作
  ``` bash
  tmux ls #列出所有会话 第一列名称，第二列窗口编号
  tmux a #进入最近的会话
  Tmux a -t 会话名 #进入指定的会话
  Windows/command 键 + 上/下箭头 #上/下翻
   ```
  
  3）tmux命令模式
  利用control + b进入tmux会话模式，此模式的响应输入命令有：
  ``` bash
  c #创建新窗口,
  p #切换到上一个window
  n #切换到下一个window
  w #查看所有窗口
  “ #横分当前面板
  % #竖分当前面板
  方向键/o #在分割的面板之间切换, 安住ctrl + b o，切换面板
  , #修改窗口名称
  ？#查看所有命令
  d #刮起当前会话
  s #查看所有会话
  $ #修改会话名称
  q #显示面板号，快速输入面板号可以选择相应面板
  & #关闭当前窗口
  x #关闭当前面板
  f #在当前窗口打开的所有文件中进行搜索
  d #暂时离开当前session
  ; #切换到最后一个使用的面板
  l #切换到最后一个使用的窗口
  z #在最大化和初始之前切换  
  ```
   bash界面中上下翻页：ctrl + a pageUp pageDown
   有时候，外接键盘的fn+方向键上下翻页不管用，解决方案：
   先ctrl+q, 按【进入拷贝模式，之后按page up/down去翻页
   
 具体在使用时，创建一个session，记住名称，在退出退出执行时，ctrl +b -》d提交到后台，重新连接时，tmux a -t session名称/tmux a进入最近的会话

 ###  vim中统计字符串出现的次数
 ``` bash
 :m，ns/\<string\>//gn#统计m-n行中字符串string出现的次数
 :%s/string//gn#统计当前编辑的文件中string出现的次数
 ```
字符串替换
``` bash
 :%s/old/new/g #直接将旧字符串替换为新字符串
 :%s/old/new/gc #替换每次需要你确认
```
统一缩进多行：
``` bash
:line_num1, line_num2 </> #按下enter即可向左/右缩进一个tab
#第二中方法
进入visual模式，选中要缩进的行shift + </> 向左/右缩进一个tab

#多窗口编辑
Ctrl w + | #水平最大化当前窗口
Ctrl w + _ #垂直最大化当前窗口
Ctrl w + = #将所有窗口设置为同等大小
```

 或者cat命令也可
  ``` bash
  Cat file | grep -I 字符串 | wc -l
  grep (-rinc) 字符串（可以是正则表达式） filename #查找包含字符串的行
  ```
  参数n表示输出结果并显示行号，c表示统计结果的出现次数， r表示在目录下递归查找，使用此参数，filename需要换成目录， i表示忽略大小写

### 是用grep查找文件夹下包含指定字符串的文件 
``` bash
 grep '字符串' ./ -Rnl #参数R表示便利所有子目录，n显示匹配的行号，l表示显示文件名称，加会覆盖n的效果不会显示行，只会显示文件名称，所以有l参数时可以去掉n
 grep -v '字符串' ./ -Rl # 表示不包含字符串的文件
 #查找多个字符串
 grep '字符串1' ./ -R | grep '字符串2' ./ -Rl
```
参数也可以放在grep命令后边

### vim使用indent代码折叠 
一下快捷键在缩进处使用
 ``` bash
 zc #折叠
 zC #对所在范围内的嵌套的折叠点进行折叠
 zo #展开折叠
 zO #对所在范围内的嵌套的折叠点进行展开
 [z #到当前折叠点的开始出
  z] #到当前折叠点的开始出
 zj #向下移动到下一个折叠处的开始
 zk #向上移动前一折叠的结束
 za #折叠打开和关闭之间切换
 zM #全文折叠
 zR #全部打开折叠
 zd #删除光标下的折叠
 zD #删除光标下的折叠以及嵌套的折叠
 ```
  
### 文件查看命令 
 查看有多少行数据：
 ``` bash
 wc -lw filename #查看文件有多少行（l）和字数（w）
 ```
 查看文件前几行/后几行
 ``` bash
 head -n line_num file_name #查看文件的前几行
 tail -n line_num file_name #查看文件的后几行
 Ls -Slh | head -n 5 #只显示结果的前5个
Grep 'Image to text' train.log | head -n 4 #显示前4个带有‘Image to text’字符串的行
 ```

### 远程登录脚本 
在ssh脚本登录的时候，避免每次输入密码
``` bash
 #!/usr/bin/expect
  set timeout 30
  spawn ssh -l username ip_adress
  expect "*password:"
  send "your password"
  interact
```
写完之后保存为文件，注意第一行。将改文件授可执行权限
``` bash
 chmod +x filename.sh
```
之后执行
``` bash
 ./filename.sh
```
注意不要使用sh filename.sh进行运行，否则会报错


### 下载数据好用多线程工具 
工具1：aria2
安装：
```
brew install aria2 #for Mac
sudo apt-get install aria2 #for ubuntu
```
断点续传下载数据：
 ``` bash
 aria2c -c url #-c表示断点续传, 注意是aria2c
 ```

工具2: axel
Linux下多线程下载工具
``` bash
sudo apt-get install axel
```
使用方法：
axel [options] url1 url2 ...
常用的选项：
``` bash
--max-speed=2 / -s x #指定最大的下载速度，单位为bite
--num-connections=x / -n x #指定最大的链接线程数为axel
--output=x / -o x #下载下来的文件重新命名为x
--search=x / -s=x #使用文件搜索引擎来找找文件镜像 （不太懂）
--no-proxy / -N #不使用代理服务器下载文件
--verbose  #输出更多的状态信息
--quiet -q #不想标准输出输输出信息
--header=x / -H x #添加HTTP的头域
--alternate -a #显示进度条
--user-agent=x / -U x #指定user agent
--help -h #显示一些帮助信息
--version / -V #显示版本信息
```

###  wget 下载文件断点续传 
 ``` bash
 wget -c -t 0 -O new_name.tar.gz http://www.cnscn.org/old_name.tar.gz 
 ```
 -c 断点续传
 -t 链接断开重试次数， 0为不限次数
 -O 重命名文件
Wget 命令的其他参数
``` bash
wget [参数列表] [目标软件、网页的网址]

-V,–version 显示软件版本号然后退出；
-h,–help显示软件帮助信息；
-e,–execute=COMMAND 执行一个 “.wgetrc”命令

-o,–output-file=FILE 将软件输出信息保存到文件；
-a,–append-output=FILE将软件输出信息追加到文件；
-d,–debug显示输出信息；
-q,–quiet 不显示输出信息；
-i,–input-file=FILE 从文件中取得URL；

-t,–tries=NUMBER 是否下载次数（0表示无穷次）
-O –output-document=FILE下载文件保存为别的文件名
-nc, –no-clobber 不要覆盖已经存在的文件
-N,–timestamping只下载比本地新的文件
-T,–timeout=SECONDS 设置超时时间
-Y,–proxy=on/off 关闭代理

-nd,–no-directories 不建立目录
-x,–force-directories 强制建立目录

–http-user=USER设置HTTP用户
–http-passwd=PASS设置HTTP密码
–proxy-user=USER设置代理用户
–proxy-passwd=PASS设置代理密码

-r,–recursive 下载整个网站、目录（小心使用）
-l,–level=NUMBER 下载层次

-A,–accept=LIST 可以接受的文件类型
-R,–reject=LIST拒绝接受的文件类型
-D,–domains=LIST可以接受的域名
–exclude-domains=LIST拒绝的域名
-L,–relative 下载关联链接
–follow-ftp 只下载FTP链接
-H,–span-hosts 可以下载外面的主机
-I,–include-directories=LIST允许的目录
-X,–exclude-directories=LIST 拒绝的目录

原文：https://blog.csdn.net/richardysteven/article/details/4565931 
```

### docker相关命令
``` bash
pytorch数据交互准备-挂载
sudo nvidia-docker run --ipc=host -it -v /media/tensorflow/1412C30112C2E738:/home/wangyx --name wangyx_docker ufoym/deepo bash
or
sudo nvidia-docker run -it -v --shm-size /home/smiles/jhy:/home/jhy -name jhy_deepo ufoym/deepo bash

挂载
sudo nvidia-docker run -it -v /media/smiles/OS1/jhy/:/home/jhy --name jhy_deepo d35aa33f77cb bash
启动
sudo nvidia-docker start -i jhy_deepo
删除
sudo docker container rm jhy_deepo / docker rm 容器id（docker ps -a查看容器id，如果docker正在运行，加上-f选项参数，强制移除）
查看容器
sudo docker ps -a

查看本地有哪些可用的镜像
docker images

删除镜像
docker rm -r id or name

查看image 有多大
docker system df -v | grep my_env

在HUB中搜索可用的镜像
docker search image_name (caffe for example)

拉取HUB中的镜像到本地
docker pull image_name (mtngld/caffe-gpu, for example)
Docker查看要拉取的镜像版本：https://hub.docker.com/r/mapler/caffe-py3/tags?page=1&ordering=last_updated

重新开启一个容器，与原先不同步：
nvidia-docker exec -it wangyx /bin/bash/ 
```
cp resnet50-19c8e357.pth /root/.torch/models
/media/tensorflow/1412C30112C2E738

my environment name: wangyx_docker (sudo nvidia-docker start -i wangyx_docker) 

### docker运行设置设置共享内存
如果共享内存不足，将会出现错误：dataloder worker exited unexpectedly，或者调小batchsize，或者重新进入docker使用上面的参数
解决办法只能是重新新建容器，因为默认容器的共享内存是64M
``` bash
nvidia-docker run -itd --shm-size 64G --name wangyx -v /data1/wangyx:/home/wangyx -p 3316:22 pytorch/pytorch(本地镜像：docker images查看本地存在哪些镜像)
```

### docker 保存容器并重新加载
```bash
#假设你的一个容器环境名字为：yaxiong
step 1: 打包
docker commit yaxiong my_env:v1

step 2:将该环境保存在为文件
docker save -o my_env.tar my_env:v1

Step 3: 加载镜像
docker load -i my_env.tar

Step 4:依托该镜像，建立容器
Nvidia-docker run -it -name yaxiong -p 5019:5019 -v

```

八卡服务器连接：
ssh wangyx@192.168.100.22
psw:gansu320

容器名称：wangyx
启动：nvidia-docker start -i wangyx
退出：ctrl+P+Q 退出但不结束 ctrl+D / exit 退出并关闭

### zip命令解压文件到指定文件 
``` bash
unzip -d dir file.zip
```
tar文件解压到指定文件夹
``` bash
tar -zxvf ./tarfile.tar -C adir
tar -zcvf ./tarfilename.tar ./adir # 压缩

#-----------------------------------
tar 解压缩命令 
tar 
-c: 建立压缩档案 
-x：解压 
-t：查看内容 
-r：向压缩归档文件末尾追加文件 
-u：更新原压缩包中的文件 
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。 
-z：有gzip属性的 
-j：有bz2属性的 
-Z：有compress属性的 
-v：显示所有过程 
-f 指定压缩文件文件名称
-O：将文件解开到标准输
```

### python调试 
倒入pdb之后去调试可能更加方便
``` bash
import pub

#在任何想调试的地方
pdb.set_trace()
```
代码运行到trace处自动进入调试状态，pdb调试命令即可使用,所以不用从程序入口开始调试

 ### tmux设置快捷键为ctrl+a 
``` bash
vim ~/.tmux.conf
#输入
unbind C-b
set -g prefix C-a
 
bind C-a send-prefix
 
bind r source-file ~/.tmux.conf
```
保存退出即可生效

### vim常用设置 
``` bash
set nocompatible
set number
syntax on
set autoindent
set smartindent
set tabstop=4
set shiftwidth=4
set hlsearch
set softtabstop=4
set tabstop=4
set shiftwidth=4
set expandtab=4

set showmatch
filetype on
set ruler

set incsearch
set cursorcolumn
hi cursorcolumn cterm=none ctermbg=black ctermfg=green
autocmd WinEnter * setlocal cursorline
autocmd WinLeave * setlocal nocursorline
highlight CursorColumn guibg=#303000 ctermbg=10
set cursorline
set scrolloff=5
hi cursorline cterm=none term=none
autocmd WinEnter * setlocal cursorline
autocmd WinLeave * setlocal nocursorline
highlight CursorLine guibg=#303000 ctermfg=White ctermbg=Black
hi Search term=standout ctermfg=0 ctermbg=3

set nocompatible       " be iMproved, required
filetype off         " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'
Plugin 'Valloric/YCM'
Plugin 'skywind3000/asyncrun'
Plugin 'vim-scripts/zoomwin'
" Keep Plugin commands between vundle#begin/end.
" 安装其它插件
" All of your Plugins must be added before the following line
Plugin 'preservim/nerdtree'
call vundle#end()      " required
filetype plugin indent on  " required

let g:ycm_global_ycm_extra_conf = '/root/.vim/bundle/YCM/.ycm_extra_conf.py'
"开启基于tag的补全，可以在这之后添加需要的标签路径
""let g:ycm_collect_identifiers_from_tags_files = 1
""开启语义补全
let g:ycm_seed_identifiers_with_syntax = 1
"在接受补全后不分裂出一个窗口显示接受的项
""set completeopt-=preview
""不显示开启vim时检查ycm_extra_conf文件的信息
let g:ycm_confirm_extra_conf=0
"每次重新生成匹配项，禁止缓存匹配项
""let g:ycm_cache_omnifunc=0
""在注释中也可以补全
let g:ycm_complete_in_comments=1
"输入第一个字符就开始补全
""let g:ycm_min_num_of_chars_for_completion=1
""不查询ultisnips提供的代码模板补全，如果需要，设置成1即可
let g:ycm_use_ultisnips_completer=0

let g:ycm_show_diagnostics_ui = 0
let g:ycm_server_log_level = 'info'
let g:ycm_min_num_identifier_candidate_chars = 2
let g:ycm_collect_identifiers_from_comments_and_strings = 1
let g:ycm_complete_in_strings=1
let g:ycm_key_invoke_completion = '<c-z>'

noremap <c-z> <NOP>

"let g:ycm_semantic_triggers =  {
"                   \ 'c,cpp,python,java,go,erlang,perl': ['re!\w{2}'],
"            \ 'cs,lua,javascript': ['re!\w{2}'],
"            \ }

map <F3> :NERDTree<cr>
map <F2> :AsyncRun
map <F1> :copen<cr>
map <F4> :ZoomWin<cr>

##与YCM等的安装配合使用
#---------------------------------------------------------
#使用map设置快捷键
map <F8> gt #f8映射到gt， 打开的多标签中进入下一个标签
map <F7> gT #f7映射为gT，打开的多标签汇总进入上一个标签
map n :bn<cr> #n映射为normal模式下的":bn+enter"，多文件中操作红移动到下一个
Map p :bp<cr> #p映射为normal模式下的 “:bp+enter”,多文件操作中的移动到上一个文件
Unman gt #取消对gt的绑定

<k0> - <k9> 小键盘 0 到 9 
<S-...> Shift＋键 
<C-...> Control＋键 
<M-...> Alt＋键 或 meta＋键 
<A-...> 同 <M-...> 
<Esc> Escape 键 
<Up> 光标上移键 
<Space> 插入空格 
<Tab> 插入Tab 
<CR> 等于<Enter>
```
### from scipy.misc import imead错误No module named imread
如果是python2 安装PIL python3 安装pillow，如果都做了还是不行，可能是pillow 和scipy的版本不一致导致
解决方案：卸载当前的scipy和pillow，重新安装
``` bash
Pip install scipy==1.1.0
Pip install pillow==6.0.0
```

### bash中的日常使用的快捷键 
``` bash
ctrl+w 删除最后一个词
Ctrl + xx 在行首和光标之间快速切换
ctrl + y 粘贴之间的删除的东西
ctrl+u 删除从光标处到行开头的内容
ctrl+k 删除光标到行末尾的内容
ctrl+e 定位光标到末尾
ctrl+a 定位光标到开头

Alt + 方向左右键 向/前后移动一个字
ctrl + _ 撤销之间的操作
alt+. 环形遍历之前的命令
alt+# 另当前的行失效，将跳过键入的命令，让你重新输入


history 查看历史命令
!! 运行上一个命令
history | grep your_prefix
会出来历史命令序号
!!序号  #可以重新运行代码

w/uptime  查看机器u已经运行了多少天
～/.bash_profile 其中可以写当你登录机器的时候要运行的命令，如希望登录的时候链接某个docker，则写入Nvidia-docker attach wangyfian2
alias 使用其创建快捷键，例如list ll='ls -latr'
#自定义传入参数
alias hist='history | grep $1' #~/.bash_profile, $1表示第一个参数
```

### tmate神器 告别vpn
使用tmate可以基于密匙认证，链接内网vpn，以mac和在内网中的服务器为例
step 1:在Mac上安装tmate
``` bash
Brew install tmate

```
Step 2:生成密匙文件
``` bash
Ssh-keygen -t rsa #一路按enter 生成文件id_rsa （～/.ssh/id_rsa）
```
Step 3:在Linux服务器上记录step2密匙
``` bash
#新建 /root/.ssh/authorized_keys
将step2中的id-rsa文件中的所有内容粘贴到 authorized_keys中并保存
```
Step4:服务器授权
在Linux服务器上为文件夹./ssh和文件authorized_keys改变权限
``` bash
Chmod 600 /root/.ssh/authorized_keys
Chmod 700 /root/.ssh
```
step 5:链接
在Linux服务器上运行tmate：
``` bash
tmate
```
开出回话，将首先链接tmate服务器，之后会在屏幕下方显示tmate id （形如ssh adfadfdfasf），复制改id，在Mac的bash中输入ssh + id即可链接(如果太快没有复制到，用tmate show-messages即可出现负值@tmate对应的命令)
 
可能的错误
``` bash
Public key (permission denied)
```
将上述机器位置对调再做同样的过程（成功案例是将服务器和mac上都进行了操作）

### 在vim中空格与tab键的替换 
某些时候vim中空格和tab并不匹配，比如文件中是四个空格表示缩进，而你想要是一个tab为四空格缩进，这种情况下需要相互的转化
四个空格转换为一个tab,在需要转换的文件中依次输入：
``` bash
:set ts=4
:set noexpandtab
:%retab!
```
一个tab转换为四个空格类似
``` bash
:set ts=4
:set expand tab
:%retab!
```
### python设置输出提示信息 
``` bash
Import os

Os.environ['TF_CPP_MIN_LOG_LEVEL'] = '3'
0---输出所有信息
1---屏蔽通知信息
2---屏蔽通知信息和警告信息
3---屏蔽通知信息，警告信息和报错信息
```

### 调用numpy函数出现错误：segmentation error （core dump） 
检查numpy版本，例如升级numpy版本
``` bash
pip install numpy --upgrade
```

### nvidia-smi not found, 但是程序可以使用gpu 
路径未配置好,安装的驱动在/usr/local/nvidia下，配置path变量
``` bash
Export PATH=/usr/local/nvidia/bin:$PATH
```

### 利用zmail发送电子邮件
环境需要package：email， smtplib和zmail均可通过pip install 安装，其中email和smtplib可能是python自带的（注意执行程序的目录下不要email.py 文件，否则导入email回出错）
准备需要：打开发送邮箱的pop3/smtp服务
``` bash
import zmail
#发送带附件的邮件
def send_zmail(path):
  #path is the attachment path
  mail_content = {'subject':'a subject string',
                 'content_text': 'a content string',
                  'attachments':path}
  server = zmail.server('senderxxx@qq.com', '授权码')
#这里的授权吗不是邮箱密码，而是打开发送邮箱pop3/SMTP服务时给的授权码,小号授权码是：rtztpbqrnyqmcjjd
#牛小号：授权码：fmzpsgtqoajhbbcg
server.seed_mail(['targetxxx@qq.com'], mail_content)
```
#接受邮件的附件
``` bash
Import zmail
server = zmail.server('xxxxxxx@qq.com', '授权码')
mail = server.get_latest()#得到最新的邮件
attachment = mail['attachments']#得到邮件的附件
name, content = attachment[0]# 分别得到邮件的名称和内容
with open(name, 'wb') as f:
     f.write(content)
```

### mac的一些常用操作命令 
``` bash
1,open:打开文件、目录或者执行程序，相当于图形界面下的双击操作
open my.docx#将会使用默认关联程序打开
选项-e: 强制在TextEdit中编辑次文件

2，pbcopy和pbpaste：命令行与剪切板，等同于鼠标的复制粘贴操作
ls . | pdcopy #复制当前文件夹下的所有文件
Pbpaste < test.file #将文件的内容复制到剪切板
pbpaste > test.file1 #将剪切板上的内容粘贴进文件里

3，say命令：让Mac说话
say 'All works is done!'

4, mdfind命令：spotlight的命令行模式
mdfind -onlyin ~/Document essay #在document中寻找essay文件
```

### linux的convert命令
作用转换图像，缩放，旋转等
``` bash
convert 1.jpg 2.png #转换图像格式
Convert 1.gif 2.bmp #
#改变图像尺寸
convert -resize 1024x768 1.jpg 2.jpg
#比例放缩 
Convert -resize %50 1.jpg 2.jpg 
#旋转图像
Convert -rotate 1.jpg 2.jpg
#批量处理-重新转换
for %f in (*.jpg) do convert “%f” “%~nf.png”
#全部进行缩放
for %f in (*.png) do convert “%f” -sample 80×40 “%~nf_sample.png”

```

### universal-ctags安装使用 1, 安装
``` bash
sudo apt-get install autoconf
git clone https://github.com/universal-ctags/ctags.git
cd ctats
./autogen.sh
./configure
make
sudo make install
```

2, 使用
``` bash
1)配置vim
打开～/.vimrc键入
Set tags=tags;
Set autochdir

2)使用在需要使用使用ctags的文件夹下，首先使用ctags -R .生成tags文件，之后进入vim
即可使用，常用快捷键
Ctrl + ] 找到光标出变量/函数定义的地方
Ctrl + T 回到跳转之前的地方
Ctrl + O 退回原来的地方
[I 全局查找光标出的变量，全局指在当前文件夹中的所有文件爱你中国呢查找
[i 局部查找变量，当前文件中查找
]i 类似上条命令，从光标出向下开始查找
[{ / [( 转到匹配的上一个中括号/圆括号
[} / [) 转到匹配的下一个中括号/圆括号
{/} 转到上/下一个空行
u 更新taglist，用于更改了代码之后更新
[[ 跳到前一个文件
]] 跳到后一个文件
*/# 转到光标出单词下一次/上一次出现的地方
gd 查找光标出的局部变量
```

### 命令行中查看图片尺寸信息的命令
``` bash
#第一种是用file命令
file im.png
#第二种是安装imagemagic
Sudo apt-get install imagemagic
identify im.jpg #使用identify查看信息
```

### 查看文件/文件夹的读取路径
``` bash
readlink -f dir/file_path
```

### superpixel-benchmark安装指南（docker） 
``` 
1, 按照项目指引安装glog
可能出现的错误： src/demangle.cc:170:16: error: ISO C++ forbids comparison between pointer and integer [-fpermissive]
解决办法是去对应的行在指针前面加*

2，安装Gflags（https://blog.csdn.net/weixin_43767344/article/details/103799983）：
git clone https://github.com/gflags/gflags.git

cd gflags

mkdir build && cd build

cmake … -DBUILD_SHARED_LIBS=ON

make -j4

make install

3，安装opencv，参考https://docs.opencv.org/4.5.2/d7/d9f/tutorial_linux_install.html，（build with opencv_contrib）

4, 然后make的时候，可能出现的错误： undefined reference to ‘google::FlagRegisterer::FlagRegisterer
解决方案，在对应的sub_dir下，以eval_summary_cli为例，在superpixel-benchamrk/eval_summary_slic/CMakelist.txt中加入：
find_package(gflags REQUIRED)
find_package(Threads)

target_link_libraries(eval_summary_cli libgflags.a libglog.a ${CMAKE_THREAD_LIBS_INIT})

该解决方案只能解决，eval相关的编译，对于core algorithm以及example的编译不起作用，目前对于对于算法模块的编译尚未找到解决方案

5，退而求其次，只能先编译用于eval的程序，将cmakelist.txt中的涉及算法的行，全都注释掉

6, 对于其他的错误如CV_BGR2Lab等未发现，为opencv版本问题，搜索即可找到解决方案
可能用到的参考网址：https://github.com/google/glog/issues/52
```

### ln命令与软，硬连接 
一个文件的硬连接相当于多个名称，删除一个不会影响另一个
一个文件的软连接相当于快捷方式
ln默认是硬连接，命令的使用方式为：
``` bash
ln 源文件路径 硬连接文件路径 #建立硬连接
ln -s 源文件路径 硬文件路径 #建立软连接

#例子，假设安装了g++-7与g++-8, 配置默认的g++通过建立软连接
ln -s /usr/bin/g++7 /usr/bin/g++ #配置默认为g++-7,g++-8同理

#查看软连接的指向
ls -al softlink
```
普通用户针对目录不能建立硬连接，可以建立软连接，原因是目录硬连接可能陷入死循环

###  强制删除坏包 
问题描述: apt remove xxx 出现 dependency problems prevent removal of xxx
```
dpkg --force-all -P package_name
#例如
dpkg --force-all -P Nvidia-driver-470
```

### at 命令定时执行任务（只执行一次） 
``` bash
#使用的格式为:
at 参数 时间
#常用参数 -v 显示任务将被执行的时间
-c 打印任务内容

#一个简单例子
at 5pm+3days #三天之后的下午五点（没有pm/am，默认为24小时制）
/bin/ls  #执行ls命令
<EOT> #ctrl +d自动出现<EOT>, 完成任务定制

at now+40 minutes #40分钟之后，关键字有minutes, hours, days, weeks
at 4:00 #今天四点执行，如果过了明天执行
At 16:00 tomorrow #明天下午四点执行, today也是个关键字
at 15:00 2019-03-11 #具体日期具体时间，绝对日期

#查询已经定时的任务
atq #查询，会给出id号

#删除已经提交的定时任务
atrm id

#查询任务内容
at -c id

```

#ubuntu ls命令去掉文件夹背景色
``` bash
dircolors -p | sed 's/;42/;01/' > ~/.dircolors
source ~/.bashrc
```

#下载google dirve中的文件
```
Pip install gdown
gdown https:google/drive...
```

###安装tiny_cuda_nn：
```
从这里下载：https://sourceforge.net/projects/tiny-cuda-neural-netw.mirror/，按照github的网站安装：https://github.com/NVlabs/tiny-cuda-nn
```

###cmake --build build 时错误： find not find xxx.h文件（无法发现c++头文件，在cmake . B -build 已经通过了，进行下一步install的时候出错）
``` bash
#解决方案：加入路径，重新编译
#step 1
#修改CMakelist.txt,加入：
INCLUDE_DIRECTORIES("PATH_OF_THE_H_HFILE")

#step 2:
 用新的CMakeList重新编译，build， install即可

```

###python setup.py install: xxx.h not found
```
#打开setup.py，找到ext_modules,加入
include_dirs = ['PATH_OF_HEAD_FILE']
```

###make all时报错：‘include <python.h>’ python.h not found:
```
Step1: export CPLUS_INCLUDE_PATH='/usr/include/python3.9/'

Step2: make all
```

###PS1彩色命令行提示设置：bashrcgenerator.com

###bash配置提示彩色之后，tmux错误： can't find terming database
解决方案：
```
mkdir ~/.terminfo
ln -s /lib/terminfo/x ~/.terminfo
```

###rsync制定端口
```bash
Rsync -avprvz -e 'ssh -p 36396' --progress file.zip yaxiong@ip:/home/
```

###ffmpeg简单指令
```bash
#剪切视频,从第一分31秒开始，剪切1分钟
ffmpeg -ss 00:01:31 -I input.mp4 -t 00:01:00 -c:v copy -c:a copy output.mp4

#改变视频尺寸,将分辨率改为720x1280 （高x宽）
ffmpeg -I input.mp4 -s 720x1280 -c:a copy output.mp4

#压缩mp4文件
ffmpeg -i input.mp4 -vcodec h264 -acodec mp3 out.mp4 #会过滤掉声音
ffmpeg -i input.np4 -vcodec h264 -acodec aac output.mp4 #保留声音
```

###github上传超过100M大文件
首先需要安装git-lfs
```
brew install git-lfs #for mac
```
假设大文件存在upload文件夹下面（如果想把大文件上传到仓库的子文件夹里，先把文件git clone下来，然后大文件复制到指定目录，git add *，再后续提交）
```bash
Cd upload #cd the dir that your file saved
Git init
Git lfs install
Git lfs track
Git commit -m 'pre'
Git remote add origin https:github. (github仓库地址链接)
git push origin master
Git add *
Git commit -m 'git LFS commt'
Git push origin master

```

###安装cuda11.7
```bash
Step1: 从官网安装，成功的案例是通过runfile local安装（run）
Step2: 系统变量倒入：
    export PATH=/usr/local/cuda-11.7/bin:$PATH
    export LD_LIBRARY_PATH="/usr/local/cuda-11.7/lib64/":$LD_LIBRARY_PATH
```


 anaconda deletes environment:
``` bash
conda remove -n tensorflow --all
Conda activate name#激活环境
conda deactivate #退出环境
conda info --env #列出所有的环境
conda info  #查看基本信息配置，例如安装源
```

 about the transpose convoluation output size 
the output size for transpose convolution is: 
* output_size=(input_size-1)xstride- 2 x input_padding + kernel_size  + output_padding

 vim command: 
when vim file1 file2, and use command "vertical all" to split the file1 and file into 2 vertical windows, use 'ctrl + w' to switch the file
多窗口操作： 
```
:new/sp filename 横向切割窗口
:vsp filename  纵向切割窗口
:tabc 关闭当前窗口
:tabo 关闭所有窗口
窗口大小调整
:ctrl+w +/- 纵向扩大/缩小
:res +/- num 纵向扩大/缩小多少行
:vertical res +/- num 横向增加/减少多少列

:!ls 运行bash命令
:n 跳到下一个文件 n c 跳到c文件
:e# 回到刚才编辑的文件
:ls 现实当前打开的文件，还有为难序号
:shell 不关闭vim回到shell
:exit / ctrl+D 从shell回到vim  
```

 工蜂git upload the file: 
it is under 'volumn' bar

### scp 命令远程拷贝文件 

``` bash
scp ./yalexwang_workstation/  webdev@100.115.156.103:haoyang/title_image_matching/yalexwang_workstation/ (如果拷贝是文件夹，scp之后加 -r即可)
```
过滤当前目录下的文件进行拷贝：
 ``` bash
 Scp !(*.new) yaxiwang@cluster.eng.uts.edu.au:~/Data/
 ```

### 远程拷贝，递归地遍历目录，过滤文件进行拷贝
``` bash
 rsync -av -e ssh --include='*.new' ./vgg16/ yaxiwang@cluster.eng.uts.edu.au:~/Data/TBIRDataSet/NUS-Wide/vgg16/
#与sshpass结合使用可以避免输入密码,-p选项输入密码 -f指定文件读取密码
sshpass -p wangyifan123456 rsync -avP ./* wangyifan@10.20.108.16:/home/wangyifan/   
```
其中a：递归忙碌
 v：详细输出
 e ssh：指定ssh作为远程shell，文件会被加密
 exclue/include： 排除文件/包含文件

###  nltk安装和使用停用词 
在已经安装nltk的基础上，进入python，导入nltk，输入命令：
``` bash
nltk.download('stopwords')
之后就可以导入停用词（列表）
from nltk.corpus import stopwords

stop_word = stopswords.words('english')#列表，里面是一些引文单词如‘me’, 'againse'等
```

###  python在声明一个类之后，对象调用
如果你想在定义一个类，通过对象调用传参调用，就得在类中实现一个函数 __call__(),定义传参如何调用，就像pytorch中的那样调用一样（liear = nn.linaer(3,4), liner(matrix)）,在nn.Linear中实现__call__函数

###  sheel输出重定向与后台运行
输出重定向命令：
``` bash
command 1> out.file 2>&1 （简写 command>out.file 2>&1）
```
其中1表示标准输出（打印到屏幕上的内容输出到了out.file中），2表示标准错误，将错误信息也输出到out.file中， &表示引用，不用重复打开文件覆盖内容, 0表示标准输入

对于将文件不断的挂起到后台运行，可以使用命令nohup（no hang up）:
``` bash
nohup command > &(nohup command 2>&1 &)
nohup python train.py >train.log 2>&1 &
```
默认将输出保存到当卡目录下的nohup.out文件中，如果已经存在，则保存到$HOME/nohup.out中, 其中最后一个&符表示提交到后台运行，第一个>表示将打印屏幕的输出弄到nohup.out文件中。

### nohup输出的文件，中途不再输出 
猜测可能是缓冲的问题，解决是加 -u参数
``` bash
nohup python -u train.py>nohup.log 2>&1 & #-u表示不启用缓冲
```

###  杀死nohup提交到后台的程序
首先使用命令：
``` bash
ps ux | grep 运行的程序名称（如 train.py）/ps ef | grep 运行的程序名称
```
按照运行时间查看要杀死的那次的运行对应的进程id，之后使用命令:
``` bash
kill -9 进程id
```
Nohup 命令执行后会输出一个id，这个id+1，一般就是提交到的后台命令程序的id。

### 从远程linux拷贝文件到本地
``` bash
sz file-name
```
输入上面的命令之后，会选择保存的位置，选择好之后就可以传送了

### linux 查看文件夹中有多少个文件
``` bash
ls | wc -w
```
列出文件夹下的所有文件并且按照内存的大小从大到小排序
``` bash
ls -Slh(r) 
```
R参数表示反过来排序


### linux查看磁盘使用情况
* 查看磁盘总体占用情况
``` bash
df -h
Df -lh #查看整个磁盘的占用情况
```
* 查看当前用户磁盘占用情况
``` bash
du -sh ~
```

###  以最高权限kill程序
``` bash
pkill -9 program_id (pkill -9 python 杀死所有python进程)
```

### 查看文件或者文件夹占用的磁盘空间大小
``` bash
du -h flick30k_image/
```

### pickle.load问题
使用pickle.load读入文件时报错：
``` bash
 'ascii' codec can't decode byte 0xe4 in position 24: ordinal not in range(128)
```
解决方案：
``` bash
pickle.load(open(filename, 'rb'), encoding = 'bytes')
```

###  python 3 与python*2中的转换为utf-8*
Python 2中： str(your_string).decode('utf-8')
Python 3中： str(your_string, 'utf-8')
在python3中使用decode会报错‘no attribute’

###  python 3错误：'collections.OrderedDict' object has no attribute 'iteritems' 
Python 3中没有OrderedDict.iteritems,该用OrderedDict.items()

### python对字典按照值进行排序
``` bash
sorted_list = sorted(dict_name.items(), key = lambda x:x[1], reverse  = True)#由大到小排序
```

### linux批处理有时候文件太多报错, 比如压缩文件zip
``` bash
/usr/bin/zip: Argument list too long
```
解决方案：
``` bash
ls | grep 关键字| xargs -n 10 zip zipfile.zip
```
含义为：列出匹配关键字的文件，以10个分组，然后分批及逆行zip操作

###  python中刷新输出语法 ：
如果是python 3
``` bash
print('\r finished percent: %f'%(counter/length * 100), end = '', flush = True)
```
如果是pyrhon2
``` bash
import sys
sys.stdout.write('\rfinished percent %f '%(counter / length *100))
Sys.flush()
```
注意write后面的\r和下一句的flush

### pickle.load报错：
``` bash
unsupported pickle protocol: 3
```
原因是pickle的保存格式版本不对，解决方案是重新保存pickle文件
``` bash
pickle.dump(data, open(file_pth, 'wb'), protocol = 2)
```

###  python从url读取图片的三种方式
第一种方式: opencv
``` bash
import cv2

cap = cv2.VideoCapture(url_string)
ret, img = cap.read()
```
读取的图片为array数据
	
第二种方式：skimage
``` bash
from skimage import io

image = io.imread(url)
#save the image
io.imsave('test.jpg', image)
```
读取的图片也为array数据

第三种方式：urllib
``` bash
import urllib

# 网络上图片的地址
img_src = 'https://img-my.csdn.net/uploads/201212/25/1356422284_1112.jpg'

# 将远程数据下载到本地，第二个参数就是要保存到本地的文件名
urllib.urlretrieve(img_src,'D:/1.jpg')
```

###  python中利用opencv在图片上写字： puttext函数
``` bash
import cv2

cv2.puttext(image_data, text_string, position, fontFace, fontcolor, thickness, linetype)

```
其中：
image_data为图片，类型为ndarray

text_string为想要放置的图片

position是textstring放置位置的左上角坐标

fontFace为字体设置，可以取的值为cv2.
FONT_HERSHEY_SIMPLEX, 
FONT_HERSHEY_PLAIN, 
FONT_HERSHEY_DUPLEX,
FONT_HERSHEY_COMPLEX, 
FONT_HERSHEY_TRIPLEX, 
FONT_HERSHEY_COMPLEX_SMALL, 
FONT_HERSHEY_SCRIPT_SIMPLEX, 
orFONT_HERSHEY_SCRIPT_COMPLEX

fontcolor为三元组（r, g, b）的值

thickness为线粗， 整数

linetype为线形，取值：
8 (or omitted) ： 8-connected line
4：4-connected line
CV_AA - antialiased line

### pickle.dumps()的使用 
pickle.dumps可以将一条数据序列化为一个字符串，并且以'.'作为一条数据的分隔符
在写入数据的时候，不用写换行, 存数据：
``` bash
a_dict = {'a':1, 'b':2}
f = open(file_path, 'w')
dict_str = pickle.dumps(a_dict)
f.write('f')
f.close()
```
读取数据:
``` bash
f = open(file_path , 'r')
content = f.read()
content_list = content.split('.')
a_data = pickle.loads(content_list[0] + '.')
f.close()
```

###  linux中文问题 
linux在程序中不支持中文u'\...'，解决方法：
```
string = u'\u...'
str1 = string.encode('utf-8')
```
有时程序中含有中文，报错， 例如：
``` bash
if '、' in string:
		....
```
解决方法， 在程序的开头加上：
``` bash
#-*-coding:utf8-*
```

###  linux下在图片上写汉字乱码，解决方法：将字符串解析为unicode并且将字体解码方式设置为默认
``` bash
unicode_str = str.decode('utf-8')
# PIL图片上打印汉字
draw = ImageDraw.Draw(pilimg) # 图片上打印
font = ImageFont.truetype("*.ttf", fontsize) # 参数1：字体文件路径，参数2：字体大小
draw.text((x, y), unicode_str, (0, 0, 0), font=font) 参数1：左上角坐标，参数2：字符串，参数3：字的颜色，参数4：字体
```
字符串如果是乱码，可以尝试：
``` bash
string = '??????'
s = string.decode('utf-8').encode('utf-8')
print(s)#将会是中文
```

###  macbook系统下进行hexo更新 
进入的你的博客目录下（博客根目录下，不需要移动到博客右键所在的目录），运行hexo g -d即可

 Linux查看内存使用情况的命令 
free -h

 linux安装文件没有权限 
输入--user 例如：
Pip install pytorch —user

###  vim打开的情况下在开另一个窗口显示另一个文件 
``` bash
:vsplit fiile_name.py
#多文件编辑
Ctrl+^ 两者之间切换
:bn/bp 切换到下一个/上一个
:ls 查看所有打开的文件
:close 关闭当前面板
#多标签操作
:tabnew/tabe + 文件名 开起新tab打开文件
:tabc/o 关闭当前tab/所有其他tab
:qa 退出所有的tab，到bash中
:tabn/p 下一个/上一个tab
gt/gT 直接可以在tab之间切换

:tabs 查看所有打开的tab
ctrl+s 停止向屏幕输出
ctrl+q 恢复向屏幕输出
```

在bash下打开之前使用vim -O file1 file2 会得到相同的结果
Split水平的效果

### 倒入tensorflow错误：ibcublas.so.10.0: cannot open shared object file: No such file or directory 
解决方法：
 ``` bash
 conda install cudatoolkit
 conda install cudnn

 ```
 如果上面的命令不奏效，运行时出错的话，终极解决办法：
 ``` bash
 conda install tensorflow-gpu
 ```
Conda会自动安装版本相关的依赖

### 读取xls文件的单元格内容及底色 
``` bash
  def count():
       path = './510_loose_true.xls'
       work_book = xlrd.open_workbook(path, formatting_info = 1)
       sheets = work_book.sheet_names()
       sheet = work_book.sheet_by_index(0)
  
       rows, cols = sheet.nrows, sheet.ncols
 
      posi_count = 0
      nega_count = 0
      img_list = []
      for row in range(rows):
          for col in range(3, cols):
              xfx = sheet.cell_xf_index(row, col)
              xf = work_book.xf_list[xfx]
              bgx = xf.background.pattern_colour_index
              value = sheet.cell(row, col).value
              if bgx == 17:
                  posi_count += 1
                  img_list.append(value)
              if bgx == 10:
                  nega_count += 1
                  img_list.append(value)
 
      img_set = set(img_list)
      print('total image: %d'%len(img_list))
      print('different images: %d'%len(img_set))
      print('negative images: %d'%nega_count)
      print('positive iamges: %d'%posi_count)
      print('validate: %d'%(nega_count + posi_count))
```

### anaconda移除环境 
 ``` bash
 conda remove -n tensorflow0 --all
```
### Image.fromarray(an_array)报错cann't haddle this type data
解决方法，fromarrray的shape必须是(224,224,3)并转换为uint8格式
``` bash
Array = np.random.rand(224,224,3)
Image = Image.fromarray(array.astyep(np.uint8))
``

### Image.open()错误：IOError：image file truncated 
``` bash
From PIL import ImageFile
ImageFile.LOAD_TRUNCATED_IMAGES=True
```

### 声明inception_v3网络报错
错误描述：ImportError: /lib64/libstdc++.so.6: version `CXXABI_1.3.9' not found (required by /home/yaxiwang/Data/anaconda/envs/pytorch/lib/python2.7/site-packages/scipy/sparse/_sparsetools.so)
解决方法：
``` bash
vim ~/.bash_profile
#在bash_profile加入
LD_LIBRARY_PATH=/home/yaxiwang/anaconda/lib:$LD_LIBRARY_PATH
export LD_LIBRARY_PATH两行
#最后要生效
source ~/.bash_profile
```
一般在anaconda/lib的目录下会有和pytorch配套的libstdc++.so.6文件，上面两行将改路径加入到搜索路径中

### python中import scipy错误 
Python自带的scipy不全，强制重新安装
``` bash
pip install --force-reinstall scipy --ignore-installed
```

### pip install安装错误：ERROR: Could not install packages due to an EnvironmentError: [Errno 39] Directory not empty: 
解决方案：后面加上选项 --ignore-installed
``` bash
pip install --force-reinstall scipy --ignore-installed
```

### anaconda安装之后无法识别conda命令 
``` bash
vim ~/.bash_profile
#在打开的文件中加入
export PATH="/users/wang/anaconda2/bin":$PATH
```
保存退出之后，source ～/.bashrc即可

### tailf命令
该命令可以实时检测文件的变动（最后一行），可以和nohup命令配合使用
```bash
tailf nohup.out
```

### docker中导入torchvision错误：libcudart.so.10.0: cannot open shared object file: No such file or directory
这是应为docker中没有读到cuda相关的文件，
解决办法：将cuda文件例如（/usr/local/cuda-10.0）复制到你的docker映射目录下例如（/home/yaxiwang/data/该目录在docker中的对应路径假设为：/data，
进入docker，打开文件：
``` bash
vim /etc/profile插入
export PATH=$PATH:/data/cuda-8.0/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/data/cuda-10.0/lib64
export LIBRARY_PATH=$LIBRARY_PATH:/data/cuda-10.0/lib64
#最后,使之生效
source /etc/profile
```

### 一次清空所有gpu程序，释放显存 
``` bash
#用clearndata来过滤指定的程序，ps ef 也可以换成ps ux
for i in `ps -ef|grep cleardata |awk '{print $2}' `; do kill -9 $i ; done;
```

### npy文件太大无法一次读入，以至于无法操作的问题 
如果一个train.npy文件太大，以至于无法操作，解决方案，使用numpy.load 函数时设置mmap_model = 'r'可以，其原理是将变量进行磁盘地址映射而不是载入内存中，语法：
``` bash
import numpy as np

Path = 'train.npy'
data = np.load(path, mmap_mode = 'r')#
```

### rsync命令传输文件 
使用rsync比scp更快，使用的一般模式：
``` bash
Rsync -avP * wangyifan@:/home/wangyifan/part_images
#过滤文件夹
rsync -avP ATTNGANW ATTNGAN_AUTO/ --exclude ATTNGANW/fea_save #注意第一个源文件夹没有/ 第二个目的文件夹有/
#-a 表示拷贝本地文件
#-v verbose 详细模式输出
#-P 相当于--partial 保留那些没有传输完成的文件，可以加快后续的传输

#其他常见的命令如下：
-v, --verbose 详细模式输出
-q, --quiet 精简输出模式
-c, --checksum 打开校验开关，强制对文件传输进行校验
-a, --archive 归档模式，表示以递归方式传输文件，并保持所有文件属性，等于-rlptgoD
-r, --recursive 对子目录以递归模式处理
-R, --relative 使用相对路径信息
-b, --backup 创建备份，也就是对于目的已经存在有同样的文件名时，将老的文件重新命名为~filename。可以使用--suffix选项来指定不同的备份文件前缀。
--backup-dir 将备份文件(如~filename)存放在在目录下。
-suffix=SUFFIX 定义备份文件前缀
-u, --update 仅仅进行更新，也就是跳过所有已经存在于DST，并且文件时间晚于要备份的文件。(不覆盖更新的文件)
-l, --links 保留软链结
-L, --copy-links 想对待常规文件一样处理软链结
--copy-unsafe-links 仅仅拷贝指向SRC路径目录树以外的链结
--safe-links 忽略指向SRC路径目录树以外的链结
-H, --hard-links 保留硬链结
-p, --perms 保持文件权限
-o, --owner 保持文件属主信息
-g, --group 保持文件属组信息
-D, --devices 保持设备文件信息
-t, --times 保持文件时间信息
-S, --sparse 对稀疏文件进行特殊处理以节省DST的空间
-n, --dry-run现实哪些文件将被传输
-W, --whole-file 拷贝文件，不进行增量检测
-x, --one-file-system 不要跨越文件系统边界
-B, --block-size=SIZE 检验算法使用的块尺寸，默认是700字节
-e, --rsh=COMMAND 指定使用rsh、ssh方式进行数据同步
--rsync-path=PATH 指定远程服务器上的rsync命令所在路径信息
-C, --cvs-exclude 使用和CVS一样的方法自动忽略文件，用来排除那些不希望传输的文件
--existing 仅仅更新那些已经存在于DST的文件，而不备份那些新创建的文件
--delete 删除那些DST中SRC没有的文件
--delete-excluded 同样删除接收端那些被该选项指定排除的文件
--delete-after 传输结束以后再删除
--ignore-errors 及时出现IO错误也进行删除
--max-delete=NUM 最多删除NUM个文件
--partial 保留那些因故没有完全传输的文件，以是加快随后的再次传输
--force 强制删除目录，即使不为空
--numeric-ids 不将数字的用户和组ID匹配为用户名和组名
--timeout=TIME IP超时时间，单位为秒
-I, --ignore-times 不跳过那些有同样的时间和长度的文件
--size-only 当决定是否要备份文件时，仅仅察看文件大小而不考虑文件时间
--modify-window=NUM 决定文件是否时间相同时使用的时间戳窗口，默认为0
-I 为所有更新输出一个更改摘要
-T --temp-dir=DIR 在DIR中创建临时文件
--compare-dest=DIR 同样比较DIR中的文件来决定是否需要备份
-P 等同于 --partial
--progress 显示备份过程
-z, --compress 对备份的文件在传输时进行压缩处理
--exclude=PATTERN 指定排除不需要传输的文件模式
--include=PATTERN 指定不排除而需要传输的文件模式
--exclude-from=FILE 排除FILE中指定模式的文件
--include-from=FILE 不排除FILE指定模式匹配的文件
--version 打印版本信息
--address 绑定到特定的地址
--config=FILE 指定其他的配置文件，不使用默认的rsyncd.conf文件
--port=PORT 指定其他的rsync服务端口
--blocking-io 对远程shell使用阻塞IO
-stats 给出某些文件的传输状态
--progress 在传输时现实传输过程
--log-format=formAT 指定日志文件格式
--password-file=FILE 从FILE中得到密码
--bwlimit=KBPS 限制I/O带宽，KBytes per second
-h, --help 显示帮助信息

### tmux配置前缀为ctrl+a 
退出tmux
``` bash
Step 1:
Vim ~/.tmux.conf
Step 2:在文件中输入
set -g prefix C-a
Unbind C-b
```
重新启动tmux即可生效,或者推出编辑之后，按ctrl+b->:进入命令行，再输入source-file ~/.tmux.conf

### tar解压与压缩文件 
解压文件夹到指定文件夹
``` bash
tar -zxvf name.tar -C ./your_dir
```
压缩文件夹到指定文件
``` bash
Tar -zcvf name.tar ./you_dir
```

### 查看内存或者cpu使用排行
``` bash
ps auxw --sort=%cpu  #cpu使用排行
ps auxw --sort=%MEM  #内存使用排行
```

### anaconda环境中不实用环境自带的python的问题
原因：未知，猜测可能是在PATH搜索中没有优先搜索到环境中的python
解决办法：
在profile配置环境中的路径在系统python和anacda自带python的前边
``` bash
PATH="/home/yxwang/anaconda3/envs/tensorflow/bin/":$PATH
```
退出出并source ～/.profile

### top 
top命令中使用 shift + m按照内存使用进行排序, shift + c对cpu占用进行排序

### anaconda改变路径的一些问题
1, anaconda创建环境的时候入指定路径，而不是默认在～/annaconda/envs下
``` bash
Conda create -p /ssd/yuxiong/.conda/envs/tensorflow python=3.6
#激活
Source activate /ssd/yuxiong/.conda/envs/tensorflow
```

2 conda install 软件的时候指定下载目录而不是下载到home目录下
``` bash
#设置环境变量
``` bash
Export CONDA_PKGS_DIRS=/ssd/yuxiong/conda/pags/
```
后边安装可能会出现设定为文件夹访问权限问题，解决办法
``` bash
Sudo chown 1002:1002 /ssd/yuxiong/conda/pkgs
```
这样安装之后可能会有一些路径问题，如
``` bash
Import numpy as np
```
报错： cannot import name '_mklinit' from 'numpy'
这是路径的问题，解决方法是配置环境所在的路径
打开～/.profile,键入
``` bash
export PATH="/ssd/yuxiong/.conda/envs/tensorflow/bin":$PATH
iexport PATH="/ssd/yuxiong/.conda/envs/tensorflow/lib/python3.6/site-packages":$PATH
```
退出， source ～/.profile重新激活环境，进入即可

### 多版本cuda与cudnn安装
如果你的tensorflow需要的版本是1.12.0,需要的cuda版本是9.0，但是机器上已经安装了cuda 10，这时候，只需要在
你的conda环境中，安装cudatoolkit和cudnn就可以了：/Users/wang/Documents/SMILES-近期工作进展报告_王亚雄_8_3.pdf
``` bash
Conda install cudatoolkit=9.0
Conda install cudnn=7.1.2
```
之后就可以直接import tensorflow as tf

### linux系统中常用的配置/Users/wang/Hexo Blog/source/_posts/miscellanea.md文件与路径
1, *配置文件：.bashrc .profile .bash_profile 的区别*
``` bash
.bashrc: 打开新的终端的时候回执行该文件中的命令和配置
.bash_profile:登陆机器的时候执行，且只执行一次
.profile: 该文件和.login,一起起到了bash_profile的作用,
有时候在bash_profile配置的命令在登录时不起作用，这时可以在./profile中进行配置，例如nvidia-docker attach wangyx
```
上述文件在用户目录和根目录下/etc都有，分别对当前用户和所有用户起作用，如果想理机起作用，只需source ～/.profile

2, *三个路径变量：PATH， LD_LIBRARY_PATH和LIBRARY_PATH*
``` bash
PATH:可执行文件存放目录
LIBRARY_PATH：程序编译期间查找动态链接库时，查找共享库的路径
LD_LIBRARY_PATH：程序的加载运行期间查找动态链接库时，除了系统默认路径之外的其他路径
```

3，*几个重要目录*
``` bash
/usr/local:软件安装的系统级目录,相当于windows下的C:/Windows
/opt: 用户级的附件安装目录，用于安装附件的软件
/usr/bin:常用命令的目录
/usr/local/bin、/bin、/usr/local/bin：常用的所有命令的文件
/usr/sbin: 不是必要的系统管理目录
/usr/include：C语言的头文件路径，
/usr/lib：程序不变的数据文件，包括一些site-wide配置文件
/var:系统一般运行时要改变的数据文件，每个系统都是特定的
```

### tqdm显示进度条 
``` bash
import tqdm 
from tqdm import trange

#索引便利
for I in trange(100):
   Pass

#职别便利列表
For item in tqdm.tqdm(a_list/iterator):
   pass
```

### linux服务器中添加新用户
得有root权限
``` bash
useradd wangyx #添加用户名
passwd wangyx #为用户设置密码
Mkdir /home/wangyx #穿件home目录
sudo chown -R linchao:linchao /home/linchao #将目录权限付给用户
useradd -d /home/wangyx wangyx #给用户增加用户目录
Userdel -r wangyx #删除用户

#增加用户的sudo权限
sudo usermod -aG sudo wangyx
#查看用户是否有sudo权限
groups yaxiong
#列出所有具有sodu权限的用户
getent group sudo

#===========================================#
#简易版本，用户不存在运行
sudo useradd -s /bin/bash -d /home/vivek/ -m -G sudo vivek  
-G 分组为sudo，即给予sodu权限，如果添加简单用户，-G sudo去掉即可 

```

### docker中git+url 错误：The SSL CA cert (path? access rights) 
解决方案：
``` bash
apt-get install ca-certificates
```
之后再次尝试即可。

### python format格式化控制输出精度 
``` bash
#第一个表示多少位有效数字，第二个整数控制精度即小数点保留位数，前面的0， 1表示位置对应
print('{0:4.2f}--{1:8.4f}'.fomrat(prection, recall)) 
```

### python中f-string
F-string中可以调用表达是或者函数,并且后面跟冒号可以控制精度(:.4f)
``` bash
A = 2
B = 3
print(f'A:{A}， B:{B}, A/B:{A/B:.4f}')
#注意括号内不能使用反斜杠\
print(f'His name is {\'Tom\'}')#错误
print(f"His name is {'Tom'}")#使用不同的引号
Str = f'{ {A} }'#插入大括号
```

### docker中tmux error: Cann't create soccer:permission denied
解决方案1:
``` bash
#运行命令
tmux -S ~/.tmsock new -ADsCyg
#tmux -a时任然会报错，解决方案在～/.bashrc中加入,并source
alias tmux='tmux -S ~/.tmsock new -ADsCyg'
```
解决方案2:
``` bash
#step1: add following command in ~/.bashrc
export TMUX_TMPDIR=~/.tmux/tmp

#step2:create folder & change to write mode
mkdir -p ~/.tmux/tmp
Chmod 777 -R ~/.tmux/tmp

#step3: source/reload the bashrc file
source ~/.bashrc

#step4: re-run the tmux, and it would be work normally
tmux
```

### ubuntu更新显卡驱动
Ubuntu 16.04, CUDA 10.2, NVIDA 
Reference: https://askubuntu.com/questions/1077061/how-do-i-install-nvidia-and-cuda-drivers-into-ubuntu (the second answer)
``` bash
step 1: sudo rm /etc/apt/sources.list.d/cuda*
sudo apt remove nvidia-cuda-toolkit

step 2: sudo apt remove nvidia-*

step 3: sudo apt update

step 4: sudo apt-key adv --fetch-keys  http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub

step 5: sudo bash -c 'echo "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64 /" > /etc/apt/sources.list.d/cuda.list'

step 6: sudo apt update

step7: sudo apt install cuda-10-2 #此步骤更新cuda和对应的driver
```

### Submitted to arXiv 
1, Journal(reference: begin{item} in main.tex)
``` bash
step 1: in overleaf, choose the submit --> arxiv->download the zip file (would auto deal with some dir problems)(texStudio打开文件并编译)
step 2: in main.tex, if you use the hyperref like '\usepackage{options ... }[hyperref]' change to
\usepackage{hyperref}
\hypersetup{colorlinks, allcolors=black} 
step 3: build a new file named ref.bbl(名称尽量和主.tex文件名相同，例如tmi_main.tex==>tmi_main.bbl) and cut the content from \begin{thebibliography}[1] to \end{thebibliograph}(包含环境变量), ie, all references
step 4: delete the content mentioned in step3 and add the following contents in its original place:
\nocite{*} #注意不是notice
\input{tmi_main.bbl}
step 5: notice the dir problems, if your project under name 'tcyb', the image path, 'tcyb/first_show.png' should change to 'first_show.png' 

#会议版本
step 2和step 4也是必须的
```

### 查找当前目录下是否包含某个字符串的所有文件
``` bash
grep -rn "nibabel" * #递归查找所有目录下包含“nibabel“的文件
```

###  matplotlib， opencv， Pillow, script.misc.imread读入的图片
``` bash
Opencv读取的图片的通道0-2对应的BGR颜色, 取值范围0-255， 数据类型numpy.ndarry
matplotlib.image.imread读入的图片是RGB颜色,取之范围0-255， 数据类型numpy.ndarray
scripy.misc.imread/scipy.ndimage.imread 读取的图像为RGB，范围0-255， 数据类型numpy.ndarray
Pillow(PIL)读取图像对象，供后续处理
skimage.io.imread通道顺序RGB，取值范围0-255， 数据类型numpy.ndarray
```

### opencv保存与读取四通道图片
``` bash
import cv2
#save the image, img是一个四通道ndarray
Cv2.imwrite('test.png', img, [int(cv2.IMWRITE_PNG_COMPRESSION), 9])
#读取图片
Im = cv2.imread(path, cv2.IMREAD_UNCHANGED)

```

### tensorboard启动时error：Not a TBLoader or TBPlugin subclass: ...
``` bash
#解决方案
Pip uninstall tensorboard-plugin-wit==1.6.0.post3
```

### segmentation可视化的包
``` bash
From skimage.segmentation import mark_boundaries, find_boundaries
#mark_boundaries:根据区域的label画出边界
#image_array:image data 0-255
#sp_arry: label data int
Out = mark_boundaries(image_array, sp_array)
Cv2.imwrite('vis.png', out*255)

#find_boundaries:根据label显示出边界（1表示边界）
#output the boundary pixel
Out = find_boundaries(sp_arr)

```

### pytorch分布式训练
step1: 安装apex
``` bash
git clone --recursive https://github.com/NVIDIA/apex.git
cd apex
pip install . #注意点号
#最好的安装是，cuda11报错未安装成功,cuda10成功：
pip install -v --no-cache-dir --global-option="--cpp_ext" --global-option="--cuda_ext" ./

#note:可能安装之后：import apex报错module not found, 解决
#
Import sys
sys.path.append('/home/yaxiong/apex')
```
step2: 分布式训练
``` bash
#代码示例
import os
from datetime import datetime
import argparse
import torch.multiprocessing as mp
import torchvision
import torchvision.transforms as transforms
import torch
import torch.nn as nn
import torch.distributed as dist
from apex.parallel import DistributedDataParallel as DDP
from apex import amp

class ConvNet(nn.Module):
    def __init__(self, num_classes=10):
        super(ConvNet, self).__init__()
        self.layer1 = nn.Sequential(
            nn.Conv2d(1, 16, kernel_size=5, stride=1, padding=2),
            nn.BatchNorm2d(16),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2))
                
        self.layer2 = nn.Sequential(
            nn.Conv2d(16, 32, kernel_size=5, stride=1, padding=2),
            nn.BatchNorm2d(32),
            nn.ReLU(),
            nn.MaxPool2d(kernel_size=2, stride=2))
        self.fc = nn.Linear(7*7*32, num_classes)

    def forward(self, x):
        out = self.layer1(x)
        out = self.layer2(out)
        out = out.reshape(out.size(0), -1)
        out = self.fc(out)
        return out


def train(gpu, args):
    #计算传入的gpu在总共gpu的顺序号，如果只是一个机器，就是gpu的id
    ##
    rank = args.nr * args.gpus + gpu                              
    #初始化方式为env://, 需要设置四个环境变量：MASTER_PORT, MASTER_ADDR, WORD_SIZE, RANK
    #前两个已设置，后两个作为参数传入
    dist.init_process_group(                                   
        backend='nccl',                                         
        init_method='env://',                                   
        world_size=args.world_size,                              
        rank=rank                                               
    )
    ##
    torch.manual_seed(0)
    model = ConvNet()
    torch.cuda.set_device(gpu)
    model.cuda(gpu)
    batch_size = 100
    # define loss function (criterion) and optimizer
    criterion = nn.CrossEntropyLoss().cuda(gpu)
    optimizer = torch.optim.SGD(model.parameters(), 1e-4)

    ##	
    #如果模型中有batchnorm，使用同步batchnorm以获得更好的结果
    model = torch.nn.SyncBatchNorm.convert_sync_batchnorm(model)      

    #包装模型，
    model = nn.parallel.DistributedDataParallel(model,
        device_ids=[gpu])

    #-------如果要同时分布式训练并且使用fp16，上面两句需要替换为（后续loss反向也需要改动)----------#
    model = apex.parallel.convert_syncbn_model(model)
    model, optimizer = amp.initialize(model, optimizer, opt_level='O1')
    model = DDP(model)
    #-------------------------------------------------------------#     
    ##
    
    # Data loading code
    train_dataset = torchvision.datasets.MNIST(root='./data',
        train=True,
        transform=transforms.ToTensor(),
        download=True)

   ##
   #负责给各个gpu切分数据
    train_sampler = torch.utils.data.distributed.DistributedSampler(
        train_dataset,
        num_replicas=args.world_size,
        rank=rank
    )
    ##
    
   #dataloader中shuffle必须为false，指定sampler为分配的sampler
   train_loader = torch.utils.data.DataLoader(dataset=train_dataset,
        batch_size=batch_size,
        shuffle=False,
        num_workers=2,                                                                                                                                                                                 
        pin_memory=True, sampler=train_sampler)

    start = datetime.now()
    total_step = len(train_loader)
    for epoch in range(args.epochs):
        for i, (images, labels) in enumerate(train_loader):
            images = images.cuda(non_blocking=True)
            labels = labels.cuda(non_blocking=True)
            outputs = model(images)
            loss = criterion(outputs, labels)

            optimizer.zero_grad()
            loss.backward()
	    #-----如果使用fp16，需要loss.backward替换为：----------------#
	    with amp.scale_loss(loss, optimizer) as scaled_loss:
                scaled_loss.backward()
            #-------------------------------------------------------#

            optimizer.step()
            if (i + 1) % 100 == 0 and gpu == 0:
                print('Epoch [{}/{}], Step [{}/{}], Loss: {:.4f}'.format(
                epoch + 1,
                args.epochs,
                i + 1,
                total_step,
                loss.item())
                )

    if gpu == 0:
        print("Training complete in: " + str(datetime.now() - start))

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument('-n', '--nodes', default=1, type=int, metavar='N')
    parser.add_argument('-g', '--gpus', default=1, type=int,
                       help='number of gpus per node')
    parser.add_argument('-nr', '--nr', default=0, type=int,
                       help='ranking within the nodes')
    parser.add_argument('--epochs', default=200, type=int, metavar='N',
                       help='number of total epochs to run')
    args = parser.parse_args()
    
    #step 1:设置控制主机的地址，如果为本地主机，即设置为127.0.0.1，并设置端口
    args.world_size = args.gpus * args.nodes
    ##
    os.environ['MASTER_ADDR'] = '127.0.0.1'
    os.environ['MASTER_PORT'] = '8888' 
    #mp的作用是启动该节点的所有进程，每个节点都会运行train函数，其中i从0-args.gpus-1(节点机器的总共可用的gpu数量）
    #即函数是将程序按照GPU id进行部署到每一个gpu上
    mp.spawn(train, nprocs=args.gpus, args=(args,))
    ##
    train(0, args)

if __name__ == '__main__':
    main()

#===============命令行中输入命令开始训练======================#
CUDA_VISIBLE_DEVICES=1,2 python pytorch_dist.py -n 1 -g 2 -nr 0
``` 
注意，在validation的时候，需要统计各个进程的结果
``` bash
validate_loss = loss(input, target)
validate_loss = reduce_tensor(validate_loss)

def reduce_tensor(tensor, world_size=2):
    dist.barrier()
    dist.all_reduce(tensor, op=torch.distributed.ReduceOp.SUM)#或者op=dist.reduce_op.SUM
    tensor =  tensor / world_size
    
    return tensor
```

### 使用pudb进行多线程调试
``` bash
#step 1:首先安装pudb
pip install pudb

#step 2:在程序中
Import pub.remote import set_trace
#在想要走停住的地方插入
set_trace() #或者设置窗口大小set_trace(term_size=(80, 24))

#step 3:打开另一个终端窗口输入
telnet into 127.0.0.1 6899.

#重要的快捷键
ctrl + x 进入交互模式，类似pdb，但是上下箭头被占用, 再次ctrl +x 推出交互模式
Ctrl + p 进行设置，包括设置主题
？ 帮助

```

###  caffe 安装的小技巧
1 如果cmake出错/或者cmake之后make出错，报变量找不到的错误，一长串, 试试设置c++版本为11： 
``` bash
 cmake -DCMAKE_CXX_FLAGS='-std=c++11' ..
```
之后再make

2 Error： cudnn.hpp(114): error : too few arguments in function call
``` bash
#修改cudnn.h中的函数

template <typename Dtype>
inline void setConvolutionDesc(cudnnConvolutionDescriptor_t* conv,
    cudnnTensorDescriptor_t bottom, cudnnFilterDescriptor_t filter,
    int pad_h, int pad_w, int stride_h, int stride_w) {
  CUDNN_CHECK(cudnnSetConvolution2dDescriptor(*conv,
      pad_h, pad_w, stride_h, stride_w, 1, 1, CUDNN_CROSS_CORRELATION));
}

#改为
template <typename Dtype>
inline void setConvolutionDesc(cudnnConvolutionDescriptor_t* conv,
    cudnnTensorDescriptor_t bottom, cudnnFilterDescriptor_t filter,
    int pad_h, int pad_w, int stride_h, int stride_w) {
 
#if CUDNN_VERSION_MIN(6, 0, 0)
	CUDNN_CHECK(cudnnSetConvolution2dDescriptor(*conv,
		pad_h, pad_w, stride_h, stride_w, 1, 1, CUDNN_CROSS_CORRELATION,
		dataType<Dtype>::type));
#else
	CUDNN_CHECK(cudnnSetConvolution2dDescriptor(*conv,
		pad_h, pad_w, stride_h, stride_w, 1, 1, CUDNN_CROSS_CORRELATION));
#endif
}
```

3 如果python3安装不成功试试python2.7

4, 安装一些自定义的层
安装一些项目自定义的层时，找这样的目录，该目录下有CMakeList.txt和src/caffe文件夹（或者类似名称），在该目录下创建build，并进行cmake .. ==> make -j 命令进行安装


###  pip/apt搜索包
``` bash
#在库中搜索安装包
Pip/apt search opencv-python

#列出已经安装的包
Pip list | grep opencv-python

#显示已经安装包的信息, 包括版本号,安装位置
pip/apt show  opencv-python

```

### python递归遍历文件夹得到所有文件的绝对路径
``` bash
Import os

all_pathes = []
for root, dirs, files in os.walk(os.path.abspath(split)):
   for file in files:
   all_pathes.append(os.path.join(root, file))

all_pathes =[item for item in all_pathes if '.jpg' in item]
```

###  整体迁移anaconda目录
参考链接： https://my.oschina.net/u/4387680/blog/4318430
注意：如果按照修改后source activate pytorch不行，请尝试conda activate pytorch

### vim配置NERDTRee
``` bash
1，安装Vundle
mkdir -p ~/.vim/bundle
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim

2，打开.vimrc添加如下内容
set nocompatible       " be iMproved, required
filetype off         " required
 
" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'
 
" Keep Plugin commands between vundle#begin/end.
" 安装其它插件
 
" All of your Plugins must be added before the following line
call vundle#end()      " required
filetype plugin indent on  " required

3，安装NERDTree
git clone https://github.com/preservim/nerdtree.git ~/.vim/bundle/nerdtree

4，打开vimrc在call vundle#end()语句之前添加内容：
Plugin 'preservim/nerdtree'

Vim打开文件，命令行下输入：NERDTree即可使用

```

###  Vim配置自动代码补全YouCompleteMe（docker环境中）
假设已经安装好了Vundle
``` bash
1，首先安装依赖库
sudo apt-get install build-essential cmake python-dev python3-dev

2，安装YouCompeleteMe，首先下载源码到指定的目录
git clone https://github.com/Valloric/YouCompleteMe.git ~/.vim/bundle/YCM

3，获取相关依赖
git submodule update --init --recursive

4，安装YCM的语言依赖
apt install mono-complete golang nodejs default-jdk npm

5，编译,在YCM目录下，运行,all表示为所有的语言安装依赖
./install.py --all
如果安装总是不成功，尝试：
/usr/bin/python3 install.py

6，配置vimrc文件，打开vimrc文件
在call vundle#begin() 和call vundle#end()之间插入：
Plugin 'Valloric/YCM'

7，在filetype plugin indent on  "" required之后输入
let g:ycm_global_ycm_extra_conf = '/root/.vim/bundle/YCM/.ycm_extra_conf.py'
""开启基于tag的补全，可以在这之后添加需要的标签路径
""let g:ycm_collect_identifiers_from_tags_files = 1
""开启语义补全
let g:ycm_seed_identifiers_with_syntax = 1
""在接受补全后不分裂出一个窗口显示接受的项
""set completeopt-=preview
""不显示开启vim时检查ycm_extra_conf文件的信息
let g:ycm_confirm_extra_conf=0
""每次重新生成匹配项，禁止缓存匹配项
""let g:ycm_cache_omnifunc=0
""在注释中也可以补全
let g:ycm_complete_in_comments=1
""输入第一个字符就开始补全
""let g:ycm_min_num_of_chars_for_completion=1
""不查询ultisnips提供的代码模板补全，如果需要，设置成1即可
let g:ycm_use_ultisnips_completer=0

""下面代码加上感觉最开的打开文件的时候，补全会变慢，不知道具体的功能
let g:ycm_show_diagnostics_ui = 0
let g:ycm_server_log_level = 'info'
let g:ycm_min_num_identifier_candidate_chars = 2
let g:ycm_collect_identifiers_from_comments_and_strings = 1
let g:ycm_complete_in_strings=1
let g:ycm_key_invoke_completion = '<c-z>'

noremap <c-z> <NOP>

""导致变慢的原因是这个命令
let g:ycm_semantic_triggers =  {
                \ 'c,cpp,python,java,go,erlang,perl': ['re!\w{2}'],
            \ 'cs,lua,javascript': ['re!\w{2}'],
            \ }


##====== 可能遇到的错误==================
1，执行以上步骤之后，打开vim，报错
The ycmd server SHUT DOWN (restart with :YcmRestartServer). Unexpected exit code 1. Type ':YcmToggleLogs ycmd_57346_stderr_2hrqlh43.log' to check the logs.

解决方案：
a 在YCM目录下，输入
cat PYTHON_USED_DURING_BUILDING
b,上述命令输出一个python路径，为YouCompleteMe所执行的python环境，假设输出为/usr/bin/python3
/usr/bin/python3 install.py

2，提示vim版本过低，不支持YouCompleteMe
YouCompleteMe unavailable: requires Vim 8.1.2269+.

解决方案：安装最新版的vim，依次执行
add-apt-repository ppa:jonathonf/vim 
apt update 
apt install vim 

3，提示gcc版本过低： Your C++ compiler does NOT fully support C++17

解决方案：升级gcc，依次执行
apt-get install g++-8
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 700 --slave /usr/bin/g++ g++ /usr/bin/g++-7
update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 800 --slave /usr/bin/g++ g++ /usr/bin/g++-8

```

### vim配置AsyncRun命令 
Async可以在不退出vim的状态下，执行各种bash命令
1 安装： 假设已经安装好了Vundle了
``` bash
cd ~/.vim/bundle
git clone https://github.com/skywind3000/asyncrun.vim asyncrun
```
2 配置vimrc文件
在call vundle#begin() 和call vundle#end()之间插入：
```bash
Plugin 'skywind3000/asyncrun' (较老版本的Plugin==>Bundle)
```

3 配置vimrc让asyncrun变得更好用
```bash
map <F1> :copen<cr> #打开quick fix窗口
map <F2> :AsyncRun  #注意最后有一个空格
```

### vim安装ZoomWin实现最大最小化窗口 
假设已经安装Vundle
1，安装
```bash
cd ~/.vimrc/bundle
git clone https://github.com/vim-scripts/ZoomWin zoomwin
```

2, 配置vimrc
在call vundle#begin() 和call vundle#end()之间插入：
```bash
Plugin 'vim-scripts/zoomwin'
```

3, 映射快捷键，在vimrc文件中
``` bash
map <F4> :ZoomWin<cr> #利用F4在最大化当前窗口与返回
```

### 命令行通过.torrent下载文件 
``` bash
step 1: 安装rtorrent
sudo apt-get install rtorrent

step 2：进入环境
rtorrent

step 3: 会车即可，load.normal> 并粘贴.torrent文件的 url

step 4: Down/Up 键按住选择要下载的文件（* 符号指示）

step 5: ctrl +S开始下载
   ctrl + D 停止下载
   ctrl + Q 退出rtorrent
```

### linux/Mac 测试制定端口的ip地址是否畅通
``` bash
#linux: telnet
telnet 192.168.1.102 3306

#Mac
nc -vz -w 2 192.168.1.104 3306

windows下得下载tcping: https://blog.csdn.net/qq_35487165/article/details/89458003
```

### 安装ninja包 
参考https://blog.csdn.net/qiuguolu1108/article/details/103842556

### mac无法联网问题 
App可以正常使用，但是浏览器不能打开网页（safari和Google chrome都不行）。
解决方案： 系统设置->网络-->高级设置->代理，只选择自动发现代理，其他不打勾

###  谷歌浏览器 your connection is not private
聚焦网页，然后直接输入：thisisunsafe (不需要在文本框中,直接聚集在网页上然后输入就好）

### docker升级驱动之后不能启动 
``` bash
Sudo apt-get install nvidia-container-runtime
```

### 启动jupyter nootbook on mac 
``` bash
python3 -m IPython notebook filename.ipynb
```

###  pip 更改源
1 查看当前源
``` bash
pip config list
```

2 临时指定源,使用-i url
``` bash
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple gevent #指定清华源下载安装
```

3 永久使用某个源
``` bash
mkdir ~/.pip
vim ~/.pip/pip.conf
#在pip.conf中写入
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host=pypi.douban.com #http-com中间内容，将永久使用豆瓣源
```

4 常用国内的源包括
```
清华大学 ：https://pypi.tuna.tsinghua.edu.cn/simple/
阿里云：http://mirrors.aliyun.com/pypi/simple/
中国科学技术大学 ：http://pypi.mirrors.ustc.edu.cn/simple/
华中科技大学：http://pypi.hustunique.com/
豆瓣源：http://pypi.douban.com/simple/
腾讯源：http://mirrors.cloud.tencent.com/pypi/simple
华为镜像源：https://repo.huaweicloud.com/repository/pypi/simple/
```

###  freeglut3 无法安装的问题（apt-get install 不行）
``` bash
wget http://ftp.debian.org/debian/pool/main/f/freeglut/freeglut3_2.8.1-3_amd64.deb
dpkg -i thewebpackage
```

###  远程web端查看服务器上的数据 
showdata工具

``` bash
#1, 在服务器上安装showdata
pip install showdata

#2, 在想查看的文件夹下，运行
showdata server -p 8000 -h 0.0.0.0
 
该命令会打印对应的查看地址，一般是服务器的IP和设置的端口号

#3，在本地浏览器输入上面的 地址即可查看
更多流程可以查看 https://github.com/silverbulletmdc/showdata

```

###  训练可视化工具wandb 
1 安装
```
pip install wandb

```

2 去官网注册账号： https://wandb.ai/home


3 服务器终端：
```
wandb login 
```
让输入author id，随便输入，根据提示网站，拿到id，粘贴到命令行

4，在程序中使用
简单使用实例
```
import wandb

# 1. Start a new run
wandb.init(project="gpt-3")

# 2. Save model inputs and hyperparameters
config = wandb.config
config.dropout = 0.01

# 3. Log gradients and model parameters
wandb.watch(model)
for batch_idx, (data, target) in enumerate(train_loader):
    ...
    if batch_idx % args.log_interval == 0:
        # 4. Log metrics to visualize performance
        wandb.log({"loss": loss})
```

4 更详细版本简介
```
https://zhuanlan.zhihu.com/p/266337608
https://zhuanlan.zhihu.com/p/342300434
https://github.com/wandb/wandb
https://docs.wandb.ai/ (文档+实力)
```

###  3D obj文件可视化
https://github.com/wangyxxjtu/show3D

pip install安装文件不在指定的环境下，可以利用target进行指定
```
pip3 install --target=/Users/wang/miniconda3/lib/python3.8/site-packages imageio_ffmpeg

#查看当前python的包搜索路径
python -site
```


### Visual Code中远程访问 Jupyter Notebook
```
#1 远程机器安装jupyter notebook
 pip install jupyter notebook

#2 配置文件生成
 jupyter notebook --generate-config
 Jupiter notebook password #input passed twice

 #3 相应设置
 Vim ~/.jupyter/jupyter_notebook_config.py
 #在最后输入
 c.NotebookApp.ip=‘*’
 c.NotebookApp.password=u'password in step 2'
 c.NotebookApp.open_browser=False
 c.NotebookApp.port=8888

 #4在远端服务器上启动
 jupyter notebook --allow-root

 #在本地terminal输入：
 ssh -N -L localhost:8888:localhost:8889(该端口显示在3中) username@ip -p port (ssh port)

 #5 之后可以直接浏览器中输入：https://localhost:8888打开
 #viscode中可以直接配置 （vs code不需要上步骤的ssh -N -L 连接）
 #右下角跳出 insecure connection的时候点确定
 #点击 右上角选择解释器，选择remote jupyter server
 #输入
 https:://remote_ip:8889// 或者 远程服务端显示的 IP （IP换成了一串字符），有一些奇奇怪怪的问题，多试几次
 #enter之后输入密码
 #之后选择解释器即可
```
需要注意的问题有：
```
#1,连上后找不到远程的conda环境(VSCode/网页登陆)
python -m ipykernel install --user --name pytorch（conda环境名称) --display-name "Python (pytorch)"
之后重启vs code

#2,连上之后仍旧报错
export JUPYTER_ALLOW_INSECURE_WRITES=1
```

### 安装多个版本的cuda
```
Step1: 去这个网站
https://developer.nvidia.com/cuda-toolkit-archive
选择对应的版本

Step2: installer type选择runfile(local)

Step3:下载到本地，sh安装，如果驱动和当前的不匹配，不要勾选驱动（x号是选择，其他都要选择）

Step4:如果要访问安装的cuda，bashrc文件中，更新PATH=/usr/local/cuda-11.1/:$PATH

Step5:source ~/.bashrc即可
```

### Zerotier 内网穿透
参考https://zhuanlan.zhihu.com/p/675736907
```bash
1，进入Zerotier 官网注册账号(谷歌账号直接登录即可)：https://my.zerotier.com/

2，登录，点击create a  network, 记录下Network ID

3，在你要穿透的内网机器上安装客户端，以linux系统为例，执行命令：
   curl -s https://install.zerotier.com | sudo bash

4，本地机器启动服务： sudo /usr/sbin/zerotier-one -d
   这步可能报错：fatal error: cannot bind to local control interface port 9993
   解决方案：step 1: systemctl stop zerotier-one （stop/Restart重新启动  start也是一个选项） step 2: sudo /usr/sbin/zerotier-one -d

5, 接入ID： sudo /usr/sbin/zerotier-cli join 网络ID

6，去zerotier 网站上，会显示member接入

7， Physical IP就是地址, 任意一台机器上  ssh  配置的时候机器用户名@Physical IP即可链接
```

### Anaconda移动安装的位置
```bash
sudo mkdir /your/new/path/anaconda3

sudo chown $USER:$USER /your/new/path/anaconda3

sudo rsync -aXS $HOME/anaconda3/. /your/new/path/anaconda3/.

mv $HOME/anaconda3 $HOME/anaconda3_back

ln -s /your/new/path/anaconda3 $HOME/anaconda3

rm -rf $HOME/anaconda3_back
```

### 利用samba文件传输协议进行ubuntu和windows 10文件共享
ubuntu端的操作
``` bash
#step 1: 安装samba
sudo apt-get install samba

#step 2: 创建共享目录
mkdir /home/hfut/smbshare
sudo chmod 777 /home/yaxiong/smbshare

#step 3: 设置密码,运行并连续输入两次密码
sudo smbpasswd -a yaxiong

#step 4: 修改samba配置文件
sudo vim /etc/samba/smb.conf
#再最后加入
[share]
path = /home/yaxiong/smbshare
public = yes
writable = yes
valid users = yaxiong
create mask = 0644
force create mode = 0644
directory mask = 0755
force directory mode = 0755
available = yes

#(1) [share]表示共享文件夹的别名，之后将直接使用这个别名
#(2) force create mode 与 force directory mode的设置是因为Windows下与Linux下文件和文件夹的默认权限不同造成的，Windows下新建的文件是可执行的，必须强制设定其文件权限。
#(3) valid users 设置为你当前的Linux用户名，例如我的是yaxiong，因为第一次打开共享文件夹时，需要验证权限。

#step 5: 启动sama服务器
sudo systemctl start smbd

#step 6: 测试是否共享成功
sudo apt-get install smbclient 
smbclient -L //localhost/share
//还记得吗？share为2.3中设置的共享文件夹的别名
//如果共享成功，将要求输入之前设置的密码
```

windows端操作：
```bash
1，cmd+r ->输入control->打开控制面板

2，控制面板->程序->启动或关闭windows功能->勾选SMB 1.0/CIFS 文件共享知识

3，点击用户账户->管理windows凭据->增加windows凭据->输入IP 用户名以及密码

4，打开文件管理器->此电脑->三个点(...)->映射网络驱动器->文件夹：\\ip\share  即可连接
```


### 安装多个cuda
```bash
安装多版本cuda，cuda都是同样的装
0，准备步骤，安装显卡驱动，已安装可跳过
sudo add-apt-repository ppa:graphics-drivers/ppa #将GPU驱动仓库加入系统
ubuntu-drivers devices #checkGPU和可用的驱动
sudo ubuntu-drivers install #安装
nvidia-detector/nvidia-smi  #测试是否成功

1，前往https://developer.nvidia.com/cuda-toolkit-archive，下载对应的CUDA版本，以11.8为例

2，最后安装方式，选择runfile (local)，并下载
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda_11.8.0_520.61.05_linux.runsudo 
sh cuda_11.8.0_520.61.05_linux.run

3，安装时，不要选择 CUDA driver选项（取消 x号）

4，安装cuDNN加速库
前往 https://developer.download.nvidia.com/compute/cudnn/redist/cudnn/linux-x86_64/，下载CUDA 11.8对应的版本

5，解压cudnn压缩包
tar -xvf cudnn-linux-x86_64-9.1.0.70_cuda11-archive.tar.xz

6，复制对应的文件到cuda tookit文件
cd cudnn-linux-x86_64-9.1.0.70_cuda11-archive/
sudo cp include/cudnn*.h /usr/local/cuda-11.8/include
sudo cp lib64/libcudnn* /usr/local/cuda-11.8/lib64

7，并赋予读取权限：
sudo chmod a+r /usr/local/cuda-11.8/include/cudnn*.h /usr/local/cuda-11.8/lib64/libcudnn*
sudo chmod a+r /usr/local/cuda-12.1/include/cudnn*.h /usr/local/cuda-12.1/lib64/libcudnn*

8，切换到安装的版本
vim ~/.bashrc
写入：
PATH=/usr/local/cuda-11.8/bin:$PATH
LD_LIBRARY_PATH=/usr/local/cuda-11.8/extras/CUPTI/lib64:$LD_LIBRARY_PATH
LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64:$LD_LIBRARY_PATH

source ~/.bashrc

9,多版本切换
 多版本之前切换
1，删除原有的cuda软连接
rm -rf /usr/local/cuda

2, sudo ln -s /usr/local/cuda-11.8/ /usr/local/cuda
nvcc -V

```

###定义快捷键传入参数
```bash
alias hs='func() { history | grep $1;}; func' 

#terminal中，筛选python程序
hs python 
```

###pytorch 分布式训练： torch.distributed.elastic.multiprocessing.api FAILED exit code -9
```bash
原因是内存不够
```


###Nvidia驱动坏了快速重新安装：
```bash
1， 安装dkms
sudo apt-get install dkms

2, 查看之前坏了的驱动版本
ls /usr.src | grep nvidia

3, 重新生成驱动模块
sudo dkms install -m nvidia -v 550.90.07

4, 禁止内核自动更新
sudo nvidia-smi -pm 1
```

###  nvidia-smi 有卡顿，刷新慢的问题解决：
```bash
sudo nvidia-persistenced --persistence-mode
```

### pytorch 分布式训练： torch.distributed.elastic.multiprocessing.api FAILED exit code -9
原因是内存不够

### 利用nltk记性词干提取  
可利用WOrdNetLemmatizer实现，该实现的时候需要手动设置处理词的词性，否则会出现错误的提取，例如working，如果不指定词性，输出将会为working。所以具体做法为，现对词进行词性标注，将词性转换为wordnet的词性，传入WordNetLemmatizer中
词性函数定义：
``` bash
from nltk.corpus import workout
def get_wordnet_pos(treebank_tag):
    if treebank_tag.startswith('J'):
        return wordnet.ADJ
    elif treebank_tag.startswith('V'):
        return wordnet.VERB
    elif treebank_tag.startswith('N'):
        return wordnet.NOUN
    elif treebank_tag.startswith('R'):
        return wordnet.ADV
    else:
        return None
```
传入词性进行单词还原：
``` bash
import nltk
from nltk.stem import WordNetLemmatizer
test_word = 'working'
word, word_pos = nltk.pos_tag([test_word])
wordnet_pos = get_wordnet_pos(word_pos) or word net.NOUN
lemmatizer = WordNetLemmatizer()
lemmatize.lemmatize(test_word, pos = wordnet_pos)#the output will be working
```

###  numpy中拼接两个数组 
``` bash
Np.vstack([a_vec, b_vec])#纵向
Np。hstack([a_vec, b_vec]#横向
```

### python多线程  
参考网址：https://www.cnblogs.com/bethansy/p/7965820.html
一个例子：
``` bash
import threading

#申明类
Class fea_read_load(threading.Thread):
	def __init__(self,func, para):
		threading.Thread.__init__(self)
		self.para = para
		self.func = func
		self.result = self.func(self.para)
	def get_result(self):
		return self.result

#调用过程
def main():
	for query in querys
		fea_t = fea_read_load(load_vis_fea, query)#其中laod_vis_fea是一个函数，query是参数
		fea_t.start()#开启线程
		fea_t.join()#等待线程结束
		#结束之后才能获取结果
		fea_dict = fea_t.get_result()#获得读区额特征字典
```


 ### error:  python read the chinese file, open(filepath,encoding='UTF-8'), however, there are still errors occuring: "UnicodeDecodeError: 'gbk' codec can't decode byte 0x80 in position 205: illegal multibyte sequence"
  solution : don't open the file with "UTF-8" mode, and open(filepath, 'rb'), read the content line by line. for example: line=f.readline(), line=line.decode('UTF-8',  'ignore' ), since the default parameter in decode function is strict, change it as "ignore" is ok.
    *

 ###  pytorch changes the network detail in squence while maintain the pretrained parameters 
 for example, we want the change the output class number for vgg16, while retrain the pretrained parameters. Since the vgg16 classifier consist of a squence, it is troublesome to change it. we can do by .children() API:
 ``` bash
vgg=models.vgg16(pretrained=True)
children=vgg.classifie.children()
*obtain the layer that want to be saved *
removed=list(vgg.classifier.children())[:-1]
model.classifier=torch.nn.Sequential(*removed)
*then, add the new fc layer*
vgg.add_module('fc', torch.nn.Linear(4096, out_num))
 ```
 by this way, we can change the class number, while retained the pretrained parameters
   
 ### pytorch error: forward() missing 1 required positional argument: 'input' 
we want to declare a net using the vgg16's feature part as a component of our network:
``` bash
vgg16=models.vgg16(pretrained=True)
class MyNET(nn.Module):
    def __init__(self):
        super(NET, self).__init__()
        self.features=vgg16.features()
        self.fc=vgg16.classifier()
    def forward(self, x):
        x=self.features(x)
        x=self.fc(x)
        return x
```
if we declare the network like above, the error will occur. Because self.feature = vgg16.features() means forward the data by vgg.features, however,we just want to declare the network.
  suolution:   self.features = vgg16.features, self.fc = vgg16.classifier

 ###  About converting the Ascii code (like '\xe8\x87\xaa\xe5\xb7\xb1') to Chinese  
``` bash
f = open(file, 'r')
line = f.readline()
we can direcyly print the string, if line is encoded by Ascii, the output will be chinese
print(line)
```

 ###  convert the pytorch data type  :
``` bash
t1=torch.eye(3)
convert t1 to ByteTensor
t1_byte = t1.type(tf.ByteTensor)
```

 ###  pytorch Error: save_for_backward can only save input or output tensors,  
this error occurs since some medium tensor is not packed by Variable, pack it by Variable will be ok

 ### pytorch Error: when use the nn.embedding to embedding the word(in rnn like LSTM), the id tensor type is LongTensor  

 ### pytorch tips: when you utilize the rnn.pack_padded_sequence() to pack the input sentence, the sen_len parameter should be a LongTensor type, instead of a Variable  
for example: rnn.pack_padded_sequence(word_embedding, list(torch.squeeze(sentent_len_tensor)), batch_first=True), where the sentence_len_tensor is a LongTensor type rather a Variable, if its a Variable, some error will occur,

 ###  about the output for LSTM  :
assume the *packed* is a output by rnn.pack_padded_sequence(), forward the LSTM
``` bash
output_packed = model.lstm(packed)
```  
the output_packed is a tuple, the first element is an packedSequence, the second element contains two components: they are *hn* output and *cn* respectively

then unpacked the output by rnn.pad_packed_squeeze()
``` bash
output_unpacked = rnn.pad_packed_squeeze(output_packed[0])
```
the output_unpacked is also a tuple, the first element *output_unpacked[0]* is the requried output Variable with size batch_size x sentence_len x lstm_hidden_size, the second element is a list containing the word number of each sentence.

 ### pytorch报错 
```
Cublas runtime error : library not initialized
```
一般是显存不够

 ###  pytorch错误 
``` bash
device-side assert triggered
```
查询网络中用到的索引是否越界（word embedding的index，以及分类类别号的index）

 ###  windows 中错误 BrokenPipeError: [Errno 32] Broken pipe  
这是windows多线程的问题，在dataloader中将num_work参数设置为0即可解决

 ###  pytorch中多标记分类的损失函数可以使用  
  ``` bash
  Loss = torch.nn.BCEWithLogitsLoss()
  #或者
  loss = torch.nn.BCELoss()
  ```
注意网络输出的最后一层不要使用softmax，因为这不是单标签分类，使用sigmoid结合BCELoss或者直接输出结合BCEWithLogitsLoss进行训练

 ###  pytorc将gpu变量移动到cpu中并且存起来，超出显存 
 场景如下：一系列书序需要通过网络，网络和输入数据在GPU上计算，在循环中想要将模型的输出移动道内存中，并且存储起来，直接运行gpu会累加并最后超出： 
 ``` bash
 #错误做法：
 out = model(input)
 out = out.cpu()
 save_list.append(out)
 #正确做法
 out = model(input)
 out = out.cpu().detach().numpy()/out.data.cpu().numpy()
 save_list.append(out)
 ```
需要使用detach函数先将变量从图中剪下来，之后移动到cpu上进行存储。

 ### pytorch网络两次输出不一样  
  在pytorch中，如果没有申明model.eval(), 即使是相同模型参数，相同输出，两次的输出结果也不一样，请注意。

 ### python 多线程 
一个简单例子
``` bash
  import multiprocess
  
  P1 = multiprocess.Process(target=Function1, arg s=(input,))
  P2 = multiprocess.Process(target = Function2, args=(input2,))

  p1.start()
  p2.start()

  p1.join()
  p2.join()
```
 ### 使用多线程进行图片下载  
``` bash
import json
import urllib#python 3使用urllib.request
import os
import sys
import multiprocessing as multiprocess
from threading import Thread
import threading

image_save_pth = './images/'
if not os.path.exists(image_save_pth):
    os.mkdir(image_save_pth)
def download_image(data_lines, start_index, end_index, split):
    #if split.startswith('test'):
    #    split = split+'all'
     
    all_data_lines = data_lines[start_index:end_index]
    count = 0.0
    thread_name = threading.current_thread().name
    print(thread_name + ' is downloading images...')
    length = len(all_data_lines)
    for i in range(0, length):
        line = all_data_lines[i]
        tmp_dict = json.loads(line)
        url = tmp_dict['url']
        img_id = tmp_dict['img_id']
        if os.path.exists(image_save_pth + str(img_id) + '.jpg'):
            continue

        flag = True
        while flag:
            try:
		#python 3使用urllib.request.urlretrieve(~,~)
                out = urllib.urlretrieve(url, image_save_pth + str(img_id) + '.jpg')
                flag = False
            except:
                flag = True
        #sys.stdout.write('[%d/%d]-th in [%s], [%f] percent is finished!\r'%(i+1, length, split, (i*1.0 + 1) / length *100))
        #sys.stdout.flush()
    
def main():
    #data_split = ['train', 'dev', 'test']
    #for item in data_split:
    #download_image(item)
    #p = multiprocess.Process(target = download_image, args = ('train',))
    #p.start()
    #p.join()
    split = 'train'
    path = '/Users/wang/SCAN/data/coco_precomp/' + split + '_data_coco.json'
    data_lines = open(path).readlines()

    num_thread = 100
    all_length = len(data_lines)
    step = all_length // num_thread
    start_index = 0
    end_index = step
    t = []
    count = 1
    while end_index != all_length:
        thread = Thread(target = download_image, args = (data_lines,start_index, end_index, split), name = 'Thread ' + str(count))
        t.append(thread)
        start_index=end_index
        end_index = min(start_index + step, all_length)
        count += 1

    for th in t:
        th.start()
    for th in t:
        th.join()


if __name__ == '__main__':
    main()
```

 关于更多python多线程的使用参照：https://blog.csdn.net/u012969412/article/details/82768882
 示例：https://blog.csdn.net/SeeTheWorld518/article/details/49639651
 基础用法：https://thief.one/2016/11/23/Python-multiprocessing/
 mutliprocess multiprecess.dummy和Thread的区别参照：https://www.zhihu.com/question/44738028

 ### python并行简单例子  
``` bash
from joblib import Parallel, delayed
import math

def my_fun(i):

    return math.sqrt(ii*2)

#Parallel和delayed配合使用进行多线程
#n_jobs设置多少个并行线程
Parallel(n_jobs=2)(delayed(my_fun)(i) for i in range(num))
```

 ### numpy从字符串中读取array 
 ``` bash
 import numpy as np
 string = '1 2 3 6'
 arr = np.fromstring(string, dtype = int, sep = ' ')

 ```

 ### pytorch runtimeError: DataLoader worker (pid13)is killed by signal : buss Error  
解决方法：在DataLoader去除num_workers
``` bash
dataloader_train = data.DataLoader(train_dataset, batch_size=64, shuffle=True, num_workers=2)
改为：
dataloader_train = data.DataLoader(train_dataset, batch_size=64, shuffle=True)

```

 ### pytorch错误：cannot assign module before Module.__init__() call  
定义的networ中的init没有使用super(network, self).__init__()

 ### pytorch错误：CUDNN_STATUS_BAD_PARAM 
输入网络的图片的size不对
一些奇怪的错误可以参考这个网址：https://blog.csdn.net/VictoriaW/article/details/72636375

 ### tensorboard的使用  
如果是在pytorch中使用tensorboardX，使用tensorboard命令还需要安装tensorflow，如果是tensorlow中使用tensorboard则不需要（浏览器中输入：localhost:
6006）

 ### python Error：bad file descriptor  
Nohup运行程序的时候代码中有 pdb.set_trace()没有注释掉

 ### docker中pytorch错误：Unexpected bus error encountered in worker. This might be caused by insufficient shared memory (shm)  
docker中运行docker把batch size设置过大，shared memory不够
解决办法：将Dataloader的num——workers设置为0

 ### 使用pytorch多GPU注意的问题  
1，第一种方式
``` bash
#使用多gpu训练
net = my_model()
net = torch.nn.DataParallel(net, device_ids=[0,1,2,3])
net = net.cuda()

#模型的保存与load--方法1:保存的时候做处理
由于被DataParallel是一个新的对象，所以保存的时候需要，直接提取出state_dict保存
torch.save(net.module.state_dict(), path)#load的时候直接按照单gpu的来训练

#方法2:load的时候做处理
1), 利用torch.nn.DataParallel包起来进行训练的模型在，载入的时候也要使用DataParallel封装，因为经过封装之后的参数字典中的key多出了一个module前缀：
``` bash
net = my_model()
net = torch.nn.DataParallel(net)
net.load_state_dict(torch.load(path))
```
2)，如果模型被DataParallel封装，访问模型的某一个模块必须加上module，例如myNet.module.fc

```
2，第二种方式 torch.nn.parallel.DistributedDataPrallel
推荐使用,使用步骤为：
``` bash
#1， 加入命令行参数并初始化
parser = argparser.ArgumentParser()
Parser.add_argument('--local_rank'm type=int, default=0)
opt = parser.parser_args()

torch.distributed.init_process_group(backend='nccl', world_size=1, rank=0)

#2, 使用DistributionSampler进行数据分享
dataset = DataSet(...) #your datasets
Sampler = torch.utils.data.distributed.DistributedSampler(dataset)
Data loader = DataLoader(dataset, batch, shuffle=False, num_workers=num, pin_memoty=True, drop_last=False, sampler=Sampler) #其中pin_memory=True是必要的

#3接下来部署模型进行分布式训练
model=torch.nn.parallel.DistributedDataParallel(model)

#最后，命令行中需要加torch.distributed.launch
CUDA_VISIBLE_DEVICES=0,1 python -m torch.distributed.launch --nproc_per_node=2 train.py

note: a_tensor.type(Tensor)会引起inplace error错误;
Dataloader的参数shuffle 要设为False
```


 ### 使用多gpu训练网络 segmentation Error（段错误） 
主要是因为docker中的共享内存不够

 ### torch.load模型错误：UnicodeDecodeError: 'ascii' codec can't decode byte 0xe9 in position 6 
原因：如果是在python 2中的保存的模型，python3环境中load就会出现上述的错误
解决方法：
``` bash
from functools import partial
import pickle
pickle.load = partial(pickle.load, encoding="latin1")
pickle.Unpickler = partial(pickle.Unpickler, encoding="latin1")
```

 ### ImageNet上的均值方差 
``` bash
 normalize = transforms.Normalize(mean=[0.485, 0.456, 0.406],
                                   std=[0.229, 0.224, 0.225])
```

 ### pytorch输出损失或者梯度为nan  
检查：1， loss计算
2，检查梯度计算,例如sqrt(0), log(0)等均会导致nan
可以使用torch.autograd.detect_anormaly
``` bash
with torch.autograd.detect_anomaly():                                              │
	out = model(input)
	optizer.zero_grad()
	loss. =Loss(out,target)
        Loss.backward()
        optimizer.step()
```
该模块自动检测grad计算异常的地方

 ### import cv2错误:mportError: libSM.so.6: cannot open shared object file: No such file or directory"  
解决方法：
``` bash
Apt-get install update
apt-get install -y libsm6 libxext6 libxrender-dev
Pip install opencv-python
```
如果没有sudo权限，可以暂时安装无头依赖版本
``` bash
Pip install opencv-python-headless
#if you need, also install the contrib
Pip install opencv-contrib-python-headless
```

 ### c++版本opencv & boost库安装 
``` bash
#step 1
1. sudo apt-get install cmake
2. sudo apt-get install python-devel numpy
3. sudo apt-get install gcc gcc-c++

#Step 2
1.sudo apt-get install gtk2-devel
2. sudo apt-get install libv4l-devel
3. sudo apt-get install ffmpeg-devel
4. sudo apt-get install gstreamer-plugins-base-devel

#step 3
git clone https://github.com/opencv/opencv.git

#step 4
1. cd ~/opencv
2. mkdir build
3. cd build

#step 5
1. cmake ../
2. make
3. sudo make install

#======安装boost==========
sudo apt-get install libboost-all-dev

```

 ### pytorch使用fp16节省显存  
* 方法1: 利用apex
主要三部分需要改动：初始化模型，反传损失，梯度裁剪，使用方法：
``` bash
from apex import amp
#declare the model
model = MyModel()#nn.Module子类
#declare the optimiser
optimizer = torch.optim.Adam(model.paramerters(), lr= 0.0001)
#initlize the model using the amp
model, optimiser = amp.initialize(model, optimiser, opt_level='O1',
keep_batchnorm_fp32=True, loss_scale='dynamic')#优化级别一般使用‘O1’
#反传loss的时候
loss = loss_function(model_out, ground_truth)
with amp.scale_loss(loss, optimizer) as scaled_loss:
    scaled_loss.backward()
#如果模型中有梯度裁剪的部分需要将普通的
clip_grad_norm(model.paramters(), clip_grad)
#改为：
clip_grad_norm(amp.master_params(optimizer), clip_grad)
```
如果模型不是nn.Module,例如model为object，包含两个子类img_enc， txt_enc为nn.Module，那么模型初始化改为
``` bash
model = MyModel()
optimizer = torch.optim.Adam(model.paramerters(), lr= 0.0001)
#initialise the model using the amp
model.img_enc, model.txt_enc, optimiser = amp.initialize([model.img_enc, model_txt], 
optimiser, opt_level='O1', keep_batchnorm_fp32=True, loss_scale='dynamic')
```
*方法2: 利用pytorch(>1.6)的封装
```
import torch

#训练开始之前
Scaler = torch.cuda.amp.GradScaler()

#训练时
for epoch in range(total_epoch):
    for data in enumerate(train_loader):
        
	optimizer.zero_grad()        
        #模型inference
	with torch.cuda.amp.autocast():
              y_pred = model(data)
              loss = loss_fn(y_pred, gt)

        scaler.scale(loss).backward()

        scaler.step(optimizer)
        scaler.update()

```

From apex import amp报错：TypeError: Class advice impossible in Python3
Uninstall the old apex
``` bash
pip uninstall apex
git clone https://www.github.com/nvidia/apex
cd apex
python setup.py install
```


 ### pytorch设置学习率衰减 
``` bash
from torch.optim import lr_scheduler

optimiser = torch.optim.Adam(net.parameters(), lr = 0.005)
Scheduler = lr_scheduler.StepLR(optimizer, 10, 0.1)#没10轮学习率乘以1

for n in range(epochs):
    scheduler.step()
    .....
```
也可以手动调整learning rate
``` bash
def adjust_learning_rate(opt, optimizer, epoch):
     """Sets the learning rate to the initial LR
        decayed by 10 every 30 epochs"""
     lr = opt.learning_rate * (0.1   (epoch // opt.lr_update))
     for param_group in optimizer.param_groups:
         param_group['lr'] = lr
```

 ### tensorflow load checkpoint error: DataLoss Error  
解决方法：
如果你的checkpoint文件名称是model.ckpt.data-00000-data-00001,那么sess.restore中掺入的参数只需要是
model.ckpt而不是整个的文件model.ckpt.data-00000-of-00001，如果传入整个文件名称就会报以下错误：
Data loss: not an sstable (bad magic number): perhaps your file is in a different file format and you need to use a different restore operator?，

 ### pytorch设置随机种子  
``` bash
random.seed(seed)
np.random.seed(seed)
torch.manual_seed(seed)
torch.cuda.manual_seed(seed)
torch.backends.cudnn.deterministic=True #使用确定性卷积,会极大的降低速度
torch.backends.cudnn.benchmark =True #自动搜索最快的操作，用于提速
```
tensorflow随机种子设置：tf.set_random_seed(seed)

 ### tensoflow一次奇怪的bug记录：libdevice.compute_30.10.bd not found 
当使用instanceNorm或者囤cated_normal_initializer()初始化的时候出现了报错。
解决方案：
找到该文件，并将它复制到你的程序运行目录下，但是只可以解决instancNorm的问题，无法解决truncated_normal_initializer的问题，
该文件啃可能的的位置：1 conda环境lib目录下例如： /home/yaxiong/anaconda3/envs/tensorflow/lib
2 cuda目录下：/usr/local/cuda8/

 ### tensorflow将tfrecords load进入内存中 
``` bash
dataset = tf.data.TFRecordDataset(files=[your_data_path])
dataset = dataset.cache()
```

 ### tensorflow error： Loaded runtime CuDNN library: 7.1.4 but source was compiled with: 7.2.1. 
如果你没有操作过cudnn相关的目录和路径，该错误很有可能是tensorflow版本的问题，例如1.12.0和1.14.0，尝试将版本降级看看

 ### pytorch下载预训练参数输入：URL error  
可能是因为python版本原因，python2.7有这个错误，解决办法：
``` bash
Import torchvision.models as models
From torch vision.models.resnet import model_urls

model_urls['resnet152'] = model_urls['resnet152'].replace('https://', 'http://')
Resnet152 = models.renset152(pertrained=True)
```

 ### python中找到字符串中子串的位置 
Python中没有直接实现这个的函数，所以可以按照如下做法得到：
``` bash
Import re
#返回在super_string中sub_string 的下标
Index = [m.start() for m inre.finditer(sub_string, super_string)]
```

 ### python得到路径下所有的满足条件的文件  
例如文件夹A下存放了10个文件夹，每个文件夹中有一些图片，一次性可以读到所有的文件：
``` bash
Import glob
all_images = glob.glob(r'.A/*/*.jpg')#得到所有文件的路径
```

 ### python中的map函数，filter函数 
*1， map函数：map(function, iterable) *
map函数会更具参数序列中的每一个元素调用function函数,例子：
``` bash
Out = map(lambda x:x*2, [1,3,5,7])#每个元素翻倍
Out = map(lambda x, y: x+y, [1,3,5], [2,4,6])#对应元素相加
```
*2，filter函数:filter(function, iterable) *
Filter函数用于过滤序列，过滤掉不符合条件的元素，其中function的返回值必须是true或者false
``` bash
Out = filter(lambda x:x.startswith('wang'), ['wang', 'wangyx', 'yaxiong'])
```

 ### python使用seaborn画热力图 
``` bash
Import matplotlib.pyplot as plt
Import senborn as sns
Import numpy as np

mat = np.random.rand(16,16)
ax = sns.heatmap(mat, camp ='rainbow')#cmap设置色系，可以参考https://blog.csdn.net/weixin_39777626/article/details/95892284
#保存热力图
ax.figure.savefig('hmx.png', transparent=True, bbox_inches='tight')

#只保存图，去除边框

plt.imshow()
plt.axis('off')#去掉坐标系
plt.savefig('test.png', bbox_inches='tight', pad_inches=0.0)
```

 ### python中matplotlib中的画图常用语法 
``` bash
Import matplotlib.pyplot as pat
styles = pet.style.available #所有的画图风格，其中第22， 24比较好看，是一个列表，
plt.style.use(styles[args.style])#应用风格，对所有的绘图
fig = plt.figure()
ax1 = plt.subplot(2,1,1)
#设置不显示上方和右边的边界
#ax1.spines['right'].set_visible(False)
#ax1.spines['left'].set_visible(False)
ax1.set_title('Discriminator Loss')#设置子图的标题
posi_d, = plt.plot(np.arange(len(loss_d)), np.array(posi_loss_d), label='Dis-NRSC', color='r    ')
our_d, = plt.plot(np.arange(len(loss_d)), np.array(loss_d), label='Dis-RSC',color = 'g')
plt.legend(handles =[posi_d, our_d],loc='best',facecolor='blue')#加图例
plt.grid()
#plt.xlabel('epoch')
#plt.ylabel('loss')
ax1.set_xlabel('epochs',fontsize=12)#设置坐标轴的名称，字体大小
ax1.set_ylable('Gen loss',fontsize=12)#设置坐标轴的名称，字体大小
plt.savefig('test.png')#保存图片

#绘制第二个子图，类似
ax2=plt.subplot(2,1,2)
ax2.set_title('Generator Loss')
posi_g, = plt.plot(np.arange(len(loss_d)), np.array(posi_loss_g),label = 'Gen-NRSC', color='    r')
our_g, = plt.plot(np.arange(len(loss_d)), np.array(loss_g), label = 'GEN_RSC', color = 'g')
plt.legend(handles =[posi_g, our_g],loc='best',facecolor='blue')
plt.grid()
ax2.set_xlabel('epochs', fontsize=12)
ax2.set_ylabel('Gen loss', fontsize=12)
```
Pyplot绘图剔除白边
``` bash
#bbox_inches='tight'去除坐标轴所占用的空间
#dpi用来控制分辨率，600是很高的分辨率
#pad_inches=0.0 重点，去除占用的白边
plt.savefig('test.png', bbox_inches='tight',dpi=600,pad_inches=0.0)

```

 ### python图片显示几秒后再关闭  
```

for im in all_images:
    Img = Image.open(im)
    plt.figure()
    plt.ion()
    plt.axis('off')
    plt.imhsow(img)
    plt.pause(2)#停顿两秒
    plt.ioff()

plt.clf()
plt.close()

```

 ### python中使用pysnooper进行调试 
先说用pip install pysnooper
使用方式
``` bash
#作为函数的装饰器进行使用
Import pysnooper
@pysnooper.snoop()
def func():
   ...

主要参数：
输出到调试信息到文件中：pysnooper.snoop('debug.log')
Prefix参数给函数命名，让挑食信息更加清晰:pysnooper.snoop(prefix='my_fun')
watch/watch_explode:查看参数的值/展开所有的值：pysnooper.snoop(watch='var1')，查看多个参数设置为watch=('var1','var2')
Depth:设置出打印的代码行数，默认为1:pysnooper.snoop(depth=2)
```

 ###  使用fire库进行命令行解析 
安装：pip install fire
使用实例
``` bash
import fire

def print_fun(path):
    print(path)

def add_fun(a,b):
    return a+b

#默认将所有的函数都进行了打包
fire.Fire()

#---------------------------------
在命令行中运行时需要指定使用哪个函数：
python test.py print_fun --path  'a path string'#也可以不要参数--path
或者 python test.py add_fun --a 2 --b 3 #同样的也可以不要参数--a and --b 值按照顺序分配

#------------------------------
也可以在py文件中指定打包哪个函数
fire.Fire(print_fun)
#则在命令行中不需要传递函数参数
python test.py --path 'a path string' 
```

 ### python setup.py install/make install错误：gcc: error: pycocotools/_mask.c: No such file  
缺少cython遇pycocotools包
``` bash
pip install cython
pip. Install pycocotools
```

 ### python序列化模快json与pickle  
``` bash
#两种都是对象在前，文件在后
json.dump(object, open('files.json', 'w'))
pickle.dump(object, open('files.pkl', 'wb'))
```

 ### git同步本地与github远端仓库 
* 本地与远程github链接配置请参见：https://www.cnblogs.com/sdcs/p/8270029.html
* 同步的步骤
``` bash
1, mkdir git_test #创建文件夹，进入该文件夹
2，git init #文件夹初始化为可供管理的仓库，之后就可以创建文件
3，touch test.txt #创建一个文件
4, git add test.txt/. #将test.txt文件/所有文件添加到本地仓库
5, git commit -m 'this is a test file' #提交改动，并增加注释
6, git remote add origin git@github.com:wangyxxjtu/JHR.git #与github的仓库建立连接
7, git push （-u） origin (+)master #推送 -u 当连接的仓库JHR.Git为空时才使用， 否则不会使用，+表示强制推送 （git push -f -u origin master 另一种强制提交方式）
```
特别注意，如果当前仓库不包含所有想要同步的远端仓库的文件，会报错：
``` bash
! [rejected]        master -> master (fetch first)
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
```

 ### 将本地的一个文件夹下的代码作为一个项目上传到指定的仓库中  

``` bash
1 在github上新建一个项目AINet
2 在要操作的文件夹下输入如下的命令
  a git init (初始化，令git管理改文件夹)
  b git add .(将当前文件夹下的所有文件加入本地仓库，如果只是想加入某个文件，将.换成文件名称)
  c git commit -m 'AINet code-Official'
  d git remote add origin https://github.com/wangyxxjtu/AINet.git (将本地仓库与远程仓库进行关联)
  e git push -u origin master (上传项目到github)
```
#解决方案是：想将两个仓库合并到本地，如果本地的文件github仓库没有，那么该文件会通过该命令删除，慎重使用
Git pull --rebase origin master

一般来说更新远端需要四步
``` bash
1，建立远端连接，并改动文件，如果项目只从github上通过git clone 命令下载下来的，第一步可以省略
git remote add origin git@github.com:wangyxxjtu/JHR.git

2, 添加改动的文件或者文件夹到仓库
git add file_name(dir_name/)

3, 提交改动
git commit -m '提示信息'

4, 执行提交的改动
git push origin master
```

 ### git常用的需求以及命令 
移除文件
``` bash
git rm file_name #删除文件,文件夹加r
git commit -m '...' #提交改动
git push origin master #执行改动
```
查看状态信息
``` bash
git status
```
代码回退某个代码提交历史中，需要满足：在当前工作仓库并且没有提交到Github,
```bash
git reset --hard HEAD^ #回退到上个版本
git reset --hard HEAD^^ #回退到上上个版本
git reset --hard HEAD~10 #回退到之前10个版本
git reset --hard commit-id #回到提交ID的版本
git log --graph / git log --stat #查看提交历史和commit-id
```
这里的回退，指的是更具git log --graph第一commit-id进行的回退，并不一定会回退到之前的改动版本，移动前务必调研清楚
特别注意，这里一reset，在这个日期之后的commit-ID使用git log --graph命令就看不到了，使用：
``` bash
git reflog #可以看到命令的log，从中可以查看进入的commit-id历史
```
查看当前工作目录的文件和远程github上的不同
``` bash
git diff HEAD -- main.py
```
放弃这此所做的修改，回到最近一次commit的结果
``` bash
git checkout main.py
```
将文件从暂存区撤回来，即抵消git add main.py操作,撤销文件的修改详细操作可以[参见这里](https://blog.csdn.net/kikajack/article/details/79846098)
``` bash
git reset main.py
```
查看一些信息
``` bash
git log --follow main.oy #查看文件的版本历史
git whatchanged main.py #查看文件每次提交的修改历史
git log -p main.py #查看文件每次的不同
git blame main.py #显示文件什么时候被什么人修改过
Git diff #显示暂存区和工作区的差异
git show --name-only commit-id #显示某次提交发生变化的文件名
git show commit-id #显示某次提交数据和内容的变化
git show commit-id1 commit-id2 #显示两次提交内容的差异
git diff HEAD #显示工作区和最新一次提交的差异
```

 ### 让自己搭的django app公网可访问：holer使用 
一般来说,自己电脑上搭建的app，在外网无法访问，借助工具holer可以实现;
假设现在你已经开发好了一些django应用，并且启动了服务：
python manage.oy runserver 127.0.0.1:8080 #后边是网址，但是为局域网
为了让该应用，可以让所有人访问,得进行如下配置
``` bash
step 1： 安装java 1.8或者更高版本
step 2: 下载[holer软件](https://link.zhihu.com/?target=https%3A//github.com/wisdom-projects/holer/tree/master/Binary/Java)
Step 3: 解压zip文件，并修改配置文件：hole-client/conf/holer.conf
设置HOLER_ACCESS_KEY如下：
HOLER_ACCESS_KEY=HOLER_CLIENT-2F8D8B78B3C2A0AE
HOLER_SERVER_HOST=holer.org
上面的holder_access_Key,参照下表设置：
访问密钥	                             访问域名	           公网地址	 本地地址	    使用场景
HOLER_CLIENT-2F8D8B78B3C2A0AE	holer65530.wdom.net	holer.org:65530	127.0.0.1:8080	WEB
HOLER_CLIENT-3C07CDFD1BF99BF2	holer65531.wdom.net	holer.org:65531	127.0.0.1:8088	WEB
HOLER_CLIENT-2A623FCB6E2A7D1D	holer65532.wdom.net	holer.org:65532	127.0.0.1:80	WEB
HOLER_CLIENT-AF3E6391525F70E4	             N/A	holer.org:65533	127.0.0.1:3389	远程桌面
HOLER_CLIENT-822404317F9D8ADD	             N/A	holer.org:65534	127.0.0.1:22	SSH
HOLER_CLIENT-27DD1389DF1D4DBC	             N/A	holer.org:65535	127.0.0.1:3306	数据库
我们启动的网址和端口，为127.0.0.1和8080所以使用第一个密钥
step 4:启动服务
Cd holer-client/bin
bash startup.sh(停止为bash shutdown.sh)
如果为windows直接点击startup.bat/shutdown.bat
```
那么当前的网址http://127.0.0.1:8080 等价于 http://holer.org:65530, 输入该网址就等于输入了http://127.0.0.1:8080
更加详细的教程可以看[这里](http://blog.wdom.net/article/28)

 ### pytorch使用data_prefetcher加速数据读取 
首先声明一个类
``` bash
class Data_Prefetcher():
     def __init__(self, loader):
         self.loader = iter(loader)
         self.dataloader = loader
         self.stream = torch.cuda.Stream()
         #self.fetch_next()

     def fetch_next(self):
         try:
             self.batch_data = next(self.loader) #imgs是一个字典形式
         except StopIteration:
             self.batch_data = None
             self.loader = iter(self.dataloader) #如果迭代器到头了重新开始
             return

         with torch.cuda.stream(self.stream):
             for item in self.imgs:
                 self.batch_data[item] = self.batch_data[item].cuda(non_blocking=True)

	      #如果不是字典，直接是tensor，直接self.img = self.img.cuda(non_blocking=True)
         return self.batch_data
```
训练的写法对应的更新为
``` bash
dataloader = DataLoader(dataset= ...) #定义你的dataloader
data_prefetcher = Data_Prefetcher(dataloaader)
for epoch in range(epochs):
	batch_data = data_prefetcher.fetch_next()
	while batch_data is not None:
		images = batch_data['imgs']
 		labels = batch_data['labels']
		......

		batch_data = data_prefetcher.fetch_next()

```

 ### tensorflow使用预训练的网络作为网络的一部分 
step 1: 下载所需要的模型和对应的模型代码（tar.gz文件和code），并解压：https://github.com/tensorflow/models/tree/master/research/slim

step 2: 载入参数--tf.1.12.0
``` bash
from tensorflow.python import pywrap_tensorflow
from resnet_v2 import resnet_v2_152, resnet_arg_scope
slim = tf.contrib.slim
def load_checkpoint(path='./resnet_v2_152.ckpt', sess=None):
    reader = pywrap_tensorflow.NewCheckpointReader(path)
    saved_vars = reader.get_variable_to_shape_map()
    variables = tf.trainable_variables()
    count =0
    hit_list = []
    if sess is None:
        sess = tf.get_default_session()#传入自己的sess，自己网络所在图
    vars_to_restore = []
    for v in variables:
        v_name = v.name[:-2] if v.name.endswith(':0') else v.name
        for k in sorted(saved_vars):
            k_name = k[:-2] if k.endswith(':0') else k
            if v_name.replace('cpu_variables/','') == k_name:#名称匹配来赋值
                #print(v_name, k_name)
                count += 1
                hit_list.append(v.name)
                para = reader.get_tensor(k)
                vars_to_restore.append(v.assign(para))

    sess.run(vars_to_restore)
    #saved_names = sorted(list(saved_vars.keys()))
    #filter = [item for item in saved_names if 'conv1' in item]
    print(count, len(hit_list))
if __name__ == '__main__':
    input = tf.placeholder(tf.float32, [6, 128,128,3])
    input_arr = np.random.rand(6,128,128,3)
    #使用该var_scope和类别数设置为1001是必须的
    arg_scope = resnet_arg_scope()#官方的设定的一些参数，包括卷积的参数配置    
    with slim.arg_scope(arg_scope):
      output, end_points = resnet_v2_152(input, 1001, is_training=False)

    load_checkpoint()    
    sess = tf.InteractiveSession()

    sess.run(tf.global_variables_initializer())
    out_arr = sess.run(output, feed_dict={input:input_arr})
    print('Done')

```

 ###  keras进行数据扩充，同时对图像和mask进行变换 
``` bash
#定义数据扩充的参数
aug_args = dict(
rotation_range=90.,
width_shift_range=0.4,
height_shift_range=0.4,
zoom_range=0.5, horizontal_flip=True, vertical_flip=True)
#利用参数分别定义image_generator
imDataGen = ImageDataGenerator( aug_args)
maskDataGen = ImageDataGenerator( aug_args)
maskDataGen1 = ImageDataGenerator( aug_args)
maskDataGen2 = ImageDataGenerator( aug_args)
maskDataGen3 = ImageDataGenerator( aug_args)
#如果imageDataGenerator中制定了normalization或者白化参数，则需要进行fit
#imDataGen.fit(im_train, augment=True, seed=random_seed)
#maskDataGen.fit(mask_train, augment=True, seed=random_seed)

#接下来只需要进行在generator中制定seed就可以同步的变换
image_generator = imDataGen.flow(x=im_train, seed=random_seed, shuffle=True, batch_size=batchnum)
mask_generator = maskDataGen.flow(x=mask_train, seed=random_seed, shuffle=True, batch_size=batchnum)
mask_generator1 = maskDataGen.flow(x=mask_train1, seed=random_seed, shuffle=True, batch_size=batchnum)
mask_generator2 = maskDataGen.flow(x=mask_train2, seed=random_seed, shuffle=True, batch_size=batchnum)
mask_generator3 = maskDataGen.flow(x=mask_train3, seed=random_seed, shuffle=True, batch_size=batchnum)

#如果网络有多个输出，也需要再次封装为Geneartor
def multi_output_generator(image_generator, mask_generator1, mask_generator2, mask_generator3, mask_generator4):
     while True:
         im = image_generator.next()
         gt1 = mask_generator1.next()
         gt2 = mask_generator2.next()
         gt3 = mask_generator3.next()
         gt4 = mask_generator4.next()

         yield im, [gt1, gt2, gt3, gt4]

final_train_generator = multi_output_generator(image_generator, mask_generator1, mask_generator2, mask_generator3, mask_generator)
#将最后封装好的generator传入fit_generator函数中进行训练
hist = model.fit_generator(final_train_generator, steps_per_epoch=len(train_index)//batchnum,
shuffle=True, epochs=epochnum,validation_data=[im_val, [mask_val1, mask_val2, mask_val3, mask_val]], callbacks=[checkpoint])
h = hist.history
```

 ### keras中自定义损失函数 
``` bash
import keras.layers as KL
From losses import My_Loss
From keras import Model
#step1:写好损失函数并利用Lamdab进行封装
my_loss = KL.Lambda(lambda x:My_Loss(*x), name='my_loss')([input1, input2, input3])
#step2:正常定义模型，output中需要输出my_loss
model= Model(input=[input1, ....], output=[output1, ...., my_loss])
#step3:得到loss的输出并加入到模型的loss之中去
loss = model.get_layer('my_loss').output
model.add_loss(loss)
#step4:loss的对应输出定义为None
losses={'out1_name':loss_fun, ...., 'my_loss':None}
weights={'out1_name':1, ..., 'my_loss':1}
最后进行模型compile即可完成
model.compile(optimizer=opt, loss=loss, loss_weights=loss_weights,metrics=[losses.dsc])
```

 ### pytorch对不同的图像进行同样的数据增广操作  
利用设置随机种子来实现
``` bash
import random
import numpy as np
...
def getitem():
    img = ....
    Mask = ...
    
    seed = np.random.randint(123456)
    random.seed(seed)
    img = self.transform(img)
    
    #再次设置transofrm
    random.seed(seed)
    Mask = self.transform(mask)
...

```

 ### keras打印不需要训练的参数 
``` bash
#model.compile之后
print(model.non_trainable_weights)
```

 ###  pytorch可视化相关 
首先需要安装tensor board: pip install tensorboard
1， from torch.utils.tensorboard import SummaryWriter错误： No module named "past",解决方法：
``` bash
Pip install -r tb-nightly
Pip install -r future
```

2, 启动tensorboard（tensorboard --logdir=./logs）报错： Tensorboard: ValueError: Duplicate plugins for name projector
``` bash
A, 下载该文件
wget https://raw.githubusercontent.com/tensorflow/tensorboard/master/tensorboard/tools/diagnose_tensorboard.py 

B, 运行
python diagnose_tensorboard.py

C, 遵照指示进行fix，一般得卸载一些东西并安装tensorflow
```

3 将服务器上写的log文件在本地显示 
``` bash
A, 在服务器上启动tensorboard
tensorboard --logdir=./logs --port 6006

B, 在本地进行端口映射(可以加f选项后台运行)
ssh -NL localhost:6006:localhost:6006 user@remote

N: no remote commands
L machine1:portA : machine2:portB: forward machine2:portB to machine1:portA

#查看哪些进程在使用指定端口:
lsof  -ti:6006

#释放指定端口
lsof -ti:6006 | xargs kill 9
```

 ### linux查看资源占用 
``` bash
#磁盘占用
sudo iotop

```

 ### tensorflow/keras对一个tensor安装行进行shuffle 
``` bash
# 不能直接tf.random.shuffle(tensor) shape: bs x height x width x channel
Out = tf.gather(tensor, tf.random.shuffle(tf.range(bs)))
```

 ### 利用imutils图像翻转 
```bash
#pip install imutils
#翻转30度并且超过边界的进行裁剪
im = cv2,imread(im_path) #im shape:256x256x3
im_rotate = imutils.rotate(im,30)

#翻转30度并且保留全图的内容，不进行裁剪
im_rotate_bound = imutils.im_rotate(im, 30)
```

 ### nii.gz图像的读取和显示 
``` bash
Import nibble as nib
From nilearn import plotting
#1读取数据
data = nib.load(nii_path)
data_arr = np.array(data.get_fdata(), np.uint8)

#2显示图像直接传送路径即可
plotting.plot_img(path)

```

 ### 可视化segmentation label 
``` bash
from PIL import Image
#open and image or generate an image from array
im=Image.fromarray(label_array, mode='P') #mode可以为'P'或者'L'

#定义colormap,数据有四个不同的label，则需定义4x3长度的list，每连续三个元素定义为一个颜色
#也可以从一个具有调色板的图像中得到color_map:
#color_map = im.getpalette()
color_map = [255,0,0,  0,255,0,  0,0,255,  128,0,128]
im.putpalette(color_map)

im.save('test.png')
```

 ### 可视化disparity map 
```
from PIL import Image

image = cv2.imread(disparity_path, cv2.IMREAD_GRAYSCALE)
plt.imshow(image, cmap='viridis') #virisdis为default setting
plt.axis('off')
plt.show()

#cmap是调色板参数，内置调色板参数较为好看的有：rainbow, plasma, cool, viridis
#参考https://matplotlib.org/stable/tutorials/colors/colormaps.html 
```

 ### pytorch中调用kl散度 
``` bash
Import torch.nn.Functional as F
Input = [0.2,0.3,0.4,0.2]
Target = [0.3,0.4,0.1,0.2]

Kl = F.kl_div(input.log(), target， reduction='mean') #input需要log，而不是直接传进去

# reduction='mean'，所有元素求平均，等价于：
kl = torch.mean(input * torch.log(input / (target+1e-10) + 1e-10))
#reduction='batchmean',bachsize求平均，为KL散度的严格定义，等价于
Kl = torch.sum(input * torch.log(input / (target+1e-10) + 1e-10)) / batch_size 

```

 ### 指数函数exp导致训练过程出现NaN 
指数函数torch.exp作用的数字过大/小，都会造成数值的溢出，从而导致训练的过程中出现NaN，
解决方法是进行数值裁剪
``` bash
torch.exp(value).clamp(min=1e-8,max=1e15),
```

 ### 保存uint16的图片 
``` bash
from PIL import Image
Im = Image.fromarray(data.astype(np.uint16)).save('16bit.png')
#读取时使用
im_r = cv2.imread('16bit.png', cv2.IMREAD_ANYDEPTH) #后面的参数很重要
```

 ###  将numpy中元素进行从小到大排列 
``` bash
Import numpy as np
Data = np.random.rand(3,4)
Ele, count = np.unique(Data, return_counts=True)
bd_index = np.argsort(count)
#the maximum ele
Ele[bd_index[-1]]
```

 ### pytorch (0.4.1) conda install error  
When. Install the pytorch 0.4.1 via conda: conda install pytorch=0.4.1 cuda90 -c pytorch
 got error: 
``` bash
PackageNotFoundError: Package not found: '' Dependencies missing in current linux-64 channels:
  - pytorch 0.4.1* -> mkl >=2018
  - pytorch 0.4.1* -> python >=3.7,<3.8.0a0
  - pytorch 0.4.1* -> cudatoolkit 9.*
```

Solution:
``` bash
conda config --add channels conda-forge
```

 ###  得到numpy中矩阵中出现次数最大的元素 
```bash
Mat = np.random.rand(3,4)
most_freq_ele = max(set(mat.flatten().tolist()), key=seg_labels.count)
```

 ###  利用pandas对csv文件进行操作  
```
import pandas as pd

csv_path = 'csv_file.csv'
#读入数据的几种常用操作
#最简单形式，csv_data时DataFrame类型数据
csv_data = pd.read_csv(csv_path, sep='\t')  

#可以设置列名
#设置header=None使用默认列名
csv_data = pd.read_csv(csv_path, sep='\t', names=['str1', 'str2', 'str3']) #

#读入时设置某列的值作为行名（索引）
#将会每行以该列作为索引
csv_data = pd.read_csv(csv_path, sep='\t', index_col='str1',  names=['str1', 'str2', 'str3'])

更多参数
skip_rows=[0,1]  读入时跳过指定的行
na_values=['male']. 将缺失的值（读取进来时NaN）填充为设定的值
nrows=3. 只读取前3行数据
chunksize=10. 分批读取，设置之后每次读取10个数据, 返回一个迭代器， csv_data.get_chunk(10)得到第11个块

#pandas设置分批去读，例如设置显示前10行
pd.options.display.max_rows=10

#写入csv文件
csv_data.to_csv('./my.csv', sep= ',', na_rep= 'WA', columns= ['sex','age','name','a'])

```

 ### csv读入之后，对DataFrame数据的操作 
``` bash
import pandas as pd
csv_data = pd.read_csv(path1, sep='\t')
#csv_data的基础属性包括values, index, columns和dtypes
csv_data.
Values: 获取元素
Index: 获取属性
column:获取列名
Types: 获取类型
Ndim： 获取维度
shape: 数据形状

#对数据的访问
#取出某列
Age = csv_data['Age']

#取出某行
Out = csv_data[:][I:i+1]

#取出多行多列
Out = csv_data['age','year'][:3]

#更方便的是利用iloc/loc函数进行数据读取,
# iloc第二维度中填列索引，loc中填列名称
DataFrame.loc[行索引条件/名称，列索引名称]
DataFrame.iloc[行索引位置，列索引位置]
#例子
Out = csv_data.iloc[:10, 2] #前10行，2列数据
Out2 = csv_data.loc[:10, 'age'] #钱10行，age列数据
#根据条件过滤
Out = csv_data.loc[csv_data['age']>10, 'age']

#访问任意数据
Out = csv_data.loc[:10, ['age','year']]

#取出制定行制定列数据
Out = csv.loc[2, ['age']]
Out = csv.iloc[2, [0]]

#删除列数据
Out = csv_data.drop(['age'], axis=1)
#删除全部为nan的行
Out = csv_data.dropna(axis=0, how='all') #how='any' 删除某一元素为nan的行


#这里
out = csv.iloc[2, [1]]索引出来的是序列化的数据，而
csv.iloc[2,1]直接是对应的位置的值，等于out[0]

#DataFrame对某列进行操作
Data1 = csv_data.age.apply(lambda x:int(x))

#匹配字典
#dict中村的事age:year, 映射过后age列和自动中对应age对应起来
csv_data['age'] = csv_data['year'].map(a_dict)

#一般读如的age是字符串，我们需要转换为数字
#首先处理‘uknown’数字（或者其他类型）
csv_data = csv_data.replace({'unknown':'0.0'})
#应用函数
csv_data1 = csv_data.age.apply(lambda x:int(x))
csv_data['age'] = csv_data1

#按照列进行排序
out. = csv_data.sort_values(by=['age'], ascending=False)
#也可以按照双列进行排序
Out = csv_dta.sort_values(by=['age','height'], ascending=[False, True])
#排序完还想调整索引号为升序，
csv_data.reset_index()
#恢复原来的排序
Out = out.sord_index()
#如果不想删除原索引号，值增加一列新索引，则:
csv_data.reset_index()
```

 ###  Maya python的基本使用（obj可视化）
参考： https://blog.csdn.net/Exupery_/article/details/77164157
      https://zhuanlan.zhihu.com/p/28077015
动画效果： https://zhuanlan.zhihu.com/p/36727011

 ###  openGL 问题
1, error: OpenGL.error.NullFunctionError: Attempt to call an undefined function glutInit
``` bash
sudo apt-get install freeglut3
sudo apt-get install freeglut3-dev
Sudo apt-get install python-poengl # 不确定是否有用
``` 

2 error: freeglut (foo): failed to open display 
``` bash
xvfb-run -a python script.py
```

3, error: 0:1(10): error: GLSL 3.30 is not supported

``` bash
export MESA_GL_VERSION_OVERRIDE=3.3 #
```

 ###  Pytorch使用albumentations实现数据增强
参照： https://blog.csdn.net/zhangyuexiang123/article/details/107705311

 ###  SPConv 1.0版本安装
参照： https://github.com/traveller59/spconv/issues/58


 ###  python 展示 obj文件
```bash
import trimesh
mesh = trimmest.load('my_obj.pbj')
mesh.show()

#=====================
“鼠标单击拖动”旋转视图
*`ctl鼠标单击拖动`平移视图
*鼠标滚轮放大
*`z`返回基本视图
*`w'切换线框模式
*`c`切换背面剔除
*`g`切换XY网格，将Z设置为最低点
*`a`可以在三种状态之间切换XYZ-RGB轴标记：关，在世界帧或每帧
*`f`在全屏和窗口模式之间切换
*`m`最大化窗口
*`q`关闭窗口wf

```

###pytorch根据名称访问网络子模块

``` bash
for layer_name, layer in net._modules.item():
     setattr(net, layer_name, layer.eval())
     Out = getattr(net, layer_name)
```

###wandb command not found
```bash
python -m wandb login
```

###torchrun 报错：
decord导入要在torch之后

```bash
import torch
import decord

```

###  1 入门简单例子  
``` bash
#！ /bin/sh

Cd ~

Mkdir shell_tut

Cd sheel_tut

For ((I=0;i<10;i++)): do
	touch test_$I.txt
done
```
上述例子中，第一行制定脚本解释器，创建循环体， 循环10次，创建10个文件， 循环体结束，其中for do done都是sh关键字。#开头为注释，无多行注释方法
要运行脚本两种方法，./test.sh 或者sh test.sh


### 2 变量定义  
在shell中变量定义直接声明赋值即可，例如
``` bash
name='yaxiwang' #注意等号不能有空格
name=‘yaxiongwang‘ #重定义变量
```
使用变量的时候：$+变量名
``` bash
Name='yaxionwang'
echo $Name
```
既可以输出name

### 3 字符串类型  
在shell中，字符串类型可以使用单引号和双引号，其中单引号会按原样输出，其中不可以包含变量，单引号中不能出现单引号，使用转义字符也不行
而双引号中可以有变量，也可使用转义字符，如：
``` bash
name=='yaxiongwang'
str="Hello. I know you are \"#name\"! \n"
```
 1)拼接字符串
 ``` bash
   name=='yaxiongwang'
   greeting="hello, "$name" !"
   greeting="hello, ${name} !"
 ```
 2)获得字符串长度
 ``` bash
  String = 'abdc'
  Echo ${#string}
  #提取子串
  echo ${string:1:4}
  #查找字符串
  echo ‘expr index "$string" bd’ #输出2，找出单词bd在单词string中的位置
 ```

### 4流程控制  
主要是if语句与循环，在sheel中if语句不能为空，if else块中必须有内容可以执行，否则报错，如果没有内容可以不写。
If语句基本语法：
``` bash
if condition
then 
	command 1
        Command 2
else
	command 1
	command 2
	command 3
fi

#另一种形式是
for var in item1, item2, item3....itemn
do
	cmd 1
	cmd 2
done

#也可以是c风格的for
for ((exp1; exp2; exp3))
do
	cmds
done
```

while循环的使用方法
``` bash
while condition
do
	cmds
done

#无限循环可以写作
while true或者while ：
```

### 5 基本逻辑运算符  
``` bash
-ne 不等于
-eq 等于
-le 小于等于
-l 小于
-ge 大于等于
-g 大于
== 字符串比较
```
逻辑联结符：
&&, ||, <和>必须写在双中括号中[[a>b && c<d]],如果写在一个单中括号中报错

### 6 $符与中括号[]使用  
``` bash
count =1
while [ $count -le 5]
do
	cmd 1
	count=$[ $count+1 ]
done
```
中括号中是可执行的程序（运算），count=$[ $count+1 ]第一个$调用变量，而第二个$获得可执行程序的结果，或者说的到命令输出
### 7 数组使用  
初始化数组：
``` bash
array=(a b c d)
array[0]#索引数组
```

### 8 括号的使用技巧  
字符串比较用双中括号[[ ]]；算数比较用单中括号[ ]——左右留空格
算数运算用双小括号(( )) ；shell命令及输出用小括号( )——左右不留空格
快速替换用花括号{ }——左右留空格
反单引号起着命令替换的作用` `

### 9 scp（其他命令）错误：argument list too long  
``` bash
find dir '*' -exec scp -r {} yaxiwang@cluster.eng.uts.edu.au:~/Data/ \;
```
注意后面的分号一定要有

###  利用shell拷贝文件到远程  
主程序
``` bash 
 word_vec_pth="/Volumes/Untitled/TBIRDataSet/NUS-WIDE/word_vec_stanford/"
  tags_pth="./tags_filtered.txt"
 
  cat $tags_pth | while read line #一行一行读取文件
  do
      tmp_line=${word_vec_pth}${line}.txt
      if [ ! -f $tmp_line ];then #判断文件是否存在
             echo 1
     else
	     #传入参数和后面的对应
             ./scp_expect.sh "$tmp_line" "yaxiwang@cluster.eng.uts.edu.au:~/Data/word_vec_s    tanford/"
 1            echo successfully transfer
      fi
 done
```
scp_expect.sh主要是为了避免每次输入密码
``` bash

 #!/usr/bin/expect
 set content [lindex $argv 0] #将传入的第一个参数命名为content
 set dest [lindex $argv 1] 
 spawn scp $content $dest
 expect "*passward:"
 send "wang\$764113779\r"
 expect eof
```
注意要将scp_except.sh授权为可执行文件chmod +x scp_except.sh

### expect命令 
参考博客：https://blog.csdn.net/jxdl6655/article/details/78626749
关键字：
``` bash
［#!/usr/bin/expect］ 这一行告诉操作系统脚本里的代码使用那一个shell来执行。这里的expect其实和linux下的bash、windows下的cmd是一类东西。 注意：这一行需要在脚本的第一行。

spawn              调用要执行的命令,相当于开启一个线程
expect               只有spawn执行的命令的执行结果才被expect捕捉到，主要包括标准输入的提示信息、eof和timeout。等待命令提示信息的出现，也就是捕捉用户输入的提示：
send                 发送需要交互的值，替代了用户手动输入内容
set                    设置变量值
interact             执行完成后保持交互状态，把控制权交给控制台，这个时候就可以手工操作了。如果没有这一句登录完成后会退出，而不是留在远程终端上。
expect eof        这个一定要加，与spawn对应表示捕获终端输出信息终止，类似于if....endif
```
expect脚本必须以interact或expect eof结束，执行自动化任务通常expect eof就够了

### 内网穿透配置-cpolar
前置条件：机器可以访问网络，假设内网服务器为ubuntu系统
参考地址：https://www.zhihu.com/question/359350613/answer/2561223350
``` bash
1, 注册cpolar账号： https://www.cpolar.com/

2, ubuntu上安装cpolar客户端
（国内安装）curl -L https://www.cpolar.com/static/downloads/install-release-cpolar.sh | sudo bash
（国外安装）curl -sL https://git.io/cpolar | sudo bash

3，查看版本信息确认安装成功
cpolar version

4，进行token认证
前往你的cplar主页，在主页右侧有验证一栏，点击即可看到token，复制然后
cpolar authtoken xxxxxxxxxxxxxxxxxx

5，配置cpolar开机自启动
sudo systemctl enable cpolar

6，启动服务
sudo systemctl start cpolar

7，查看cpolar守护进程状态
sudo systemctl status cpolar

第二行显示 Active: active (running)说明服务器启动成功

8，获取ssh公网地址
在内网机器上打开：http://localhost:9200/  ，通过cploar账号登陆，左侧栏-》状态-〉在线隧道列表
Ssh那一栏的第二列，即为tcp://公网地址：端口
在外网机器上输入：ssh 内网机器用户名称@公网地址 -p 端口 输入密码即可登录
注意：ssh公网地址是tcp：//之后的地址，不需要携带tcp://

```
