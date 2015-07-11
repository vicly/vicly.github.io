---
layout: page
title: Archive
---

## Blog Posts

{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
{% endfor %}


## Tags

<ul>
{% for tag in site.tags %}
  <li><a class="tag_list_link" href="/tag/{{ tag[0] }}">{{ tag[0] }}</a></li>
{% endfor %}
</ul>