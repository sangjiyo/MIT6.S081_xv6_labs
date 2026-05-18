# 这是一个开始
>参考资料：  
>该课程课表链接为https://pdos.csail.mit.edu/6.828/2020/schedule.html  
>中文翻译参考https://xv6.dgs.zone/  
>实验总结参考https://juejin.cn/post/7499667840495222799?searchId=2026051421531478C1A0C27758A28BD15F  
>课程中文翻译https://www.zhihu.com/column/c_1294282919087964160  
>课程中文字幕视频链接https://www.bilibili.com/video/BV1rS4y1n7y1/  
这是第一次的学习和实验过程，环境搭建参考课程第一个实验，这里记录我遇到的一些问题和解决办法  
安装实验文档获取实验源码并切换到util分支之后，使用`make qemu`命令构建并运行xv6时，出现了以下报错信息  
```
root@sang-virtual-machine:/learn/MIT_6S081/xv6-labs-2020# make qemu
***
*** Error: Couldn't find a riscv64 version of GCC/binutils.
*** To turn off this error, run 'gmake TOOLPREFIX= ...'.
***
gcc    -c -o kernel/entry.o kernel/entry.S
kernel/entry.S: Assembler messages:
kernel/entry.S:11: Error: no such instruction: 'la sp,stack0'
kernel/entry.S:12: Error: no such instruction: 'li a0,1024*4'
kernel/entry.S:13: Error: no such instruction: 'csrr a1,mhartid'
kernel/entry.S:14: Error: no such instruction: 'addi a1,a1,1'
kernel/entry.S:15: Error: too many memory references for 'mul'
kernel/entry.S:16: Error: too many memory references for 'add'
kernel/entry.S:20: Error: no such instruction: 'j spin'
make: *** [<builtin>: kernel/entry.o] Error 1
root@sang-virtual-machine:/learn/MIT_6S081/xv6-labs-2020#
```
这是因为系统缺少针对针对RISC-V架构的工具链，运行如下命令进行安装  
```
        sudo apt update
        sudo apt install -y git build-essential gdb-multiarch qemu-system-misc
        sudo apt install -y gcc-riscv64-linux-gnu binutils-riscv64-linux-gnu
```
而后`make qemu`命令就能正常运行了，在github创建此仓库后，将本地分支上传到我的仓库中  
先配置我自己的仓库地址，在xv6-labs-2020目录下运行命令`cat .git/config`能看到当前配置信息  
```
        [core]
        	repositoryformatversion = 0
        	filemode = true
        	bare = false
        	logallrefupdates = true
        [remote "origin"]
        	url = git://g.csail.mit.edu/xv6-labs-2020
        	fetch = +refs/heads/*:refs/remotes/origin/*
        [branch "master"]
        	remote = origin
        	merge = refs/heads/master
        [branch "util"]
        	remote = origin
        	merge = refs/heads/util
        [remote "github"]
        	url = git@github.com:sangjiyo/MIT6.S081_xv6_labs.git
        	fetch = +refs/heads/*:refs/remotes/github/*
```
这里是我已经配置过github仓库地址，使用了`git remote add github https://github.com/sangjiyo/MIT6.S081_xv6_labs.git`命令  
但是使用`https`来将本地分支上传需要输入GitHub账户和密码，这种认证方式已经被弃用了，使用时会出现如下报错  
```
remote: Invalid username or token. Password authentication is not supported for Git operations.
fatal: Authentication failed for 'https://github.com/sangjiyo/MIT6.S081_xv6_labs.git/'
```
因此我选择使用ssh密钥认证  
运行`ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`该命令创建密钥   
运行`cat ~/.ssh/id_rsa.pub`查看并复制密钥内容，而后在GitHub用户中心设置里新建ssh密钥并将刚刚复制的内容粘贴进去  
修改远程连接github的链接为ssh链接  
`git remote set-url github git@github.com:sangjiyo/MIT6.S081_xv6_labs.git`  
到这里就完成了环境的配置，使用如下命令将本地分支上传到GitHub
```
git checkout util
git push github util:util
```
