---
layout: page
title: Post List
description: >
  Posts from Alexander Gude on technology, data science, machine learning, and
  anything else I find interesting. Updated just often enough! Come on in!
---

{% comment %} From: https://stackoverflow.com/a/43190996 {% endcomment %}
{% assign postsByYear = site.posts | group_by_exp:"post", "post.date | date: '%Y'" %}
{% for year in postsByYear %}
  <h2>{{ year.name }}</h2>
  <ul>
    {% for post in year.items %}
      <li>
        <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
        {{ post.description }}
      </li>
    {% endfor %}
  </ul>
{% endfor %}
