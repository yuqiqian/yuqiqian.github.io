---
layout: default
modal-id: 2
date: 2015-06-17
img: blocmarks.png
alt: image-alt
project-date: June 2015
client: Start Bootstrap
category: Web Development
description: Use this area of the page to describe your project. The icon above is part of a free icon set by <a href="https://sellfy.com/p/8Q9P/jV3VZ/">Flat Icons</a>. On their website, you can download their free set with 16 icons, or you can purchase the entire set with 146 icons for only $12!

---
>Such as Read It later, this is a website allow user to save bookmarks for the web pages as archive or to share them with friends. Bookmarks are arranged by topic and open to other users, who can like or dislike the bookmark. Users can always found the bookmarks they created or liked in their portfolio. For the flexibility, topics and bookmarks can be created by sending a simple email.

#Explanation
One of the major feature of Blocmarks is that it can create topics by emails sent by users, which is managed by a heroku addon, [Mailgun](www.mailgun.com). The mailgun server receives emails from user and transforms its content into http request, then send it to Blocmarks server. Blocmarks, routers the request to corresponding controller and action, then manage the database.

Other major feature is that Blocmarks generate preview automatically when user saves a url as bookmark. The feature is achieved by [Embedly](https://github.com/embedly/embedly-ruby), a service which can grab picture in target website.

#Problem
We encountered problems when we tried to send http request from mailgun to Blocmarks. We found that mailgun send request to Blocmarks, which does not route to correct action. 

#Solution
To solve the redirect problem, we use [Httparty](https://github.com/jnunemaker/httparty) gem to simulate http request, which helps a lot when debug at local. However, Blocmarks do receive the simulating request and treat it properly, on local and online. So The difference between Mailgun’s request and simulating request must be the problem. Finally, we find that Mailgun request has more complicate structure so that Blocmarks cannot get correct parameters. The problem was solved after we implemented the request retrieve method.