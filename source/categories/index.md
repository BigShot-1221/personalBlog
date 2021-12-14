#  Hexo+Github博客搭建之Matery主题个性化修改篇（二）

##  修改原有相册

> 参考教程：https://yafine-blog.cn/posts/3b98.html

##  matery主题自定义字体

> 参考教程：https://yafine-blog.cn/posts/49fb.html

## 添加天气小插件

首先去中国天气官网：https://cj.weather.com.cn/plugin/pc

配置自己的插件，选择自定义插件—>自定义样式——>生成代码，然后会生成一段代码，复制粘贴到 `themes/matery/layout/layout.ejs`即可。

## 关于我页面添加个人简历

打开`theme/matery/layout/about.ejs`文件，大约在13行。有一个`<div class="card">`标签，找出其对应结尾的标签，大约在61行左右，然后在新增如下代码：

```
<div class="card">
     <div class="card-content">
         <div class="card-content article-card-content">
             <div class="title center-align" data-aos="zoom-in-up">
                 <i class="fa fa-address-book"></i>&nbsp;&nbsp;<%- __('个人简历') %>
              </div>
                 <div id="articleContent" data-aos="fade-up">
                     <%- page.content %>
                 </div>
           </div>
      </div>
</div>
```

注意粘贴的位置和空格要正确，这里的位置随你自己设置，你也可以把简历作为第一个card，然后`/source/about/index.md`下面写上你的简历了（就像写博客一样）。

## 豆瓣书单电影页面

> 注意：首先需要检查你的hexo版本是多少，在你的博客目录下执行命令 hexo -v
> 即可，这个豆瓣插件需要的版本需要<4.2.0，否则会出现bug，比如点击书单的在读，想读或者已读会出现一个新的弹出页面，解决办法就是降低hexo的版本，先卸载目前的hexo版本，再安装4.0.0版本的hexo即可，我的版本为4.0.0。

```
npm uninstall hexo
npm install hexo@4.0.0 -g
```

1. 首先在博客站点目录执行下面的命令安装豆瓣插件：

```
npm install hexo-douban --save	
```

2. 紧接着在博客站点目录的配置文件`_config.yml`下，添加如下配置：

```
douban: 
  user: 252345665    #这个需要修改为你个人的id  
  builtin: false   #如果想生成豆瓣页面，这个需要设置为true
  book: 
  	title: 'This is my book title' 
  	quote: 'This is my book quote' 
  movie: 
  	title: 'This is my movie title' 
  	quote: 'This is my movie quote' 
  game: 
  	title: 'This is my game title' 
  	quote: 'This is my game quote' 
  timeout: 10000
```

* 你的豆瓣ID。打开豆瓣，登入账户，然后在右上角点击 ”个人主页“，这时候地址栏的URL大概是这样：https://www.douban.com/people/xxxxxx/ ，其中的”xxxxxx”就是你的个人ID了。
* **builtin**：是否将生成页面的功能嵌入 `hexo s` 和 `hexo g` 中，默认是 `false` ，另一可选项为 `true` 。
* **title**： 该页面的标题。
* **quote**： 写在页面开头的一段话,支持html语法。
* **timeout**： 爬取数据的超时时间，默认是 10000ms，如果在使用时发现报了超时的错(ETIMEOUT)可以把这个数据设置的大一点。

3. 然后再主题配置文件`_config.yml`中添加关于此页面的菜单：(下面是我的配置)

```
menu:
    媒体:
       url: /
       icon: fas fa-list
       children:
         - name: 电影
           url: /movies
           icon: fas fa-film
         - name: 书单
           url: /books
           icon: fas fa-book
         - name: 游戏
           url: /games
           icon: fas fa-gamepad
```

4. 适配Matery主题：在 `/themes/hexo-theme-matery/layout` 文件夹下面创建一个名为 `douban.ejs` 的文件，并将下面的内容复制进去：

```
<%- partial('_partial/post-cover') %> 
<style> 
    .hexo-douban-picture img {
        width: 100%; 
    } 
</style>
<main class="content"> 
    <div id="contact" class="container chip-container"> 
        <div class="card"> 
            <div class="card-content" style="padding: 30px"> 
                <h1 style="margin: 10px 0 10px 0px;"><%= page.title %></h1> 
                <%- page.content %> 
            </div> 
        </div> 
        <div class="card"> 
            <div class="card-content" style="text-align: center"> 
                <h3 style="margin: 5px 0 5px 5px;">如果你有好的内容推荐，欢迎在下面留言！</h3> 
            </div> 
        </div> 
        <div class="card"> 
            <% if (theme.gitalk && theme.gitalk.enable) { %>
            	<%- partial('_partial/gitalk') %>
            <% } %> 
            <% if (theme.gitment.enable) { %> 
            	<%- partial('_partial/gitment') %> 
            <% } %> 
            <% if (theme.disqus.enable) { %> 
            	<%- partial('_partial/disqus') %> 
            <% } %> 
            <% if (theme.livere && theme.livere.enable) { %> 
            	<%- partial('_partial/livere') %> 
            <% } %> 
            <% if (theme.valine && theme.valine.enable) { %> 
            	<%- partial('_partial/valine') %> 
            <% } %> 
        </div> 
    </div> 
</main>
```

5. 然后在博客站点目录下的node_modules文件夹下找到hexo-douban/lib，文件夹下有三个js文件，分别为：books-generator.js 、games-generator.js 、movies-generator.js，用文本编辑器打开这三个文件，并将其文件内容末尾的代码修改为一下内容：

```
/* 原文件内容为 layout: [`page`, `post`] ，将其修改为下面的内容*/
layout: [`page`, `douban`]
```

6. 最后就是使用并生成相应的页面，执行命令如下:

```
hexo douban
```

**需要注意的是**，通常大家都喜欢用 hexo d 来作为 hexo deploy 命令的简化，但是当安装了 hexo douban 之后，就不能用 hexo d 了，因为 hexo douban 跟 hexo deploy 的前缀都是 hexo d ，你以后执行的 hexo d 将不再是 Hexo 页面的生成，而是豆瓣页面的生成.

以下是可选的命令参数：

```
-h, --help    # 帮助页面
-b, --books   # 只生成书单页面
-g, --games   # 只生成游戏页面
-m, --movies  # 只生成电影页面
```

**当站点配置文件的builtin的值为true时，生成页面的功能会嵌入到`hexo g`和`hexo s`中，在进行部署生成操作，会自动生成相应的页面**。

## 外链跳转插件

> hexo-external-link是一个跳转外链相关插件。自动为所有html文件中外链的a标签生成对应的属性。 比如 设置target=’_blank’, rel=’external nofollow noopener noreferrer’ 告诉搜索引擎这是外部链接,不要将该链接计入权重。 同时自动生成外链跳转页面,默认在根目录下 go.html;

使用 npm 或者 yarn 安装

```
## npm 安装
npm install hexo-external-link --save
## yarn 安装
yarn add hexo-external-link
```

之后再hexo博客站点根目录下添加如下配置：

```
hexo_external_link:
  enable: true
  enable_base64_encode: true
  url_param_name: 'u'
  html_file_name: 'go.html'
  target_blank: true
  link_rel: 'external nofollow noopener noreferrer'
  domain: 'your_domain' # 如果开启了防盗链，填写你的域名
  safety_chain: true
```

