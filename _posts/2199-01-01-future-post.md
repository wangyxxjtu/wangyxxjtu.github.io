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
