---
layout: resume_default
title: Resume
permalink: /resume/
---


{% include career-profile.html %}

{% unless site.data.data.sidebar.education %}
  {% include education.html %}
{% endunless %}

{% include experiences.html %}

{% include projects.html %}

{% include publications.html %}

{% include skills.html %}