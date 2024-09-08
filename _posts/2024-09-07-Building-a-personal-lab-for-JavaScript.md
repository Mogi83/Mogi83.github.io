---
layout: post
title: "Building a Personal Web Development Lab"
---

## Why Make Another Website?

Over the past two weeks, I’ve been setting up a new site to have a dedicated space for testing my code without risking damage to this blog. This new site, [Static Web Demos](https://mogi83.github.io/Static-Web-Demos/), will serve as a playground for web development and smaller projects. Hosting these demos on GitHub Pages, just like this blog, makes it easy to share my work with others. I’ll walk you through what I’ve been up to, provide a general overview of the site’s setup, and show you how to create something similar.

## Some Background and the Foundations of a Website:

### What Makes a Website?

I’ve spent the past week diving into the core components of this new site. At its essence, a website is built from HTML, CSS, and JavaScript.

- **HTML** (Hypertext Markup Language) provides the structure and content of a page, linking to other files and elements.
- **CSS** (Cascading Style Sheets) enhances the visual presentation of HTML by defining colors, fonts, and layout.
- **JavaScript** adds interactivity and functionality, enabling features like buttons, pop-ups, and animations.

These three languages come together to make websites and are known as **front-end development**.


### Front-End vs. Back-End Development:

While the front end involves HTML, CSS, and JavaScript, the back end is responsible for the behind-the-scenes processes that users don’t see. This includes server-side scripting, databases, and server management tools that handle data storage, user authentication, and communication between the front end and the server.

The distinction between front-end and back-end development also differentiates static from dynamic websites.

**Example:**  

A banking website provides personalized pages based on user authentication. After logging in, the backend generates a custom page for that user that shows their account balance.  

This is an example of a dynamic site that utilizes the **full stack**. The full stack is just a piece of software that inorperates both the front end and the back end in one code base, websites like these make up modern webpages.  


## Tools of the Trade:

The demo site I’m working on is a static site hence it's name. I don't intend to dive into the full stack for some time I want to get the front end down first. I will also have a class on the full stack in a few semesters.  

As a refresher from the Initial Commit post, Static sites are built with pre-made or custom assets without connecting to server-side scripts. This means every user sees the same content. In contrast, dynamic sites use server-side scripts to generate pages tailored to each user's request. This has some advantages, namely a reduction in complexity for the developer.


**Resources I Used:**

For learning how to build a website, I relied heavily on W3Schools for their documentation of [HTML](https://www.w3schools.com/html/default.asp), [CSS](https://www.w3schools.com/css/default.asp), and [JavaScript](https://www.w3schools.com/js/default.asp). They offer comprehensive, well-organized information that you can reference. I found W3Schools's stuff to be extremely useful as I haven't built a website from scratch since 2014!

## Building the Website from Scratch

To start, pick your favorite code editor. I used [Notepad++](https://notepad-plus-plus.org) back in 2014, I’m now using [Atom](https://atom.io). It really doesn't matter what you want to use even basic editors like the default Windows Notepad will work just make sure you save your files with the right extension.

Once you have that I recommend  you organize your project by creating a folder with the following structure:



```
Website folder:
   index.html
   about.html
   contact.html

   Assets folder:

      CSS folder:
          stylesheet.css

      JavaScript folder:
          script.js

      Any other site assets and folders you need.

```


This is just my opinion however you want to organize your sites filesystem is up to you, just ensure your files are organized in a way that makes sense to you.  

Once you are happy with your folders start by writing HTML for your `index.html` page, create a stylesheet, and add any JavaScript you need. To test your site, if you have Python installed, open the windows command prompt and run:

```
python -m http.server 8000
```


This will start a local server. Open your browser and go to [http://localhost:8000](http://localhost:8000). Navigate to your site’s folder and open `index.html` to view your site.

The key is to build your site using a basic HTML skeleton. A HTML skeleton is just a basic template you can use to quickly make new pages for your site without having to rewrite some of the essential parts you need on every site.

## Conclusion

[Static Web Demos](https://mogi83.github.io/Static-Web-Demos/) is still in its early stages, but I’ve set up the basic structure and a functional stylesheet. Over the next week or two, I plan to add a demo to showcase involving APIs.
