---
layout: page
title: 浩宇的博客
tagline: iOS
---
{% include JB/setup %}


MyEmail:378975015@qq.com

MyMobile:(86)18566182258


最近我在观看 [WWDC 2015 Videos](https://developer.apple.com/videos/wwdc2015/)

最近我在学习的网页 [objc中国](http://objcio.cn/)

<!--## Update Author Attributes-->
<!---->
<!--In `_config.yml` remember to specify your own data:-->
<!--    -->
<!--    title : My Blog =)-->
<!--    -->
<!--    author :-->
<!--      name : Name Lastname-->
<!--      email : blah@email.test-->
<!--      github : username-->
<!--      twitter : username-->
<!---->
<!--The theme should reference these variables whenever needed.-->
    
## 分享资料

<!--This blog contains sample posts which help stage pages and blog data.-->
<!--When you don't need the samples anymore just delete the `_posts/core-samples` folder.-->
<!---->
<!--    $ rm -rf _posts/core-samples-->
<!---->
<!--文章列表：-->

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

<!--## To-Do-->
<!---->
<!--This theme is still unfinished. If you'd like to be added as a contributor, [please fork](http://github.com/plusjade/jekyll-bootstrap)!-->
<!--We need to clean up the themes, make theme usage guides with theme-specific markup examples.-->


