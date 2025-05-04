---
layout: default
title: Home
---

*Author: Zack Cheng*
<br/>

# Zack's Software Development Study Notes

<img src="assets/images/S__17408045.jpg" width="256" height="256" >

**Welcome!** My name is Zack Cheng, I am currently a studying the Applied Computer Science post-bacc program at the University of Colorado Boulder. This blog documents my study notes for the Linux Foundation Introduction to Kubernetes course and Udemy Certified Kubernetes Application Developer course. Follow along as I work through each selected module in the certification syllabus and share my learning journey. This blog was created as part of my semester project for the University of Colorado, Boulder's CSPB 3112: Professional Development in Computer Science course.

Here are links to the course I am following along with:
- [Linux Foundation Introduction to Kubernetes](https://training.linuxfoundation.org/training/introduction-to-kubernetes/)
- [Udemy Certified Kubernetes Application Developer](https://www.udemy.com/course/certified-kubernetes-application-developer/) 

## Lastest Posts

<table>
    <thead>
        <tr>
            <th>Post</th>
            <th>Date</th>
        </tr>
    </thead>
    <tbody>
        {% for post in site.posts %}
        <tr>
            <td><a href="{{ post.url | relative_url }}">{{ post.title }}</a></td>
            <td>{{ post.date | date: "%b. %d, %Y" }}</td>
        </tr>
        {% endfor %}
    </tbody>
</table>
