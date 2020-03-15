---
layout: about
title: About
description:
keywords:
comments: true
menu: 关于
permalink: /about/
---
<h3 class="title with-icon"><span class="glyphicon glyphicon-envelope"></span> Contact</h3>
Email:ehco1030@163.com

{% if site.data.author.workHistory %}
<h3 class="title with-icon"><span class="glyphicon glyphicon-plane cat-title"></span>Work History</h3>
<ul class="timeline">
    {% for work in site.data.author.workHistory %}
        <li class="timeline-inverted">
            {% if work.started %}
                <div class="timeline-badge info">{{ work.started }}</div>
            {% endif %}
            <div class="timeline-panel grid-block">
                <div class="timeline-heading">
                    {% if work.company %}
                        <h4 class="timeline-title">{{ work.company }} </h4>
                    {% endif %}
                    <p>
                        <small class="text-muted">
                            {% if work.title %}
                                {{ work.title }}
                            {% endif %}
                        </small>
                    </p>
                    <p>
                        <small class="text-muted">
                            {% if work.duration %}
                                <i class="fa fa-calendar"></i> {{ work.duration }} |
                            {% endif %}
                        </small>
                    </p>
                </div>
                <div class="timeline-body">
                    {% if work.description %}
                        <p>{{ work.description }}</p>
                    {% endif %}
                </div>
            </div>
        </li>
    {% endfor %}
</ul>
{% endif %}

{% if site.data.author.educationHistory %}
<h3 class="title with-icon"><span class="fa fa-book cat-title"></span>Education History</h3>
<ul class="timeline">
    {% for education in site.data.author.educationHistory %}
        <li class="timeline-inverted">
            {% if education.started %}
                <div class="timeline-badge info">{{ education.started }}</div>
            {% endif %}
            <div class="timeline-panel grid-block">
                <div class="timeline-heading">
                    {% if education.organization %}
                        <h4 class="timeline-title">{{ education.organization }}</h4>
                    {% endif %}
                    <p>
                        <small class="text-muted">
                            {% if education.degree %}
                                {{ education.degree }}
                            {% endif %}
                            {% if education.major %}
                                , {{ education.major }}
                            {% endif %}
                        </small>
                    </p>
                    {% if education.duration %}
                        <p>
                            <small class="text-muted"><i class="fa fa-calendar"></i> {{ education.duration }}
                            </small>
                        </p>
                    {% endif %}
                </div>
                <div class="timeline-body">
                    {% if education.description %}
                        <p>{{ education.description }}</p>
                    {% endif %}
                </div>
            </div>
        </li>
    {% endfor %}
</ul>
{% endif %}
