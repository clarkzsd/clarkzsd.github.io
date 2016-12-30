---
title: 调教 PowerShell 
date: 2016-12-12 22:22:43
tags: windows
categories: Tech
description: Windows 一直以来都被很多非微软系程序员诟病，莫名奇妙的 bug 太多啊，经常蓝屏啊（这个是所有人都吐槽的），terminal 太弱太丑啊……的确，Windows 不是完美的系统，但也不至于说它彻底不适合开发。环境条件不行（待议），但人行啊，事在人为对不对。

---

## PowerShell

Windows 一直以来都被很多非微软系程序员诟病，莫名奇妙的 bug 太多啊，经常蓝屏啊（这个是所有人都吐槽的），terminal 太弱太丑啊……的确，Windows 不是完美的系统，但也不至于说它彻底不适合开发。环境条件不行（待议），但人行啊，事在人为对不对。

对于程序员来说，terminal 是一个很重要的工具，其颜值和易用性很大一部分决定了开发效率的高低，在 Windows 上有两个 terminal ，很多人只知道黑不溜秋的cmd，却不知道高大上的Powershell 。

> **Windows PowerShell**是[微软公司](https://zh.wikipedia.org/wiki/%E5%BE%AE%E8%BD%AF%E5%85%AC%E5%8F%B8)为[Windows](https://zh.wikipedia.org/wiki/Windows)环境所开发的[壳程序](https://zh.wikipedia.org/wiki/%E6%AE%BC%E7%A8%8B%E5%BC%8F)（[shell](https://zh.wikipedia.org/wiki/Shell)）及[脚本语言](https://zh.wikipedia.org/wiki/%E8%85%B3%E6%9C%AC%E8%AA%9E%E8%A8%80)技术，采用的是[命令行界面](https://zh.wikipedia.org/wiki/%E5%91%BD%E4%BB%A4%E8%A1%8C%E7%95%8C%E9%9D%A2)。这项全新的技术提供了丰富的控制与[自动化](https://zh.wikipedia.org/wiki/%E8%87%AA%E5%8B%95%E5%8C%96)的系统管理能力。
>
> [UNIX](https://zh.wikipedia.org/wiki/UNIX)系统一直有着功能强大的壳程序（[shell](https://zh.wikipedia.org/wiki/Shell)），Windows PowerShell的诞生就是要提供功能相当于UNIX系统的命令行壳程序（例如：[sh](https://zh.wikipedia.org/wiki/Bourne_shell)、[bash](https://zh.wikipedia.org/wiki/Bash)或[csh](https://zh.wikipedia.org/wiki/C_shell)），同时也内置脚本语言以及辅助脚本程序的工具。

​																	——From [维基百科](https://zh.wikipedia.org/wiki/Windows_PowerShell)

![Windows PowerShell 1.0 PD.png](https://upload.wikimedia.org/wikipedia/commons/d/d5/Windows_PowerShell_1.0_PD.png)

powershell的默认界面是这个样子的，还是丑，对不对？丑没关系，我们可以调教啊

## Step1 自定义界面

其实powershell 是支持自定义界面的

```
右键工具栏->属性
```

![powerrshell settings](http://o6ljw8wcq.bkt.clouddn.com/newblog/pic/powershell/powershell.png)

这个厉害了，字体、布局、颜色都可以自定义。

下面给出我配置的颜色及字体大小仅供参考：

> 屏幕背景：RGB(0,0,0)
>
> 屏幕文字：RGB(0,255,0)
>
> 不透明度：70%
>
> 字体大小：16

关于字体，微软对控制台字体的元数据有严格限制。

> 这些字体必须满足以下条件，可在命令会话窗口中：
> 　该字体必须是等宽字体。
> 　该字体不能为斜体字体。
> 　该字体不能有A或C负空间。
> 　如果是 TrueType 字体，则它必须是 FF_MODERN。
> 　如果它不是 TrueType 字体，则它必须是 OEM_CHARSET。
> 对于亚洲字体的附加条件：
> 　如果不是 TrueType 字体，字体名必须是“Terminal”。
> 　如果它是亚洲的 TrueType 字体，它还必须使用亚洲语言的字符集。

所以很多自带的字体都用不了，往往特别设计的字体才能支持控制台模式。这里推荐[Belleve](https://www.zhihu.com/people/be5invis)大神做的[Inziu Iosevka](https://link.zhihu.com/?target=http%3A//be5invis.github.io/Iosevka/inziu.html)字体，（其中，Inziu Iosevka SC/TC/J 和 Inziu IosevkaCC SC/TC/J 可以用于Terminal）

我用的是 Inziu Iosevka SC，设置完字体，效果是大概是这样的：

![MyPowerShell](http://o6ljw8wcq.bkt.clouddn.com/teri.png)

变好看了许多，对不对！

## Step2 posh-git + power-theme

posh-git 能让你在 PowerShell 中优雅的使用 Git。

>Posh-git: A set of PowerShell scripts which provide Git/PowerShell integration.
>
>#### Prompt for Git repositories
>
>The prompt within Git repositories can show the current branch and the state of files (additions, modifications, deletions) within.

它能更改 Git 仓库中命令提示符的外观样式，让你直接能看到当前仓库的状态。

### Pre-requisites

检查 PowerShell 的执行策略是否允许执行未知脚本，以管理员身份打开PowerShell，输入：

`Set-ExecutionPolicy RemoteSigned`

### 安装 posh-git

```powershell
Install-Module posh-git -Scope CurrentUser
```

### power-theme

power-theme 是[章程](https://www.zhihu.com/people/chantisnake)写的 PowerShell 命令提示符样式主题， 用了之后进一步提升 PowerShell 的颜值。如果你懂 PowerShell 脚本的话，还可以自己写样式。

power-theme 的github 地址 ：https://github.com/chantisnake/power-theme

安装：

```shell
git clone https://github.com/chantisnake/power-theme "$($($env:PSModulePath -split ';')[0])\power-theme"
```

启动 theme，这里是个例子，我选的是 ys 主题：

```powershell
notepad $PROFILE
```

然后在这个文件中添加以下命令

```powershell
Import-Module power-theme

Enable-Theme ys   

# 这个可以更改路径样式，有 concise, full 和 folder 三个选项
# 其中 concise 代表省略路径
$Global:THEME.PathFormat = 'concise'
```

我配置完的 PowerShell 完是这个样子的：

![FinishedPS](http://o6ljw8wcq.bkt.clouddn.com/tada.png)

是不是Bigger than bigger ？

它还可以自己添加时间样式

![img](https://pic3.zhimg.com/6cedca4b2468fa82352539ae5a9e1e7a_b.png)

具体操作可以查看它的 github 仓库。

## Step3 oh-my-posh + ConEmu

其实还有比 power-theme 更加厉害的东西，那就是 oh-my-posh 。

如果你玩过 *nix 的shell，那么你肯定知道 oh-my-zsh。在 Windows平台，PowerShell 有自己的 [oh-my-posh](https://github.com/JanJoris/oh-my-posh)。只不过，这东西只能在 ConEmu 环境下才有用。（补：power-theme 有些主题也需要在 ConEmu 中才有效）

> ConEmu是一个带标签的Windows终端，提供多标签支持和丰富的自定义选项，是Windows下不可多得的Console.

ConEmu 的确很强大，但我还是偏爱原生的 PowerShell，所以就没有用 oh-my-posh。有兴趣的朋友可以自己去尝试一下。