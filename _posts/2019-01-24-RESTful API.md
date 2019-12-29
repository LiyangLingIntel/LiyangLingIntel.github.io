---
layout:     post
title:      "What is RESTful API"
subtitle:    "什么是REST API"
date:       2019-01-24
author:     Lyon Ling
header-img: img/post-bg-restfulapi.jpg
catalog: true
tags:
    - WebDeveloping
    - 转载
---

>RESTful API is not a new concept for today's Internet developing, but I always forget the meaning of that.
>
>So now I write it here.

### Foreword

With the development of mobile devices and the Internet, there are more and more devices and platforms connecting into the Internet, so it's needed to define a rule or mechanism to unify the ways of API design.

Here comes RESTful.

<img src="/img/in-post/2019-01-24-RESTfulAPI/restfulapi.png" />

### RESTful API

**REST**, abbreviation of **Representational State Transfer**. 

That is a design philosophy of Internet Applications API. Which means we should and only should use **URL** to locate resources and **HTTP verbs** (GET, POST, DESC, DELETE, PUT) to describe actions.

In this we, each kind of resource on server, like a file, a picture, a movie, have its respective URL address. If we want to do some operations on these resouces on server, some related actions unter HTTP protocol should be taken.

For simplification, resources in URL only could be in **Noun** form, while using HTTP actions to operate resources.

Here are some examples:

This is not RESTful:

```javascript
GET /blog/getArticles
GET /blog/addArticles
GET /blog/editArticles
GET /rest/api/deleteArticles?id=1
```

This is RESTful:

```javascript
GET /blog/Articles
POST /blog/Articles
PUT /blog/Articles
DELETE /blog/Articles/1
```

*Some of these ideas come from [here](https://www.jianshu.com/p/6baf8554b3f4?from=timeline&isappinstalled=0)*