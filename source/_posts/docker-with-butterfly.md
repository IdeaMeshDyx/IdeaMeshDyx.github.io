---
title: docker-with-butterfly
date: 2024-04-10 12:16:29
tags:
categories:
---

# Hexo Blog

> 记录博客搭建管理的技术栈和流程
>
> 博客的技术方案选择： [Hexo+github.io][https://hexo.io/zh-cn/]
>
> 博客模板选择： [Butterfly][https://butterfly.js.org/]
>
> 博客文件管理模式： 
>
> * github分支管理： blog 分支是源文件，main分支是渲染后文件
> * docker环境管理：[独立使用 docker 编译环境][https://github.com/IdeaMeshDyx/dev-enviroment/tree/main/hexo]

首先是选择一个合适的博客管理技术栈，实际上借用已有的博客平台比如[掘金][],[开源中国][],[CSDN][] 等可以节省自己部署成本，但是这些平台太过于复杂且页面风格等方面难以符合自己的需求，最重要的是太过于复杂和笨重。

使用静态博客需要花费更多的精力，但是也可以更贴近开发方式，也可以作为部分技术点的试验方式，故选用这个需要折腾的方式。目前最为著名的两个技术栈是：

- Jekyll：42.6k [Github地址](https://link.zhihu.com/?target=https%3A//github.com/jekyll/jekyll)/[官网地址](https://link.zhihu.com/?target=https%3A//jekyllrb.com/)
- Hexo：32.6k [Github地址](https://link.zhihu.com/?target=https%3A//github.com/hexojs/hexo)/[官网地址](https://link.zhihu.com/?target=https%3A//hexo.io/)

其中 Jekyll 有github支持，可以直接在github网页上编辑，但是技术要求较高，后期等到博客管理维护的方法论和技术流程成熟了就切换成这个试试。Hexo基于node.js 实现，有非常多的教程和资料，许多操作都做了封装和优化，可以在本地进行调试，作为学习和测试的方式来说非常适合。其他博客技术对比资料参考[静态博客搭建工具汇总](https://www.cnblogs.com/FLY_DREAM/p/16527100.html),[Top ten popular static site generators (SSG) in 2023][https://medium.com/@ezinneanne/top-ten-popular-static-site-generators-ssg-in-2023-e1894fca6925],[Jekyll / Hugo / Hexo Comparison][https://lexcao.io/posts/jekyll-hugo-hexo/],[Gatsby, Ghost, Hugo, Jekyll or another static site generator?][https://www.reddit.com/r/webdev/comments/10qpeu9/gatsby_ghost_hugo_jekyll_or_another_static_site/]



##  Docker 管理环境

不论选用哪一类技术，当切换设备和环境的时候，以往部署的 ruby，nodejs 环境就需要重新部署，而这往往是成本最高的地方，容易受到操作系统、设备硬件、网络状态的影响，所以采用 Docker 镜像做统一的管理。 

Docker 管理的方式是 Docker 内配置博客需要的所有的环境依赖，包括nodejs、ruby、github sshkey等等，然后通过挂载本地博客项目文件的方式来获取源文件，这样就可以使用本地 IDE 编辑文件，然后利用 Docker 环境编译生成和部署。

Docker 环境部署参考：https://github.com/IdeaMeshDyx/dev-enviroment/tree/main/hexo



## Markdown文章内容

简单的模板, 配置在`/scafffolds/post.md` 中

``` markdown
---
title: {{ title }}
date: {{ date }}
author: 
img: 
coverImg: 
top: false
cover: false
toc: true
mathjax: false
password:
summary:
tags:
categories:
---

```

然后每次使用 `hexo new` 创建新的博客时候就会采用这个格式



## github + hexo 部署流程

当完成了博客的内容编写之后，需要编译静态文件同时发布的github上，遵循以下流程：

```
# 所有的修改都在 blog 分支上进行
git checkout blog

# 先保存已有的修改
git status
git add .  # 或者是 add 修改地文件
git commit -m ""  --signoff

# 然后生成文件
hexo c # 清理之前的 cache
hexo g
hexo s

# 此后可以直接访问 localhost:8000 查看博客的具体样式，依据样式修改
# 如果修改完成则完成部署
hexo d

# hexo d 之后静态文件就会被 push 到远端 github 的 master 或者 main （看自己的设置）分支上
# 但是为了能够保证自己的blog源文件保存，还需要清理这些静态文件之后，将源文件push到 blog分支
hexo c

# status 应该显示没有修改，是update
git status
git push origin/blog

```



