---
layout: page
permalink: /tools/
title: tools
description: An overview of all bioinformatics tools and pipelines developed and described in this blog.
nav: true
nav_order: 3
---

<div class="table-responsive">
<table class="table table-sm table-hover">
  <thead>
    <tr>
      <th scope="col">Tool</th>
      <th scope="col">Description</th>
      <th scope="col">Blog post</th>
    </tr>
  </thead>
  <tbody>
    {% for post in site.posts %}
      {% if post.tools %}
        {% for tool in post.tools %}
    <tr>
      <td><a href="{{ tool.url }}" target="_blank" rel="noopener noreferrer"><code>{{ tool.name }}</code></a></td>
      <td>{{ tool.description }}</td>
      <td><a href="{{ post.url | relative_url }}">{{ post.title }}</a></td>
    </tr>
        {% endfor %}
      {% endif %}
    {% endfor %}
  </tbody>
</table>
</div>
