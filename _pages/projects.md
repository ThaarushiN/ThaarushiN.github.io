---
layout: archive
title: "Projects"
permalink: /projects/
nav_exclude : false
entries_layout: grid # Add this back to ensure container styling is applied
---

**Project Highlights - Start Here:**

<div class="highlight-grid">
  <div class="highlight-card">
    <i class="fas fa-search"></i>
    <h3><a href="/projects/ansell-cv-defect-detection/">Real-Time Defect Detection for Glove Packaging</a></h3>
    <p>Computer Vision & IoT</p>
  </div>
  <div class="highlight-card">
    <i class="fas fa-brain"></i>
    <h3><a href="/projects/global-rubber-llm/">LLM-Based Document Analyzer and Chatbot System</a></h3>
    <p>AI/LLM</p>
  </div>
  <div class="highlight-card">
    <i class="fas fa-water"></i>
    <h3><a href="/projects/meander-prediction/">River Meander Migration Prediction</a></h3>
    <p>Deep Learning & GIS</p>
  </div>
  <div class="highlight-card">
    <i class="fas fa-home"></i>
    <h3><a href="/projects/indoor-monitoring/">IoT-Based Indoor Environment Monitoring</a></h3>
    <p>Azure IoT</p>
  </div>
</div>

<style>
  .highlight-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 1.5rem;
    margin: 2rem 0;
  }
  
  .highlight-card {
    padding: 1rem;
    border: 2px solid #0d341c;
    border-radius: 8px;
    text-align: center;
    transition: all 0.3s ease;
  }
  
  .highlight-card:hover {
    box-shadow: 0 4px 12px rgba(13, 52, 28, 0.2);
    transform: translateY(-2px);
    border-color: #0d341c;
  }
  
  .highlight-card i {
    font-size: 2rem;
    color: #0d341c;
    display: block;
    margin-bottom: 0.5rem;
  }
  
  .highlight-card h3 {
    margin: 0.5rem 0;
    font-size: 0.95rem;
  }
  
  .highlight-card h3 a {
    text-decoration: none;
    color: inherit;
  }
  
  .highlight-card h3 a:hover {
    color: #0d341c;
  }
  
  .highlight-card p {
    margin: 0.25rem 0 0 0;
    color: #666;
    font-size: 0.85rem;
  }
</style>

---

## Professional Projects

<span id="defect-detection"></span>
<span id="document-analyzer"></span>

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

## Academic Projects

<span id="iot-monitoring"></span>
<span id="meander-prediction"></span>

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