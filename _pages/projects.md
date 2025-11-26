---
layout: archive
title: "Projects"
permalink: /projects/
nav_exclude : false
entries_layout: grid # Add this back to ensure container styling is applied
---

## 💼 Professional Projects

{% assign professional_projects = site.work | where: "category", "Professional" %}

{% if professional_projects.size > 0 %}
  {% for post in professional_projects %}
    {% include archive-single.html 
      type="grid" 
      show_excerpt=true 
      show_date=false 
      show_author=false 
      show_collection=false 
    %}
  {% endfor %}
{% else %}
  <p>No professional projects found yet. Check back soon!</p>
{% endif %}

<div style="clear: both;"></div> 

## 📚 Academic Projects

{% assign academic_projects = site.work | where: "category", "Academic" %}

{% if academic_projects.size > 0 %}
  {% for post in academic_projects %}
    {% include archive-single.html 
      type="grid" 
      show_excerpt=true 
      show_date=false 
      show_author=false 
      show_collection=false 
    %}
  {% endfor %}
{% else %}
  <p>No academic projects found yet. Check back soon!</p>
{% endif %}

<style>
  /* Applies only to Desktop screens */
  @media (min-width: 768px) {
    .grid__item {
      /* 1. INCREASE WIDTH (Switch to 2 columns) */
      /* We use 46% width to leave room for the margins */
      width: 46% !important; 
      
      /* 2. INCREASE MARGIN LEFT (Space OUTSIDE the card) */
      margin-left: 4% !important;
      
      /* 3. INCREASE PADDING LEFT (Space INSIDE the card text) */
      padding-left: 2rem !important; 
      
      /* 4. Add bottom spacing */
      margin-bottom: 3rem !important;
    }

    /* OPTIONAL: Reset margin for the first item in every row 
       so the grid stays aligned with your headers */
    .grid__item:nth-child(2n+1) {
      margin-left: 0 !important;
    }
  }
</style>