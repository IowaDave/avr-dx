---
description: My First Post
---

This is my first post.

<ul>
  {% for post in site.posts %}
    <li>
      <h2><a href="{{ post.url }}" 
             
        {% if post.title == page.title %}
           style="color: black;"
        {% endif %}>{{ post.title }}</a>
        
        {% if post.title == page.title %}
          &nbsp; << You are here.
        {% endif %}
        
      </h2>
    </li>
  {% endfor %}
</ul>

My title is:

{{ page.title }}
