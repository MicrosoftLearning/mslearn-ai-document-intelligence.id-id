---
title: Latihan Azure AI Document Intelligence
permalink: index.html
layout: home
---

Latihan berikut ini dirancang untuk membekali Anda dengan pengalaman belajar praktis di mana Anda akan menjelajahi tugas-tugas umum yang biasa dilakukan pengembang saat membangun solusi kecerdasan dokumen di Microsoft Azure.

> **Catatan\**: Untuk menyelesaikan latihan, Anda memerlukan langganan Azure dengan izin dan kuota yang memadai untuk menyediakan sumber daya Azure yang diperlukan. Jika Anda belum memilikinya, Anda bisa mendaftar untuk mendapatkan [akun Azure](https://azure.microsoft.com/free). Ada opsi uji coba gratis untuk pengguna baru yang menyertakan kredit selama 30 hari pertama.

## Latihan

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %} {% for activity in labs  %} {% if activity.url contains 'ai-foundry' %} {% continue %} {% endif %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }})

{{activity.lab.description}}

{% endfor %}

> **Catatan**: Meskipun Anda dapat menyelesaikan latihan ini sendiri, latihan ini dirancang untuk melengkapi modul di [Microsoft Learn](https://learn.microsoft.com/training/paths/extract-data-from-forms-document-intelligence/); di mana Anda akan menemukan penyelaman yang lebih dalam ke dalam beberapa konsep dasar yang menjadi konsep dasar latihan ini.
