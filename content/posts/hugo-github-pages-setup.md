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

The first time I used it, it blew me away by how easy it was to set up a blog on my local machine. It is also effortless to publish your Hugo site to Github Pages for free.

In this tutorial, I will show you how to set up your Hugo blog on Github Pages. We would be using this [theme](https://themes.gohugo.io/hugo-papermod/). You do not need to pay for hosting or provide your credit card details.


### Step 1: Installing Hugo on your windows machine.
You can get Hugo by using [Chocolatey](https://chocolatey.org/install). Chocolatey is a windows package manager.

After installing Chocolatey. Visit [Hugo website](https://gohugo.io/getting-started/installing/#chocolatey-windows), for instructions on how to install Hugo.

You can also install it [directly from GitHub](https://gohugo.io/getting-started/installing/#source). If you do not want to install Chocolatey.


### Step 2: Creating a new project
After installing Hugo, type `hugo` in your cmd to ensure that it is working.
```
hugo
```
If that was successfully installed, you will see the following output:

```jsx
Total in 2 ms
Error: Unable to locate config file or config directory. Perhaps you need to create a new site.
       Run `hugo help new` for details.
```

You can go ahead and start a new site in a directory of your choice.

```
hugo new site test.com -f yml
```

This code creates a new site called `test.com` and forces it to use `yml` format for its' config file, which is more readable than `toml` files. 
It also creates a folder called `test.com` that will house all our Hugo resources.

You should see a message like:

```
Congratulations! Your new Hugo site is created in C:\file\path\test.com

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

This code changes the directory to `test.com`.
It initializes git in the current folder *(git is needed to push the code to GitHub and publish to GitHub Pages)*.
And finally, it pulls the PaperMod theme from Github into the ```themes/PaperMod``` folder.

Your directory should now look like this:

![test.com directory](/img/test.com_directory.png)

Next, visit the [PaperMod installation page](https://github.com/adityatelange/hugo-PaperMod/wiki/Installation#sample-configyml) and copy the content of the sample ```config.yml.```
Overwrite the original content of the ```config.yml``` file, in the root of the ```test.com``` folder.

Then run this in your command prompt.
```
hugo server
```
This will start the Hugo server. It will show you the ```localhost``` URL to visit where Hugo says the site is running. And you should see this:

![test.com default page](/img/test.com_default_page.png)

This is the default home page of the PaperMod theme.

### Step 4: Add pages
To add pages.

Run the command:
```
hugo new posts/html.md
```

This creates a new folder called `posts` and a new markdown file called `html.md`.

The file should have a title, date and draft status.

Change the draft status to false or delete that line.

Copy the text below into the `html.md` file for test purposes.

---

> Hypertext Markup Language (HTML) is the standard markup language for documents designed to be displayed in a web browser. It can be assisted by technologies such as Cascading Style Sheets (CSS) and scripting languages such as JavaScript.

---

Run code below the command line:

```
hugo server -D
```

The `-D` flag, tells the server to also show drafts, because our html post has draft set to true. You can set draft to false, or remove that line entirely. Then you do not need the 
`-D` flag anymore. 

> Note that drafts will not be added to the site when we build it later.

You should see the first post.

![test.com draft post](/img/test.com_draft_post.png)

### Step 5: Build Hugo site
Before you deploy the site, after making changes. You have to build it. This will convert the markdown files into HTML and also creat indexes for the categories. We achieve this by running:

```
hugo
```

In the the base folder of the Hugo website.

At this point we are ready to move our blog to Github Pages.

### Step 6: Set up repository

Firstly, in the `config.yml` file, add this line ```publishDir: "docs"``` after ```theme: PaperMod```. This tells Hugo to publish our site into the directory called `docs`, when we build.

Then run:

```
hugo -t PaperMod
```
The PaperMod is the name of our theme, if you are using another theme. You would type the name of your theme in its' place.

This code builds our site into the `docs` folder.

Visit your github profile and create a new repository called test.com.

From our test.com directory on our local machine.
Run the following code:

```
git remote add origin <repository URL>
git add .
git commit -m "Initial Commit"
git push origin master
```

This code adds a remote url to our repository, adds and commits the code. Then finally pushes it to our remote repository.

### Step 6: Github Pages

From the new repository, click on settings then scroll down to the Github Pages section.

For `source`, select branch as master and change ```/(root)``` to ```docs```, then save.

After saving, scroll down back to the Github Pages section, then copy the new URL that Github has provided. That is where our website is located.

We would copy this URL into the local `config.yml` file.

In the `config.yml` file replace the value for `baseURL` with the github page link you just copied.

Then run the following code in the command line:

```
git add .
git commit -m "Updated baseURL"
git push origin master
```

This updates the remote repository with the latest changes.

Now when you visit the Github Pages URL, you should see the site we just built.

That is how we connect Hugo to Github Pages.

The benefit is that you can write your posts locally in markdown, when you build the site and push to the remote repository, your website will be updated automatically.

If you would like to host your website on your own domain like this site. This [link](https://www.namecheap.com/support/knowledgebase/article.aspx/9645/2208/how-do-i-link-my-domain-to-github-pages/) provides instructions on how to achieve that with namecheap web host.