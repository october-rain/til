`pnpm` 中

1. 软链接是链接到当前项目路径下的 `.pnpm/<packagename>@<version>/node_modules/<packagename>` 目录下。
2. 硬链接是指 `.pnpm/<packagename>@<version>/node_modules/<packagename>` 路径下的文件，有一个相同的内存地址，因此多个项目安装只需要一次。


[参考掘金](https://juejin.cn/post/7044807973868142622)

