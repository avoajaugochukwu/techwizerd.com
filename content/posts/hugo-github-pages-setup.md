---
title: "How to create Hugo blog on Github Pages Using Windows"
date: 2021-02-18T14:35:25+01:00
url: "posts/how-to-create-hugo-blog-on-github-pages-using-windows"
author: "Ugochukwu Avoaja"
categories:
  - Hugo
  - Github
  - Github Pages
  - Blogging
draft: false
---

Hugo blog is becoming one of the most popular static site generators.

The first time I used it, it blew me away by how easy it was to set up a blog on my local machine. It is also very easy to publish your hugo site to Github Pages for free.

In this tutorial, I am going to show you how to set up your Hugo blog on Github Pages. We would be using this [theme](https://themes.gohugo.io/hugo-papermod/). You do not need to pay for hosting or provide your credit card details.


### Step 1: Installing Hugo on your windows machine.
You can get Hugo by using [Chocolatey](https://chocolatey.org/install). Chocolatey is a windows package manager.

After installing Chocolatey. Visit [Hugo website](https://gohugo.io/getting-started/installing/#chocolatey-windows), for instructions on how to install Hugo.

You can also install it [directly from Github](https://gohugo.io/getting-started/installing/#source). If you do not want to install Chocolatey.


### Step 2: Creating a new project
After installing Hugo, type hugo in your cmd to ensure that it is working.
```
hugo
```
If it was successfully installed, we can go ahead and start a new site in a directory of your choice.

```
hugo new site test.com -f yml
```

This code creates a new site called *test* and forces it to use *yml* format for its' config file, which is more readable than *toml* files. 
It also creates a folder called test that will house all our Hugo resources.

You should see a message like so:

```
Congratulations! Your new Hugo site is created in C:\computer\Hugo\test.com

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>\<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
```


### Step 3: Add a Hugo theme
As discussed earlier, Hugo has a lot of themes that makes development easy. We would use the [PaperMod theme](https://themes.gohugo.io/hugo-papermod/).

Run the following code one after the other in your command prompt.

```
cd test.com
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod --depth=1
```

This code changes the directory to the test.com.
It initializes git in the current folder.
And finally pulls the PaperMod theme from Github into the ```themes/PaperMod``` folder.

Your directory should now look like this.

![test.com directory](/img/test.com_directory.png)

Next, visit the [PaperMod installation page](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation#sample-configyml) and copy the content of the sample ```config.yml.```
Overwrite the original content of the config.yml file, in the root of our test.com folder.

Then run this in your command prompt.
```
hugo server
```
This will start the Hugo server. Visit the ```localhost``` URL where Hugo says the site is running. And you should see this.

![test.com default page](/img/test.com_default_page.png)

This is the default home page of the PaperMod theme.

### Step 4: Add pages
To add pages.

Run the command:
```
hugo new posts/html.md
```

This creates a new folder called *posts* and a new markdown file called *html.md*.

The file should have a title, date and draft status.

Change the draft status to false or delete that line.

Copy the text below into the *html.md* file for test purposes.

---

> Hypertext Markup Language (HTML) is the standard markup language for documents designed to be displayed in a web browser. It can be assisted by technologies such as Cascading Style Sheets (CSS) and scripting languages such as JavaScript.

---

Run in the command prompt

```
hugo server -D
```

The *-D* flag, tells the server to also show drafts, because our html post has draft set to true. You can set draft to false, or remove that line entirely. Then you do not need the 
*-D* flag anymore. 

> Note that drafts will not be added to the site when we build it later.

You should see that our first post.

![test.com draft post](/img/test.com_draft_post.png)

At this point we are ready to move our blog to Github Pages.

### Step 5: Set up repository

Firstly, in the *config.yml* file, add this line ```publishDir: "docs"``` after ```theme: PaperMod```. This tells Hugo to publish our 
site into the directory called *docs*, when we build.

Then run:

```
hugo -t PaperMod
```
The PaperMod is the name of our theme, if you are using another theme. You would type the name of your theme in its' place.

This code builds our site into the *docs* folder.

Visit your github profile and create a new repository called test.com.

From our test.com directory on our local machine.
Run the following code:

```
git remote add origin <repository URL>
git add .
git commit -m "Initial Commit"
git push origin master
```

This code adds a remote url to our repository, adds and commits our work. Then finally pushes it to our remote repository.

### Step 6: Github Pages

From the new repository, click on settings then scroll down to the Github Pages section.

For source, select branch as master and change ```/(root)``` to ```docs```, then save.

After saving, scroll down back to the Github Ppages section, then copy the new URL that Github has provided. That is where our website is located.

We would copy this URL into our *config.yml* file.

In the *config.yml* file replace the value for baseURL with the github page link we just copied.

Then run the following code:

```
git add .
git commit -m "Updated baseURL"
git push origin master
```
This updates the remote repository with our latest changes.

At this stage, when you visit the Github Pages URL, you should see the site we just built.

That is how we connect Hugo to Github Pages.

The benefit is that you can write your posts locally in markdown, and once you push to the remote repository, you website will be updated automatically.

If you would like to host your website on your own domain. This [link](https://www.namecheap.com/support/knowledgebase/article.aspx/9645/2208/how-do-i-link-my-domain-to-github-pages/) provides instructions on how to achieve that with namecheap web host.