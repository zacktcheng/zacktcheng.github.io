---
layout: default
title: Home
---

# Zack's Software Development Study Notes

<img src="assets/images/S__17408045.jpg" width="128" height="128" >

**Welcome!**

My name is Zack Cheng, I am currently a studying the Applied Computer Science post-bacc program at the University of Colorado Boulder.

This blog documents my study notes for the AI For JavaScript Developers (OpenAI, Embeddings, Vercel AI) on Udemy. Follow along as I work through each selected module in the certification syllabus and share my learning journey. This blog was created as part of my semester project for the University of Colorado, Boulder's CSPB 3112: Professional Development in Computer Science course.

Here are links to the course I am following along with:
- [AI For JavaScript Developers (OpenAI, Embeddings, Vercel AI)](https://www.udemy.com/course/ai-for-js-devs)


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

Here is my contact information:
- [Linkedin](https://www.linkedin.com/in/zack-cheng-3684506b/)

For detailed professional information such as skills and experience, please check out my [resume](https://zacktcheng.github.io/resume.html)
