# 前言

Fork于[张志豪的个人博客](https://izhangzhihao.github.io/)，感谢作者提供这么好的Jekyll模板

## Fork 指南

Fork 本项目之后，还需要做一些事情才能让你的页面「正确」跑起来。

1. 修改配置:

   网站的配置基本都集中在 \_config.yml 文件中，将其中与个人信息相关的部分替换成你自己的，比如网站的 title、subtitle、duoshuo 和 Disqus 的用户名等。

   **注意：** 因为 Disqus 处理用户名与域名白名单的策略存在缺陷，请一定将 disqus\_username 修改成你自己的。

2. 删除我的文章:
   `_posts`文件夹中除了 template.md 文件外，都可以全部删除，然后添加你自己的内容。

3. 删除[我的Google Analytics ID](https://github.com/izhangzhihao/izhangzhihao.github.io/blob/master/_includes/footer.html#L44-L53),你不想让我统计你博客的访问量对吧?

4. 删除[我的各种个人信息](https://github.com/izhangzhihao/izhangzhihao.github.io/blob/master/_config.yml) 还有[这里](https://github.com/izhangzhihao/izhangzhihao.github.io/blob/master/pages/about.md) 别的地方还有,要改成你自己的。

5. 正确设置项目名称与分支。

   按照 GitHub Pages 的规定，名称为 `username.github.io` 的项目的 master 分支，或者其它名称的项目的 gh-pages 分支可以自动生成 GitHub Pages 页面。

6. 修改域名:

   如果你需要绑定自己的域名，那么修改 CNAME 文件的内容；如果不需要绑定自己的域名，那么删掉 CNAME 文件。


7. 修改「关于」页面:

   pages/about.md 文件内容对应网站的「关于」页面，里面的内容多为个人相关，将它们替换成你自己的信息。
