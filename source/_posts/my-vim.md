---
title: My vim
date: 2016-07-23 16:55:09
tags: vim
---

---
## 常用命令
插入命令，执行后会进入插入状态
```
i 在光标处插入
a 在光标后插入
o 在新的下一行插入
O 在当前行前插入一个新行
cw 替换从光标所在位置后到一个单词结尾的字符，之后进入插入
```
撤销与恢复
```
u 撤销
<C+r> 恢复撤销
```
分屏操作
```
:vsplit xxx 要打开的文件名
:vnew 在新窗口创建文件
:only 取消分屏
<C+w> h 跳转到左边的窗口 （若为大写H，则是把当前窗口调至左方）
<C+w> l 跳转到右边的窗口 （类似于上）
<C+w> w 在窗口之间跳转
<C+w> + 扩大窗口 (或在当前窗口位置，输入命令 :res +9)
<C+w> - 缩小窗口 (同理 :res -num)
```
光标移动
```
gg 光标移动到文件头
G 将光表移动到文件尾
0 数字零，到行头
^ 到本行第一个不是blank字符的位置（所谓blank字符就是空格，tab，换行，回车等）
$ 到本行行尾
w/W 跳转到下一个单词首部
e/E 跳转到下一个单词尾部
b/B 跳转到上一个单词首部
```
使用鼠标
```
:set mouse=
n 普通模式
v 可视模式
i 插入模式
c 命令行模式
h 在帮助文件里，以上所有的模式
a 以上所有的模式
r 跳过 |hit-enter| 提示
(建议直接使用:set mouse=a)
```
用鼠标和可视模式复制/粘贴 ('mouse' 选项必须设置，见上)
```
1. 在文本的第一个字符上按鼠标左键，移动鼠标到文本的最后一个字母，然后释放左
   键。这会启动可视模式并高亮选择区域。
2. 按 "y" 抽出可视文本到无名寄存器里。
3. 在要插入的位置上按鼠标左键。
4. 按鼠标中键。
A 在可视模式下自动选择 
```
查找与替换
```
:set ic 查找时忽略大小写 (noic)
:set hls 设置高亮搜索 (nohls)
?[/] text 向后[前]查找text对象
n[N] 查找下[上]一个
:%s/yyy/xxx/g  将文件中的yyy替换为xxx
/\<aaa\> 精确查找单词aaa
r<Input> 用Input替代当前光标所在位置的字符
gu[U] 替换成小[大]写
```
格式化
```
:set cindent 按C方式缩进
:set shiftwidth=n n是缩进宽度
gg=G 重新缩进整个文件
N== 从当前行开始缩进, N为缩进行数。
例如:set cindent shiftwidth=4  以C方式缩进4个单位
```
复制粘贴
```
[n]yy 将 n 行或当前行的内容放入临时缓冲区 
p[P] 将临时缓冲区中的文本放入光标后[前]
dd 将当前行剪切到缓冲区
dw 将光标所在词剪切到缓冲区
J 删除本行的回车符，把下一行并入本行末尾
"+y 把选定的内容放到寄存器"+中，这对应的是系统剪切版 
"+p 把起存器"+中的内容paste到当前的VIM中
```
其他
```
:set ts=4 expandtab 将tab用4个空格替换
:set nu 左侧显示文本的行号
:set list 显示换行符和tab键
<C-p>或是<C-n> 自动补全
. (小数点) 可以重复上一次的命令
N<command> 重复某个命令N次
:e[!] 打开其他文件[放弃修改，强制载入]
:set noexpandtab 取消用空格替换tab
```
```
对于("xxx")操作时:
vi" 选择 xxx
va" 选择 "xxx"
vi) 选择 "xxx"
va) 选择("xxx")
```
```
qa 用a寄存器记录操作，按q停止记录，@@重复最新记录,@a重复a寄存器操作 
ga 显示光标下字符的ascii值， 分别以十进制、十六进制和八进制显示
<C-v> + 字符 输入特殊字符
<C-a> 将当前数字加1
:let i=0 设置变量i值为0，例如:s/0/\=i 将光标所在行中第一个0用i变量的值来替换
:syntax on 语法高亮
:set nobackup 不生成~的备份文件
:%!xxd 显示16进制编码，加 -r 编辑结束后还原文件格式 
shift+d 删除光标到本行结的内容
d+(shift+$) 删除光标到结束的内容
d+(shift+^) 删除光标到行开始的内容
n, m !sort 将n到m行排序(e.g. 10,15 !sort)
m + a-z mark 位置
' ＋a-z 光标移动到对应的标记
marks 显示所有标记
delmarks 删除所有标记
```

```
vimdiff:
c,[c] 跳到下一个[上一个]差异位置
```
```
VsVim:
关闭控件 Ctrl + Shift + F12
修改热键：工具--> 选项 --> VsVim
```

Windows下，如果不想要windows下的ctrl-v粘贴这样的设置，可以将_vimrc中的这一行注释掉：
```
source $VIMRUNTIME/mswin.vim
```

## vim配置文件
### 位置
```
ubuntu: /etc/vim/vimrc
win: 安装目录/_vimrc
mac: /usr/share/vim/vimrc 
linux/Unix: ~/.vimrc
```

### 我的配置
```shell
syntax on    
set nu    
set mouse=a    
set ic    
set cindent    
set shiftwidth=2    
    
let filename=expand('%:t') "set variable    
if filename != 'makefile'     
    set ts=2 expandtab    
endif    
set nobackup    
    
"noremap <CR>  o^[  
"Enter replace cmd(o) + Esc (^[ represent Esc)  
"imap { {}^[OD  
"imap ( ()^[OD  
"imap [ []^[OD  
"imap <C-P> <Esc>  
```
MacOS 下加上这两句，用来复制选中文字进入系统剪切板
```
vmap "+y :w !pbcopy<CR><CR> 
nmap "+p :r !pbpaste<CR><CR>
```
