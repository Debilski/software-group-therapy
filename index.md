---
layout: page
title: Software Group Therapy
tagline: treating the troubled programming scientist
---
{% include JB/setup %}

## Introduction

<p><img src="images/freud.jpg" class="img-polaroid" alt="Freud"></p>

Dear junior and not-so-junior scientists,

* You use and write software all day and are frustrated because you would
like to do science instead?

* You feel like you are reinventing the wheel and that there must be a
better way or a better tool?

* You just need to talk about your software problems to other people
instead of banging your head all alone against the screen?

We want to start a hopefully regular meeting for software group therapy.

Bring along your software problems and we will talk about them and try
to find solutions together.

Beer and chips will be provided courtesy of [Technologit.de](http://www.technologit.de).

Your Software Therapists,
Rike and Tiziano

## Sessions

<ul class="posts">
  {% for post in site.posts %}
    {% if post.category == "sessions" %}
      <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

## Articles

<ul class="posts">
  {% for post in site.posts %}
    {% if post.category == "articles" %}
      <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
    {% endif %}
  {% endfor %}
</ul>

