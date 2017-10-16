## Welcome to GitHub Pages

## Frame
    ###404 页面，你可以自定义
    ├── 404.html
    ├── README.md
    ### 博客配置文件，基本上是最重要的一个文件之一了
    ├── _config.yml
    ### 博客页面模版目录
    ├── _layouts
    │   ├── default.html
    │   ├── home.html
    │   ├── page.html
    │   └── post.html
    ### 博客文章目录，下面可以按文件夹进行博文分类
    ### 注意，博文文件格式必须是：时间-博文标题.md，参考下面的格式
    ├── _posts
    │   ├── books
    │   │   └── 2016-04-29-books-recommend-and-summarize-on-apr-2016.md
    │   └── python
    │       └── 2016-04-01-spark-in-finance-and-investing.md
    ### 这个是你的站点地图了，用户可以访问这个文件夹下面的所有文件
    ### 比如说，用户可以直接访问我的Hokkaitao.github.io/404.html; Hokkaitao.github.io/images/navigation.jpg
    ### 比如说，当你访问 Hokkaitao.github.io/spark-in-finance-and-investing  
    ###        实际上是访问了 Hokkaitao.github.io/spark-in-finance-and-investing.html
    ### 你会发现这下面有很多在博客更目录下重复的文件夹，比如说 css，js，images等文件夹，不要纳闷，这是正常的
    ### 因为你的博客更目录下的文件，是 jekyll 用来渲染一个 html 文件的，html 文件及其所需要的任何文件，都会放到 _site 这个
    ### 专用的目录下面
    ├── _site
    │   ├── 404.html
    │   ├── README.md
    │   ├── atom.xml
    │   ├── books-recommend-and-summarize-on-apr-2016.html
    │   ├── css
    │   │   ...
    │   │   ...
    │   │   ...
    │   ├── images
    │   │   ├── 2.jpg
    │   │   ├── spark-in-finance-1.jpg
    │   │   ├── spark-in-finance-2.jpg
    │   │   └── spark-in-finance-3.jpg
    │   ├── index.html
    │   ├── js
    │   │   ...
    │   │   ...
    │   │   ...
    │   └── spark-in-finance-and-investing.html
    ├── atom.xml
    ├── css
    │   │   ...
    │   │   ...
    │   │   ...
    ├── images
    │   │   ...
    │   │   ...
    │   │   ...
    ├── index.html
    └── js
        │   │   ...
        │   ...
        │   ...

## Launch
jekyll server --watch
http://127.0.0.1:4000/

## Reference
https://github.com/litaotao/github-blog-template
