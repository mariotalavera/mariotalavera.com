---
layout: page
title: 
permalink: /blog/
---

  <div class="wrapper">
    <!-- <h2 class="post-heading">{{ site.title }}</h2> -->
    <div class="post-col-wrapper">
      <div class="post-col post-col-1">
        <!-- injerto -->
        <ul class="post-list">
          {% for post in site.posts %}
            <li>
              <h2>
                <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
              </h2>
              <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }} </span>
            <hr color="#e7e7e7"/>

            </li>
          {% endfor %}
        </ul>  
        <!-- injerto -->
      </div>

      <div class="post-col post-col-2">
        {% include categories-list.html %}
      </div>
    </div>
  </div>