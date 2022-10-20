---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

This list highlights what I believe are my most relevant publications. You can 
find the full list on <u><a href="{{site.author.googlescholar}}">my Google Scholar profile</a>.</u>

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
