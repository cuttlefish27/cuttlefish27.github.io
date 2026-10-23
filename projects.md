---
layout: page
title: Projects
permalink: /projects/
---

# Robotic Hand
My ongoing project to build an autonomous, human-like robotic hand. 

Link to code repo: 
<a href="https://github.com/cuttlefish27/robotic_hand_controller" target="_blank">
  Robotic Hand Controller
</a>

Link to CAD files:
<a href="https://github.com/cuttlefish27/RoboticHand" target="_blank">
  Robotic Hand CAD
</a>

### Posts

<ul>
  {% for post in site.posts %}
    {% if post.project == "robotic hand" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> — {{ post.date | date_to_string }}
      </li>
    {% endif %}
  {% endfor %}
</ul>

______________________
# Magnetic Sensor
Designing and developing a soft magnetic tactile sensor that can measure forces for use in robotic sensing.

Link to project repo:
<a href="https://github.com/cuttlefish27/robotic_hand_controller/tree/main/SensorDevelopment" target="_blank">
  Magnetic Sensor Development
</a>

### Posts
<ul>
  {% for post in site.posts %}
    {% if post.project == "magnetic sensor" %}
      <li>
        <a href="{{ post.url }}">{{ post.title }}</a> — {{ post.date | date_to_string }}
      </li>
    {% endif %}
  {% endfor %}
</ul>

_____________________
# Transformer Translation
Implementing and training a transformer based off of the paper "Attention is All You Need" using pytorch

Link to project repo:
<a href="https://github.com/cuttlefish27/pytorchTransformer" target="_blank">
  Transformer Machine Translation
</a>
