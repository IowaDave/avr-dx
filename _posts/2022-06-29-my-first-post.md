---
description: My First Post
---

This updates my first post.

<ul>
  {% for post in site.posts %}
    <li>
      <h2>
        <a href="{{site.baseurl}}{{ post.url }}"       
        {% if post.title == page.title %}
           style="color: black;"
        {% endif %}>{{ post.title }}
        </a>
        
        {% if post.title == page.title %}
          &nbsp; << You are here.
        {% endif %}
        
      </h2>
    </li>
  {% endfor %}
</ul>

My title is:

{{ page.title }}
