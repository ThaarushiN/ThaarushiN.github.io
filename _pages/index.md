---
layout: splash
title: "Tharushi Herath"
permalink: /
header:
  overlay_color: "#000"
  overlay_filter: "0.4"
  overlay_image: /assets/images/header.png
  actions:
    - label: "View My Projects"
      url: "/projects/"
excerpt: >
  Trainee AI Developer & Data Science Graduate passionate about building intelligent systems that connect data, engineering, and real-world impact.
feature_row:
  - image_path: /assets/images/ai-engineering.png 
    alt: "AI Engineering"
    title: "AI Engineering"
    excerpt: "Building and deploying scalable AI solutions using FastAPI, Azure, and MLOps tools."
    url: "/projects/"
    btn_label: "View Projects"
    btn_class: "btn--primary"
  - image_path: /assets/images/blog.png
    alt: "Tech Blog"
    title: "Tech Blog"
    excerpt: "Insights and tutorials on AI, Machine Learning, and practical engineering workflows."
    url: "/blog/"
    btn_label: "Read Blog"
    btn_class: "btn--primary"
  - image_path: /assets/images/about.png
    alt: "About"
    title: "About Me"
    excerpt: "Learn more about my journey into AI and what drives me as an engineer."
    url: "/about/"
    btn_label: "Learn More"
    btn_class: "btn--primary"
---

{% include feature_row id="feature_row" %}

{% comment %}
<h2>Professional Projects</h2>
{% assign professional = site.work | where: "category", "Professional" %}
{% include collection.html collection=professional %}

<h2>Academic Projects</h2>
{% assign academic = site.work | where: "category", "Academic" %}
{% include collection.html collection=professional %}
{% endcomment %}

<style>
  .feature__wrapper .archive__item-teaser img {
    width: 120px;  
    height: 75px;  
    object-fit: contain; 
    margin: 0 auto; 
  }
</style>