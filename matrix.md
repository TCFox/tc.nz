---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
---

Matrix Device Keys
==================

`@tcfox:tc.nz`
--------------

| Device Label | Device ID | Device Key |
|-------------:|-----------|------------|
{% for device in site.data.matrix %}| {{ device.label }} | `{{ device.id }}` | `{{ device.key }}` |
{% endfor %}