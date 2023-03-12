---
layout: archive
title: "Publications"
permalink: /publications/
author_profile: true
---

This list highlights my most relevant publications that make up the bulk of the work carried out during my PhD. You can 
find my full publication list on <u><a href="{{site.author.googlescholar}}">my Google Scholar profile</a>.</u>

My thesis topic is Streaming Speech Translation, which is the task of translating (in real-time) an unbounded text stream,
which is typically the output of a Streaming ASR system. During my PhD, I have worked in four key elements 
that are required for Streaming Speech Translation: 
<ul>
<li> A high quality multilingual Speech Translation dataset</li>
<li> A Streaming Segmenter that process the output of the ASR system into sentence-like units</li>
<li> Robust latency evaluation metrics that can be applied to the Streaming scenario</li>
<li> Context-aware models and methodologies for improved Streaming translation quality</li>
</ul>

Take a look into each of the following publications in order to learn more:

{% include base_path %}

{% for post in site.publications reversed %}
  {% include archive-single.html %}
{% endfor %}
