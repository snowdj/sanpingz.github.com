---
layout: landing
title: mceiba的个人博客
head_title: 晨旭园
description: |
  程序员的技术生涯
---

<div style="margin-bottom:20px;">
  <div style="width:100%">
    <h3 style="margin-bottom:5px; margin-left:1.388%; color:#333333;">努力的人还是会有机会的、、</h3>
    <!-- <h6 align="right" style="font-size:12px; margin-right:0.56%">喜欢我或者我的博客，可以把它加入你的<a href="javascript:void(0)" onclick="window.external.AddFavorite(location.href, document.title)">收藏夹</a>
    </h6> -->
  </div>
  <div class="divbox" style="width:96%;margin-right:0px;padding-right:0.694%;">
    <h1 id="start-now" style="margin-left: 0; margin-right: 0; font-size: 22px;">最新博文</h1>
    <div style="margin-left:-1.04%">
    {% assign posts_all = site.posts %}
    {% assign count = 10 %}
    {% include custom/posts_all %}
    </div>
  </div>
  
</div>
