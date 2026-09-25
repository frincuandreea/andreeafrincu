---
layout: default
title: Articles
permalink: /blog/
lang: en
---

<div class="portfolio-articles">
  <section class="articles-hero">
    <div class="portfolio-container articles-hero__inner">
      <p class="portfolio-eyebrow">Technical Articles</p>
      <h1>Notes, solutions and lessons from software development.</h1>
      <p>I write about practical implementation challenges and technologies I use in real projects, with a particular focus on AEM, Java and web development.</p>
    </div>
  </section>

  <section class="portfolio-section">
    <div class="portfolio-container">
      <div class="articles-toolbar">
        <div><p class="portfolio-eyebrow">From the Blog</p><h2>All Articles</h2></div>
        <p>{{ site.posts | size }} published articles</p>
      </div>
      <div class="articles-grid">
        {% assign articles = site.posts | where: "lang", page.lang %}
        {% if articles.size == 0 %}{% assign articles = site.posts %}{% endif %}
        {% for post in articles %}
        <article class="article-card">
          {% if post.thumbnail %}
          <a class="article-card__image" href="{{ post.url | relative_url }}" aria-label="Read {{ post.title }}"><img src="{{ post.thumbnail | relative_url }}" alt=""></a>
          {% else %}
          <a class="article-card__placeholder" href="{{ post.url | relative_url }}" aria-label="Read {{ post.title }}"><span>&lt;/&gt;</span></a>
          {% endif %}
          <div class="article-card__body">
            <div class="article-card__meta"><time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>{% if post.categories and post.categories.size > 0 %}<span>{{ post.categories | first }}</span>{% endif %}</div>
            <h2><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h2>
            <p>{{ post.excerpt | default: post.content | strip_html | strip_newlines | truncate: 170 }}</p>
            <a class="portfolio-text-link" href="{{ post.url | relative_url }}">Read article →</a>
          </div>
        </article>
        {% endfor %}
      </div>
    </div>
  </section>

  <section class="articles-about">
    <div class="portfolio-container articles-about__inner">
      <div><p class="portfolio-eyebrow">Why I Write</p><h2>Sharing practical engineering knowledge.</h2></div>
      <p>I started this blog to document solutions to development problems that are useful, interesting or likely to appear again. The goal is simple: turn implementation experience into something another developer can reuse.</p>
    </div>
  </section>
</div>
