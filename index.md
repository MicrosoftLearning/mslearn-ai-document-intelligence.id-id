---
title: Latihan Azure AI Document Intelligence
permalink: index.html
layout: home
---

# Latihan Azure AI Document Intelligence

Latihan berikut dirancang untuk mendukung modul di Microsoft Learn.


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endfor %}
