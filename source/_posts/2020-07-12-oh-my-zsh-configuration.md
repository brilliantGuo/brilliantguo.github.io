---
title: iTerm2 + Oh My Zsh 配置教程
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
date: 2020-07-12 20:26:22
subtitle:
tags:
categories:
---

本篇文章在 [iTerm2 + Oh My Zsh 打造舒适终端体验](https://github.com/sirius1024/iterm2-with-oh-my-zsh/blob/master/README.md) 上面加以补充，记录自己 iTerm2 的配置过程。

iTerm2 安装过程可以参考上面这篇教程 ，安装完就可以使用 Agnoster 的炫酷主题了。这个是我按照教程配置后的效果：

![配置后的样例](https://cdn.jsdelivr.net/gh/brilliantGuo/picgo/imgtheme-host-before.png)

但是总感觉还有些不太美观，就在上面的配置进行了些改动，于是就有了这篇文章。

## 修改主题配置

### 隐藏用户名和主机名

Agnoster 主题下，iTerm2 每行开头都会有 `[user]@[hostname]` 的提示，比较占位子。很多时候我们是在本地操作的，这点信息可以忽略，因此我们动手修改一下，把这段代码删掉。

在编辑主题的时候，我们最好拷贝一份主题，在新的主题上做修改。一方面避免 iTerm2 更新把之前修改过的主题覆盖掉，另一方面在配置错误时能参考原来的配置进行回滚：

```sh
cd ~/.oh-my-zsh/themes
# 拷贝 agnoster 主题为 my-agnoster 新主题
cp agnoster.zsh-theme my-agnoster.zsh-theme
```

编辑 `my-agnoster.zsh-theme` 文件，主题里面的 `build_prompt` 便是 zsh 每行开始的输出了，其中的 `prompt_context` 就是表示输出 `[user]@[hostname]` 字段。把这个字段注释掉就行：

主题里面的 `build_prompt` 便是终端的输出了，其中的 `prompt_context` 就是表示输出 `[user]@[hostname]` 字段。把这个字段注释掉就行：

```sh
build_prompt() {
  RETVAL=$?
  prompt_status
  prompt_virtualenv
  prompt_aws
  # prompt_context
  prompt_dir
  prompt_git
  prompt_bzr
  prompt_hg
  prompt_end
}
```

然后编辑 `.zshrc` 文件，把 `ZSH_THEME` 字段改成我们自己的主题：

```sh
ZSH_THEME="my-agnoster"
```

保存并退出。运行 `source ~/.zshrc` 命令更新下配置，就能生效了：

![iTerm2 修改后的默认主题](https://cdn.jsdelivr.net/gh/brilliantGuo/picgo/imgtheme.png)

### iTerm2 使用独立主题

Agnoster 主题虽然好看，但是兼容性并不是很好。比如在 Android Studio 等 IDEA 系列 IDE 上会变形，不太好看：

![其他编辑器不兼容时的问题](https://cdn.jsdelivr.net/gh/brilliantGuo/picgo/imgandroid-studio-bug.png)

虽然换个主题能解决这个问题，但是 Agnoster 好看呀，有没有办法既能在 iTerm 下使用 Agnoster 主题，在 Android Studio 中又不会变形呢？

其实在 iTerm2 中有个名为 `TERM_PROGRAM` 的变量，值为 `iTerm.app`。这个变量只在 iTerm2 里打开的终端存在，其他地方打开的终端不存在这个变量。利用这个我们可以做点手脚。编辑 `.zshrc` 文件：

```sh
# 用下面的代码替换之前的 ZSH_THEME="my-agnoster"
if [ "$TERM_PROGRAM" = "iTerm.app" ]; then
    ZSH_THEME="my-agnoster"
else
    ZSH_THEME="ys"
fi
```

这段代码表明在 iTerm 中使用我们修改过的 my-agnoster 主题，在其他 app 打开的终端中使用 ys 的主题。

保存后重启下 Android Studio 的终端，可以看到有变化了：

![自动切换主题后，Android Studio 的终端展示正常了](https://cdn.jsdelivr.net/gh/brilliantGuo/picgo/imgandroid-studio-ys.png)

## 强大的 alias 配置

zsh 其中一个强大的功能，就是可以通过 alias 字段自定义命令。

如果你安装了 VSCode 或者 Sublime Text，则可以通过设置 alias 在 iTerm2 中使用这两个软件可视化编辑文件：

```sh
# vscode
alias code="/Applications/Visual\ Studio\ Code.app/Contents/Resources/app/bin/code"

# sublime text
alias subl="/Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl"

```

保存并退出，运行 `source ~/.zshrc` 命令更新下配置。后面就可以通过 `subl ~/.zshrc` 在 Sublime Text 中编辑配置文件，不用 `vi` 在终端中编辑了。
