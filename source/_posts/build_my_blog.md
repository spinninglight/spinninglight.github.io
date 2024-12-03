---
title: 用github page搭建个人网站
excerpt: 从jekyll到hexo
date: 2024-11-24 20:37:00
---
## 2024.11.24更新

忙活了一下午，终于将博客框架从jekyll改成了hexo  
做得不够细心，首先是安装主题时忘记建站，然后又弄错了工作流的部署分支

## 在github创建个人仓库，并访问简单的个人网站

登陆[github](https://github.com/),新建仓库，注意仓库名必须为自己的github用户名<br>
创建好之后，点击create new file，创建一个html文件，文件内容如下
```html
<h1>hello，world!<h1>
```
点击确定，等github page部署好之后，访问hhtps://用户名.github.io/,即可看到自己的主页

## 使用静态页面生成工具来生成精美的网页

可以使用静态网页生成工具Jekyll，首先要安装ruby和gem<br>
Ruby 一种简单快捷的面向对象（面向对象程序设计）脚本语言<br>
Gem 全称 RubyGem 是 ruby 的包管理工具。<br>
以下介绍了在Ubuntu22.04安装Jekyll的过程   
 **ps:看到同学用hexos的模板很帅**

## 安装ruby


```bash
sudo apt update            //更新包列表
sudo apt install ruby-full //安装ruby
ruby -v                    //验证安装
```

## 安装GEM
```bash
sudo apt install rubygems
gem -v
```
安装完gem之后可能要对gem进行换源，否则安装jekyll可能会失败
```bash
gem source -l  //查看源
gem source -r <源地址> //删除源
gem sources --add https://gems.ruby-china.com //添加源
```

## 安装Jekyll
```bash
gem install jekyll
```
## 找到自己喜欢的模板

我的模板来源在这里 [wind-theme](https://github.com/a-chacon/wind)
下载到本地，打开项目后输入命令
```bash
bundle install
bundle exec jekyll serve  
```
可以在本地启动并调试项目。

ps:bundle install有时会失败，可能是因为GEM_HOME的问题
```bash
echo $GEM_HOME //查看GEM_HOME位置
```
如果没有，可以使用以下命令修改
```bash
export GEM_HOME=/home
```
此命令临时有效，若想永久修改，需要修改配置文件
```bash
vim ~/.zshrc  //zsh配置文件
vim ~/.bashrc //普通配置文件
```
[参考教程](https://blog.51cto.com/u_15294985/7978684)

## 代码高亮

我使用的这个模板没有代码高亮功能，很low  
找了个[教程](https://www.jianshu.com/p/80be7ebb273f)

### 安装rouge
```bash
gem install kramdown
gem install rouge
```
### 配置 _config.yml
添加如下代码
```yaml
highlighter: rouge
markdown: kramdown
kramdown:
  input: GFM
  syntax_highlighter: rouge
```
### 生成 rouge css 风格文件
用以下命令生成文css文件
```bash
#                css 主题           生成的文件名
rougify style monokai.sublime > syntax_monokai.css
```
支持的 css 主题可以使用 help 命令查看
```bash
rougify help style
```
然后将生成好的 css 文件拷贝到博客的某个目录下，再到 include/head.html 文件头部引用这个新的 css 文件就行了
```html
<link href='{{ site.url }}/stylesheets/syntax_monokai.css' rel='stylesheet' type='text/css' />
```


