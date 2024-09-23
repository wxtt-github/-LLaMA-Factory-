# Fine-tuning-based-on-LLaMA-Factory
### 概述

基于LLaMA-Factory微调框架，对qwen2-7B和qwen2-72B进行微调，构建智能问答助手。

### 环境准备

> 由于微调大模型一般在有GPU资源的服务器上进行操作，因此操作系统选为ubuntu 20.04

#### 1.安装Anaconda

点击进入[Anaconda官网](https://www.anaconda.com/download/success)下载Linux系统下的[64-Bit (x86) Installer](https://repo.anaconda.com/archive/Anaconda3-2024.06-1-Linux-x86_64.sh)，然后通过xftp软件或者通过scp命令将安装包上传到服务器上。

```shell
scp命令格式如下(win+r打开命令行，然后cd到文件所在的目录下)
scp -P <端口号> <文件名> <服务器用户名>@<服务器IP地址>:<传输的目标路径>
例如：
scp -P 22888 Anaconda3-2024.06-1-Linux-x86_64.sh wxtt@100.121.4.56:/home/wxtt/
```

上传完成后，使用sh命令执行安装程序,然后按照指引安装即可。

```shell
sh Anaconda3-2024.06-1-Linux-x86_64.sh
```

安装完成后，需要导入环境变量，用vim命令打开bashrc

```shell
vim ~/.bashrc
```

输入i进入insert模式，然后光标移到文件末尾，将这句话`exportPATH="/home/wxtt/anaconda3/bin:$PATH"`添加到末尾（注意将用户名wxtt改成你自己的，如果你装的miniconda，将anaconda3改为miniconda3），然后执行source命令更新bashrc。

```shell
source ~/.bashrc
```

重启服务器，此时，你应该能注意到，终端中用户名前面多了一个`(base)`，输入下面命令查看conda版本，若能正常显示则成功安装完Anaconda

```
conda -V
```

#### 2.安装Python

输入下面命令查看Anaconda管理下当前已有的Python环境

```
conda info --envs
```

如果你是第一次用，应该只有base环境即本机环境

输入下面命令创建Python环境

```
conda create -n <环境名> python=<版本号>
比如下面创建了一个3.11版本的名为openai的Python环境
conda create -n openai python=3.11
```

激活环境

```
conda activate <环境名>
比如
conda activate openai
```

pip安装一些常用库

```
pip install pandas -i https://pypi.tuna.tsinghua.edu.cn/simple
pip install openpyxl -i https://pypi.tuna.tsinghua.edu.cn/simple
```

点击进入[LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory)下载代码压缩包，解压上传文件夹到服务器上，方法和之前上传Anaconda安装包的方式一样。上传完成后在服务器上cd到解压好的文件夹中。执行如下命令安装一些库。为了避免代码仓库变化导致安装库的问题，贴一个LLaMA-Factory压缩包的[百度云链接](https://pan.baidu.com/s/1l-TzlVgA7QHZ_rCOWXuMwQ?pwd=13ax)。

```
pip install -e ".[torch,metrics,deepspeed,liger-kernel,bitsandbytes,hqq,gptq,awq,aqlm,vllm,galore,badam,adam-mini,qwen,modelscope,quality]"
```

安装好后，在LLaMA-Factory文件夹下，执行下面命令（注意全程要激活你的Python环境），验证库是否成功安装。

```
查看当前Python环境已安装库
pip list
运行LLaMA-Factory命令测试
llamafactory-cli help
```

如果显示了llamafactory命令的帮助信息没有报错（警告不管），则说明所有库安装成功。

#### 3.部署qwen大模型
