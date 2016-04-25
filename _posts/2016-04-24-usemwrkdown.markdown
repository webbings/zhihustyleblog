---
layout: post
title: 使用Markdown写文章
tags: 杂货箱
---

博归纳写博客的一些小技巧

### 1.一级标题

{% highlight html %}
## 这是一级标题
{% endhighlight %}

`效果如下：`

## 这是一级标题

### 2.二级标题

{% highlight html %}
### 这是二级标题
{% endhighlight %}

`效果如下：`

### 这是二级标题

### 3.加背景

{% highlight html %}
`我有背景`
{% endhighlight %}

`效果如下：`

`我有背景`

### 4.代码

{% highlight html %}
{% highlight html %}
int i = 0;
{% endhighlight %}
{% endhighlight %}

`效果如下：`

{% highlight html %}
int i = 0;
{% endhighlight %}

### 5.引用图片

{% highlight html %}
[![picture name](/upload/famoustestimg.jpg)](http://www.zhihu.com)
{% endhighlight %}

`效果如下：`

[![picture name](/upload/famoustestimg.jpg)](http://www.zhihu.com)

### 6.引用链接

{% highlight html %}
[点我可以转向知乎](http://www.zhihu.com)
{% endhighlight %}

`效果如下：`

[点我可以转向知乎](http://www.zhihu.com)

### 7.加粗

{% highlight html %}
**我被加粗了**
{% endhighlight %}

`效果如下：`

**我被加粗了**