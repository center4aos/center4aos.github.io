---
layout: default
title: Calendar
permalink: /calendar/
description: Upcoming CAOS events.
---

## Calendar

{% assign upcoming = site.events | where_exp: "event", "event.date >= site.time" | sort: "date" %}

{% if upcoming.size > 0 %}
<ul class="event-list">
  {% for event in upcoming %}
  <li class="event-list-item">
    <time datetime="{{ event.date | date_to_xmlschema }}">{{ event.date | date: "%B %-d, %Y" }}</time>
    {% if event.time %} at {{ event.time }}{% endif %}
    — <a href="{{ event.url | relative_url }}">{{ event.title | escape }}</a>
  </li>
  {% endfor %}
</ul>
{% else %}
<p>No upcoming events at this time. Check back soon.</p>
{% endif %}
