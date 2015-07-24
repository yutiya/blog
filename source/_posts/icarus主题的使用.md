title: icarus主题的使用
date: 2015-07-23 16:07:53
tags: themes
categories: Hexo
---

## icarus主题的使用

### 初始化

`hexo init <folder>`
`cd <folder>`
`npm install`

### 安装主题

`git clone https://github.com/ppoffice/hexo-theme-icarus.git themes/icarus`
Rename    
    `themes\icarus\_config.yml.example` to    
    `themes\icarus\_config.yml`
Copy 
    `themes\icarus\_config.yml.site.example` to    
    your hexo blog's root directory and rename it to `_config.yml`;
Then modify `theme` setting in `_config.yml` to `icarus`.

### 更新

``` bash
    cd themes/icarus  
    git pull
```

<!-- more -->

### Categories & Tags 的使用问题

``` bash
    To enable custom categories page and tags page,    
    just copy the categories folder and tags folder under your theme's _source foler    
    into your site's source folder. Then edit theme's _config.yml and add the following lines:  

      # Header  
      menu:  
        ...  
        Categories: categories # -> add this line  
        Tags: tags # -> and add this line  
        ...  
```

### RSS的使用问题

``` bash
  $ npm install hexo-generator-feed --save

  In _config.yml:

    plugins:  
      - hexo-generator-feed

  In the theme configuration:

    # Header
    menu:
      ...
      RSS: atom.xml         <<<<<<<<
      ...
```

### 自定义文章的缩略图

``` bash
  thumbnail: http://example.com/thumbnail.jpg
  banner: http://example.com/banner.jpg
```