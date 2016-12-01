# hnxyzhw.github.io
## "Page Build Warning" email
These days, some of you must receive a "Page Build Warning" email from github after you commit happily. Don't Worried! It just that github changes its build environment.
In this mail, github told us:
`You are attempting to use the 'pygments' highlighter, which is currently unsupported on GitHub Pages. Your site will use 'rouge' for highlighting instead. To suppress this warning, change the 'highlighter' value to 'rouge' in your '_config.yml'.`
So, just edit _config.yml, find highlighter: pygments, change it to highlighter: rouge and the warning will be gone.

关于收到"Page Build Warning"的email
由于jekyll升级到3.0.x,对原来的pygments代码高亮不再支持，现只支持一种-rouge，所以你需要在 _config.yml文件中修改highlighter: rouge.另外还需要在_config.yml文件中加上gems: [jekyll-paginate].
同时,你需要更新你的本地jekyll环境.
使用jekyll server的同学需要这样：
	1.	gem update jekyll # 更新jekyll
	2.	gem update github-pages #更新依赖的包
使用bundle exec jekyll server的同学在更新jekyll后，需要输入bundle update来更新依赖的包.
参考文档：using jekyll with pages & Upgrading from 2.x to 3.x


