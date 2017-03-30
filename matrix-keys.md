---
layout: default
title: Matrix Device Keys
permalink: /matrix-keys/
---

{{ page.title }}
==================

{% for account in site.data.matrix %}
{% assign account_name = account[0] %}
`{{ account_name }}`
--------------

| Device Label | Device ID | Device Key |
|-------------:|-----------|------------|
{% for device in site.data.matrix[account_name] %}| {{ device.label }} | `{{ device.id }}` | `{{ device.key }}` |
{% endfor %}
{% endfor %}