---
layout: post
title: A Simple Explanation of AngularJS SEO
---
试着用通俗一点的解释说明AngularJS的搜索引擎优化(SEO)是如何实现的。

<!--more-->

先来说一点背景介绍。在单页应用(Single Page Application)火起来前，大部分网站都是服务器端准备好完整的HTML，然后直接发送到浏览器进行展示。对于搜索引擎来说，只需要网站提供站点地图(Sitemap)就可以直接抓取到完整的网页信息。

基于AngularJS (或 BackboneJS 等) 的网站对于大部分搜索引擎来说都是灾难，原因在于当搜索引擎访问页面时，拿到的不是传统的HTML，而是混合着HTML和JavaScript框架特殊写法的乱七八糟的东西。搜索引擎本来希望拿到这样的代码  
{% highlight js %}
  <span> Hello World </span>
{% endhighlight %}

结果发现拿来的是这样子的:  
{% highlight js %}
  <span> {% raw %}{{ myModel.greeting }} {% endraw %} </span>
{% endhighlight %}

原因是AngularJS应用会在浏览器端动态生成HTML，而大部分搜索引擎都不支持抓网页时运行JavaScript ([Google 除外](http://googlewebmastercentral.blogspot.no/2014/05/understanding-web-pages-better.html))。

各大搜索引擎也知道无法运行JavaScript是个缺点，所以大家搞了个约定：网站可以在链接里加入 HashBang (#!) 符号，搜索引擎当遇到这样的符号时，会把网页链接稍微变一下。举个例子，如果本来的网页链接是这样子的:  
{% highlight js %}yourwebsite.com/#!/some/page/with/ajax/content{% endhighlight %}

搜索引擎就会把它变成:  
{% highlight js %}yourwebsite.com/?_escaped_fragment_=/some/page/with/ajax/content{% endhighlight %}

注意搜索引擎把原来链接里的`#!`换成了`?_escaped_fragment_=`。为什么要这么做呢？这意思其实是，如果网站觉得JavaScript用得太多，搜索引擎很难获取实际内容，就用`#!`让搜索引擎换条路重新抓页面。当搜索引擎换了链接后，网站就会提供专门为搜索引擎准备好的静态完整的HTML。

打个俗一点的比方，社会大哥Google去各个店家收保护费，只收现金。有的店家正常营业没有现金，就跟大哥说，店门口给您放了个特殊标志，下次您看到这个标志就直接移步进我们贵宾室，我们在里面准备好了给您的现金上供。

对于使用了AngularJS的单页面应用来说，也需要给搜索引擎大哥单独准备一套静态的HTML。做SEO时，除了把URL中的`#`换成`#!`之外，还要生成一些网页快照(Snapshot)，当搜索引擎用特殊链接访问时直接提供。

有一个开源工具叫做[PhantomJS](http://phantomjs.org/)可以帮我们做到这个。基本原理是：当使用链接访问时，网页需要加一个Flag来通知何时内容全部加载完毕。PhantomJS提供了一个跑在服务器上的脚本 `.phantomjs-runner.js`，可以一个一个去访问你想缓存的网页，每当网页加载成功时，PhantomJS就会把最后生成的HTML存储在服务器的一个文件目录下。网站需要设置一下，当搜索引擎访问链接时，利用HashBang转换出的新链接提供事先缓存好的HTML，搜索引擎就可以从这些静态网页上了解到原来动态的内容了。

更详细的信息可以参考 [YearOfMoo](http://www.yearofmoo.com/2012/11/angularjs-and-seo.html) 的解释。

\- Bellevue, WA
