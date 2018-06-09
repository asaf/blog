---
tags:
- blogging
- octopress
comments: true
date: 2013-07-08T00:00:00Z
title: 'Blogging with Octopress: Add About Page'
url: /2013/07/08/blogging-with-octopress-add-about-page/
---

Adding About Me page, probably one of the first thing to do, right?

*Note: If you just want to say few words about yourself on the aside panel, skip to _About Me on the aside panel_*

### Adding About Page

Create the page:

``` bash
rake new_page["about"]
```

rake creates a new page located in _source/about/index.markdown_


I edited mine to look like:

``` yaml
---
layout: page
title: "About Me"
comments: true
sharing: true
footer: true
---
```

Note that I removed the _date_ line because it doesn't make sense in this page.


#### Add About page in the top menu

You probably want to add the _About_ page in the top menu, right? 

``` bash
vi source/_includes/custom/navigation.html

#add the line to the bottom of the page:
<li><a href="{{ root_url }}/about">About</a></li>
```

### About on the aside panel

If u want to put some words about yourself on the side panel panel, do this:

``` bash
vi source/_includes/custom/asides/about.html

<h1>your name<h1/>
<p>some words about you, not longer than a line.</p>
```

Then enable the about html in the aside bar by:


``` bash
vi _config.yml

default_asides: [custom/asides/about.html, OTHER STUFF] # leave all other pages as is
```



Now the world knows about who you are :-)