
# Leon Ling's Nth Blog

搭建博客的意义在于提供一个自由表达自己的平台, 一方面是想说啥就能说啥, 读书笔记, 记录学习, 分享生活; 另一方面也是想利用博客框架中的标签和站内搜索功能快速管理自己的内容.

关注到我朋友们欢迎联系我, 可以加一波 `friend link` 相互引用哦~

本来一开始搭建这个博客就是想着直接fork [BY Blog](https://github.com/qiubaiying/qiubaiying.github.io), 然后只修改内容就完事儿了. 后来发现总有一些细节不是特别满意, 所以又找到主题的原作者 Hux 提供的纯净版 [huxblog-boilerplate](https://github.com/huxpro/huxblog-boilerplate) 并参考 [BY Blog](https://github.com/qiubaiying/qiubaiying.github.io) 重新加了一些新特性, [详见这里](https://liyanglingintel.github.io/2019/11/26/Blog-%E9%87%8D%E5%90%AF%E5%A3%B0%E6%98%8E/). 喜欢这类主题的同学们也欢迎`star`/`fork` 我的repo或者直接参考 [Reference](##Reference) 里原作者的Repo.

## How to start

### Method 1

不想费心定制的同学, 可以直接fork这个或者[Reference](##Reference) 里原作者的Repo, 然后把 `_config.yml` 中的配置信息改成自己的, 后面就可以直接在 `_post` 目录下添加自己的帖子啦.

### Method 2

1. 直接参考 [jekyll官方教程](https://jekyllrb.com/docs/), 完成所有配置.

2. 简单无脑的自定义方式

    * 还是先参考 [Ruby安装教程](https://jekyllrb.com/docs/installation/)
    * 国内的用户可以参考 [Ruby中国](https://gems.ruby-china.com/) 进行换源, 不然还是太卡了
    * 安装 `jekyll` 时如果总发现一些依赖关系处理不好,可以尝试 `gem install github-pages`, 一键安装 `gitpage` 所需所有库, 就是会有些臃肿.
    * 然后切换到当前博客目录, 运行

        * `npm run start` 或者 `bundle exec jekyll serve` 启动博客服务
        * `npm run dev` 或者 `bundle exec jekyll serve --incremental` 进入增量debug模式, 然后自己对博客框架进行自定义修改.

## Reference
博客的搭建教程 [Hux](https://github.com/Huxpro/huxpro.github.io) 
 
更为详细的教程戳这 [《利用 GitHub Pages 快速搭建个人博客》](http://www.jianshu.com/p/e68fba58f75c) 或 [Wiki](https://github.com/qiubaiying/qiubaiying.github.io/wiki/%E5%8D%9A%E5%AE%A2%E6%90%AD%E5%BB%BA%E8%AF%A6%E7%BB%86%E6%95%99%E7%A8%8B)

## License

遵循 MIT 许可证。有关详细,请参阅 [LICENSE](https://github.com/qiubaiying/qiubaiying.github.io/blob/master/LICENSE)。

