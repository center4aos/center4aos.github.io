---
layout: default
title: CAOS Home
permalink: /
description: The intersection of disability, accessibility, and open source.
---

## Welcome to the Center for Accessibility and Open Source

Open-source accessibility is our primary mission: We support disability inclusion in open-source communities, and open-source assistive technologies of all kinds.

> "CAOS is building a future in which the educational and economic opportunities promised by open source are also open to people with disabilities."
> — Dr. Joshua A. Miele, CAOS Founder

---

### Updates from the CAOS Blog

{% if site.posts.size > 0 %}
<ul>
  {% for post in site.posts limit:3 %}
    <li>
      <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      <span> — <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %-d, %Y" }}</time></span>
    </li>
  {% endfor %}
</ul>
<p><a href="{{ "/blog" | relative_url }}">All posts →</a></p>
{% else %}
<p>Blog posts coming soon. <a href="{{ "/about/" | relative_url }}">Learn more about CAOS.</a></p>
{% endif %}

---

### Upcoming CAOS Events

{% assign upcoming = site.events | where_exp: "event", "event.date >= site.time" | sort: "date" %}
{% if upcoming.size > 0 %}
<ul>
  {% for event in upcoming limit:3 %}
  <li>
    <a href="{{ event.url | relative_url }}">{{ event.title | escape }}</a>
    — <time datetime="{{ event.date | date_to_xmlschema }}">{{ event.date | date: "%B %-d, %Y" }}</time>
    {% if event.time %} at {{ event.time }}{% endif %}
  </li>
  {% endfor %}
</ul>
<p><a href="{{ "/calendar/" | relative_url }}">Full calendar →</a></p>
{% else %}
<p>No upcoming events at this time. <a href="{{ "/calendar/" | relative_url }}">Check the calendar.</a></p>
{% endif %}

---

### Stay Connected

Subscribe to CAOS News to receive updates on our work, events, and opportunities to get involved.

<form action="https://buttondown.com/api/emails/embed-subscribe/caos" method="post" aria-label="Subscribe to CAOS News newsletter">
  <div>
    <label for="home-bd-email">Email address</label>
    <input type="email" name="email" id="home-bd-email" required autocomplete="email">
  </div>
  <div>
    <button type="submit">Subscribe to CAOS News</button>
  </div>
</form>
