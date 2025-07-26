---
layout: about
title: about
permalink: /
subtitle: Science, Technology and AI enthousiast, dad, sensei & coffee lover.

profile:
  align: right
  image: prof_pic.jpg
  image_circular: false # crops the image to make it circular
  more_info: >
    <p>ILVO - Plant Sciences Unit - Plant Health</p> 
    <p>Burgemeester van Gansberghelaan 96</p>
    <p>9820 Merelbeke-Melle</p>
    <p>Belgium</p>

selected_papers: true # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

announcements:
  enabled: true # includes a list of news items
  scrollable: true # adds a vertical scroll bar if there are more than 3 news items
  limit: 5 # leave blank to include all the news in the `_news` folder

latest_posts:
  enabled: false
  scrollable: true # adds a vertical scroll bar if there are more than 3 new posts items
  limit: 3 # leave blank to include all the blog posts
---

I am a passionate bioinformatician at ILVO (Flanders Research Institute for Agriculture, Fisheries and Food) and the EU Reference Laboratory for bacterial plant diseases. My work focuses on bacterial genomics for plant health, using high-throughput sequencing, artificial intelligence, and advanced computational tools. I develop robust pipelines for genomic data analysis, combining Linux shell scripting, R, Python and Snakemake. My research involves designs for pathogen detection, phylogenomics, genotyping, and comparative genomics, with the goal of accelerating discoveries in phytobacterial research and supporting sustainable plant health. By making this blog, i hope to help early-career researches and cross-disciplinary collaborators, believing that sharing knowledge and tools is essential for advancing science and technology.

{% include announcements.html %}

{% if page.selected_papers %}
  {% include publications.html %}
{% endif %}

{% if page.social %}
  {% include social.html %}
{% endif %}
