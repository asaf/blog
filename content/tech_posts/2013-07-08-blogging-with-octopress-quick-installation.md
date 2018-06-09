---
tags:
- octopress
- blog
- blogging
comments: true
date: 2013-07-08T00:00:00Z
title: 'Blogging with Octopress: Quick Installation'
url: /2013/07/08/blogging-with-octopress-quick-installation/
---

I really wanted to revive my blog (which was hosted in Blogger), but I wanted to have more control over it,
It just feels very natural for guys like me who uses vi more than GUI =)


Since Octopress docs are too long and frustrating (oh common, i just wanted a blog), I decided to dedicate few posts for it,
I hope I'll have the time to record (blog, post, whatever) all the cool things i'm gonna add to my blog in the future.

15 minutes and you have a pro blog, so there we go:

### Installing Ruby

####Ubuntu
``` bash 
sudo apt-get update
sudo apt-get install ruby 1.9.3
```

#### MacOS

``` bash
brew update
brew install rbenv
brew install ruby-build
rbenv install 1.9.3-p0
rbenv rehash
```

(If you don't have brew, visit: http://mxcl.github.io/homebrew)


### Installing Octopress

Now Ocotopress !

``` bash
cd ~ #or wherever u want the octopress folder to be located
git clone git://github.com/imathis/octopress.git octopress
cd octipress
gem install bundler
rbenv rehash #only if using rbenv
bundle install
```

Thats it!


### Deploying your blog in GitHub pages.

There are other alternatives, but I personally love GitHub, to deploy your blog in GitHub pages:

* [Create a GitHub repo](https://github.com/new) and name it _your_user.github.io_, where _your_user_ is your github username, mine is _asaf_,
so it'll be: _asaf.github.io_

* Lets tell Octopress about your github created repo (that'll hold your blog):
``` bash
rake setup_github_pages
```
When it asks for your git repo, paste: _git@github.com:your_user/your_user.github.io_, for example mine is: _git@github.com:asaf/asaf.github.io_

* Now lets deploy and see our new shiny blog!

``` bash
rake generate
rake deploy
```

This generates the blog site from the (default) sources, commit it via _git_ and deploy it in the created github repo.


Thats all!

now visit: http://your_user.github.io to see your blog!


### Minimal Configuration

And if we'r already here, lets quickly configure the minimal details:

``` bash
vi _config.yml
title: Blog title, I put my full name.
subtitle: What are you going to blog about?
author: Your full name
```

rake generate
rake deploy


But it looks gray and dark and missing some cool stuff, isn't it? follow the next blog about how to customize OctoPress =)