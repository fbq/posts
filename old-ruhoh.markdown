---
title: ruhoh旧文
date: 2012-11-07
---


这是我的第一篇  [ruhoh][]  框架下的blog, ruhoh是一个Static Blog Generator, 和[Pelican][Pelican]与[Octopress][]类似.
静态blog有着诸多好处, 不过我在这里不准备论述这些 :)

我是从刚入大学的时候开始写blog的, 首先用的是一些空间和门户网站一类的东西作为blog, 只是写写心情什么的,很难写出有深度的东西, 后来坚持了不到两年左右的[vimwiki][], 也是类似于静态blog的方法, 不过当时hosting是个大问题, 虚拟主机和域名都很麻烦, 而且经常容易出问题, 所以就只把wiki的源码保留在和[GitHub][]上, 可能过些日子我就会wiki中的文章迁徙过来, 然后删掉那个repo把.

后来又热心折腾起blog来是因为[GitHub Pages][]的兴起, 也鼓捣了一阵, GitHub Pages确实很方便, 提供了hosting和域名的服务(虽然是个\*.github.com,不过就将就吧), 同时还有版本管理的功能, 依托[Git][]强大的版本管理功能, 你可以记录你的pages发展的点点滴滴, GitHub也从此时起成为**"程序员的facebook"**.

不过, 就我折腾下来的感觉来看, 我并不是很喜欢GitHub Pages, 主要一点是冗余的问题. 因为如果你想使用GitHub Pages的服务, 你需要在GitHub上建立一个repo, 然后将你的网站内容push到这个repo的master分支, 每次当你更新这个分支的时候, Github Pages就随之更新. 这种hosting方式带来一个问题, 那就是你实际上在**为输出做版本控制**, 而如果你的这些输出的源文件不是html(或者是GitHub Pages支持的模板), 那么你还需要对**源文件进行版本控制**, 这一点让我觉得很麻烦. 当然如果你并不在意GitHub Pages上的那些历史信息, 每次都是`git push origin +master`, 那么这并不会成为你的困扰, 只是我觉得白白增加了一个repo的维护工作(也有用工具去做这个维护工作的, 比如说Octopresss, 但是解决方案并不完美), 我希望只去跟踪我的源文件. 更重要的是, 我觉得好的blog系统应该能够保留blog中内容的属性和分类, 能够从同一份内容中生成不同的表现(就算目前不能, 也应该为今后留下可能性).

所以我更喜欢只需要管理源文件一个repo的静态blog解决方案, 直到我发现了ruhoh, 在这里就不详细介绍ruhoh的特点了, 留到今后详细说明, 只是简单说一下ruhoh的机制, 你需要在GitHub上建立你自己blog的ruhoh, 然后通过web hook关联到[http://post.ruhoh.com](http://post.ruhoh.com), 就能在每次push之后, 自动在[ruhoh.com](http://ruhoh.com)生成自己的页面, 当然, 你也可以不依赖于[ruhoh.com](http://ruhoh.com), 自己将compile之后的网站内容host在任意主机上, 包括[Github Pages].

至此, 我的只需要管理一份repo的静态blog方案找到了, 至于它与其他方案的优缺点和是否我这样的选择是正确的, 还要交给时间来考验.

[ruhoh]: http://ruhoh.com
[Pelican]: http://docs.getpelican.com
[Octopress]: http://octopress.org/
[GitHub]: http://www.github.com
[vimwiki]: http://code.google.com/p/vimwiki/
[GitHub Pages]: http://pages.github.com
[Git]: http://git-scm.com

