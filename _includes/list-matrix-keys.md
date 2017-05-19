{% for account in site.data.matrix %}
{% assign account_name = account[0] %}
`{{ account_name }}`
--------------

| Device Label | Device ID | Device Key |
|-------------:|-----------|------------|
{% for device in site.data.matrix[account_name] %}| {{ device.label }} | `{{ device.id }}` | `{{ device.key }}` |
{% endfor %}
{% endfor %}

---

[\[source\]](https://github.com/{{ site.github.username }}/{{ site.github.jekyllrepo }}/blob/master/_data/{{ include.data }}.json) \| [\[sig\]](https://github.com/{{ site.github.username }}/{{ site.github.jekyllrepo }}/blob/master/_data/{{ include.data }}.json.asc)