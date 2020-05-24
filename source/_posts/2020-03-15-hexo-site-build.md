---
title: Github + Hexo 搭建个人博客教程
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
date: 2020-03-15 21:15:44
subtitle:
tags:
- blog
categories:
- hexo
---

## 在 GitHub 上新建自己的博客仓库

打开 [Github](https://github.com/)，在 Repositories 下点击 New 按钮创建自己的博客仓库。这里的仓库名需要填写成 `<你的 GitHub 用户名>.github.io` ，方便后面自己的站点能通过 GitHub 直接访问，而无须部署到自己的服务器。

![Github 创建仓库页面](https://raw.githubusercontent.com/brilliantGuo/picgo/master/img20200511201552.png)

这里我已经创建了自己的仓库了，所以会提示报错。

## 安装 Hexo 博客系统

Hexo 是发布在 npm 上的博客系统，所以我们需要先安装 nodejs。非前端用户可以直接在 [Nodejs 官网](https://nodejs.org/zh-cn/download/) 进行下载，前端用户建议使用 [nvm](https://github.com/nvm-sh/nvm)（ Windows 则使用 [nvm-windows](https://github.com/coreybutler/nvm-windows) ），方便管理 Nodejs 版本。

安装完后输入以下命令全局安装 Hexo ：

```bash
npm install hexo-cli -g
```

安装完成后，可以通过 `hexo init <folder>` 命令创建自己的博客了。但是我们先不用急着创建，因为 Hexo 本身的主题比较简陋，且 Hexo 每个主题本身就是一个博客网站，所以我们可以先访问 [Hexo 主题](https://hexo.io/themes/) 页面，选择自己一个喜欢的主题，找到这个主题的仓库，然后运行 `git clone <博客 GitHub 地址>` 命令，把这个仓库保存到本地。以 [Hexo Next](https://github.com/next-theme/hexo-theme-next) 主题为例，在这里找到这个仓库的 git 地址：

![Next 主题的仓库地址](https://raw.githubusercontent.com/brilliantGuo/picgo/master/img20200511215550.png)

把仓库 clone 到本地后，进入到文件夹，在终端下依次运行下面命令，将这个仓库指向我们刚才申请的 GitHub 博客地址：

```bash
# 删除原来项目的 git 信息
rm -rf .git

# 新建自己的 git 仓库
git init

# 添加所有文件记录，并保存到 git 本地仓库
git add .
git commit -m '<修改记录>'

# 改成自己的仓库地址并上传代码
git remote add origin <你的 GitHub 博客仓库地址>
git push -u origin master
```

由于 Hexo 的主题都是依靠制作者自己上传，主题的页面也是作者自己开发，所以有些博客的 GitHub 地址没有标注出来，需要自己搜索。

我使用的主题是 [Super Snail](https://www.dusign.net/)，比较简洁美观且文档比较齐，按照首页置顶的文章配置下即可使用。

## 常用命令

全部指令可以参考 [Hexo - 指令](https://hexo.io/zh-cn/docs/commands) 一栏。

### 新建文章

```bash
hexo new [layout] <title>
```

layout 可以指定文章模板。如果没有设置 layout 的话，默认使用 `_config.yml` 中的 `default_layout` 参数代替。

### 启动本地服务器

```bash
hexo server
```

写文章时可以在本地启动一个服务器，预览博客的效果。默认链接是：`http://localhost:4000/`。

### 生成静态文件

```bash
hexo generate # 或者简写为 `hexo g`
```

若在生成静态文件后想要立即部署，可以带上 `-d` 参数。

### 部署网站

```bash
hexo deploy # 或者简写为 `hexo d`
```

若在部署前想要先生成静态文件，可以带上 `-g` 参数。

可以看到，`hexo generate -d` 和 `hexo deploy -g` 效果是一样的，看个人喜好使用。

## 利用 Travis CI 做自动化部署

如果参照其它网站的 Travis 教程，可能会遇到推送到 GitHub 时，GitHub Pages > Source 只能为 master 分支，不能修改为 gh-pages 的问题。这个问题的原因是 GitHub 现在对于个人博客（类似 `xxx.github.io` 这样的域名）只允许在 master 分支构建，其他分支构建 GitHub 不会再做识别。因此我们需要修改 travis 的配置文件，将源代码迁移到其他分支上（如 `dev`），将 master 作为构建分支即可。

原先的 travis.yml 配置文件如下：

```yml
sudo: false
language: node_js
node_js:
  - 10 # use nodejs v10 LTS
cache: npm
branches:
  only:
    - master # build master branch only
script:
  - hexo generate # generate static files
deploy:
  provider: pages
  skip-cleanup: true
  github-token: $GH_TOKEN
  keep-history: true
  on:
    branch: master
  local-dir: public
```

修改 `branches` 和 `deploy` 部分代码，改动如下：

```yml
branches:
  only:
    - dev # 这里我们把 dev 作为开发分支，因此监听该分支构建即可

deploy:
  target_branch: master # 构建后的文件分支，这里改成 master
  on:
    branch: dev
```

问题参考链接：

- [Unable to change source branch in GitHub Pages](https://stackoverflow.com/questions/39978856/unable-to-change-source-branch-in-github-pages)
- [利用Travis CI和Hugo將Blog自動部署到Github Pages](https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/)

## 接入 Gitalk 评论系统

待说明

## 相关主题博客，供参考

- [运维之美](https://www.hi-linux.com/)
- [Bean Tech](http://beantech.org/)
- [Super Snail](https://www.dusign.net/)
