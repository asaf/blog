---
tags:
- blogging
- blog
- hugo
- octopress
date: "2016-08-23T22:43:26+03:00"
title: "Migrate Octopress to Hugo"

---
My blog was based on [Octopress](https://github.com/imathis/octopress) blogging system, based on [jekyll](https://github.com/jekyll/jekyll),
but I was never a ruby person, it also seems like Octopress is not so active lately,

In the other hand, Hugo feels super slim and simple, thanks to _Go_ and _Hugo_, it takes only few lines to start a blog,

You can download a binary package of Hugo, but I have Go installed so it's simply 1 command:

```bash
go get -u -v github.com/spf13/hugo
```

Creating a new blog is simply one command:

```bash
hugo new site myblog
```

This command creates the `myblog` folder with some initial data that serves as a baseline for our blog.


Adding a new post is simple as:

```bash
hugo new post/welcome.md
```

This generates the `content/post/welcome.md` file, a post is just a simple file, no magic!


A theme defines how the blog look like visually, there are many [themes](themes.gohugo.io), choose the one you like, in this post, I use the [purehugo](http://themes.gohugo.io/purehugo/) theme because it's clean and simple.

```bash
cd themes
git clone https://github.com/dplesca/purehugo.git
```

Lets star a local server to see our shining new blog:

```bash
hugo server --theme=hugo_theme_robust --buildDrafts
```


Hit in browser: http://localhost:1313

You should see the _welcome_ blog entry we just added.



## Migrate from Octopress


Hugo has a built in support to [migrate from jekyll](https://gohugo.io/commands/hugo_import_jekyll/)

```bash
cd my_octopress_blog
ls
config.yaml ... source/ ...
hugo import jekyll source/ hugo-import/
Congratulations! 26 post(s) imported!
...
```

Make sure to have _config.yaml_ and the _source_ folder in current directory, running the import command should import all files

Simply move the _posts_ folder into your new blog _content_ folder by:

```bash
mv hugo-import/content/post/* <location_of_new_hugo_blog>/content/post`
```


Happy blogging!