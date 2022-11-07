---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

This list highlights my most relevant publications that make up the bulk of the work carried out during my PhD. You can 
find my full publication list on <u><a href="{{site.author.googlescholar}}">my Google Scholar profile</a>.</u>

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
