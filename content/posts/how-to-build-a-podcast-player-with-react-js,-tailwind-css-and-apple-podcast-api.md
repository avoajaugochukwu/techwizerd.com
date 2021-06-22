---
title: "How to Build a Podcast Player With React Js, Tailwind Css and Apple Podcast API"
author: "Ugochukwu Avoaja"
date: 2021-06-15T20:25:06+01:00
categories:
  - React
  - TailwindCSS
  - API
  - React-Redux
draft: false
---

### This article will cover all the steps and code needed to build a podcast player with React, TailwindCSS, and Apples' podcast API. It is a long one broken into five parts. I hope you are ready to dive in.

![app_sample_home_page](/img/rpp_tutorial/podcast_player.gif)
![app_sample_home_page](/img/podcast_player.gif)

## Introduction

React is a user interface framework developed by Facebook. It allows developers to build intuitive websites with rich user interactivity using HTML-like syntax. It also allows modularity, which means we can break code down into manageable modules that makes maintenance of your website easy.

In this tutorial, we are going to build a podcast application using React and Apple Podcast API. The application will allow users to browse through all the podcasts available on Apple. When users visit our deployed site, they can choose to listen to the top podcast of the day or use the search functionality that we will develop to search for any podcast of their choice available on Apple Podcast.

I chose Apple API because it offers a free API with few limitations. While experimenting with the API, I didn't hit the APIs rate limit despite some heavy reloading during the application development. I found that other podcast APIs required creating an account, and they had limited access to their free plans. Meanwhile, with Apple API you don't need to log in or provide any details, the limits are generous and no token is required to send requests.

### Who this tutorial is for
This tutorial is for intermediate developers, developers who have built a few apps with React and are looking for a comprehensive introduction to some advanced features like debounce, lazy loading, redux (state management), routing, and loading pages dynamically. Newbies can follow along but note that we glazed over details in some parts, including the CSS.

### What you will learn

In this tutorial you will learn:

1. API calls with Axios
2. Debounce
3. React Hooks
4. Responsive web design with Tailwind CSS
5. Reusable React component
6. Using the 'read more' button to hide/show text
7. Using JavaScript Audio API to play sounds in the browser
8. Apple API
9. Infinite scroll, for lazy loading
10. App layout with a fixed sidebar that does not change between renders, improving performance
11. Postman - Calling Apple API from the browser downloads a file that is hard to analyze, but postman has a "JSON" feature
12. Reverse Proxy to prevent CORS Error
13. React-Redux