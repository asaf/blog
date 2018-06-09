---
tags:
- blog
- blogging
- octopress
comments: true
date: 2013-07-08T00:00:00Z
title: 'Blogging with Octopress: Add Comments'
url: /2013/07/08/blogging-with-octopress-add-comments/
---

If you want your blog to support comments, follow this:


Octopress has a native 3rd party plugin for Disqus that will host all your blog's comments.


Visit [http://www.disqus.com](Disqus) web site and sign in,
Then [https://disqus.com/admin/signup/](Signup for a new application).

Here's example how I file the signup form my blog which is hosted in GitHub pages.

```
Site Url: asaf.github.io
Site Name: Asaf Shakarchi's Blog
Site Shortname: asafshakarchisblog
```

*Remember the site short name, you need it soon!*


Then configure _Octopress_ to be aware of your new _Disqus_ application:

``` bash
cd octopress #or wherever your octopress folder is located
vi _config.yml

#then change discqus_short_name line:
disqus_short_name: asafshakarchisblog #replace with the short name you've put in the app signup form
```



Done!