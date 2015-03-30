---
title: "Jekyll: The blog maker"
layout: post
date: 2015-04-03   
tags: 
- jekyll
- guide
categories: 
- jekyll
- guide
levelup: 5
permalink: /:title/
---
As you may have figured out, I use Jekyll to create my Github Pages. And boy did I have a hard time understanding what the heck was going on. I dunno, maybe docs and I just don't get along. Most of the how-to guides out there were like "oh yeah rip off this template from this site and start using jekyll!" but that feels like a cop-out to me, and I like to bash my head into the wall. Or keyboard.

![Bash your head in](http://i1.kym-cdn.com/photos/images/masonry/000/021/531/SmashingHeadOnKeyboard.gif)

Pretty much.

Anyway, here's what I've learned thus far with using Jekyll and Github Pages. Maybe this will help you out, too. I hope to expand my knowledge.  Glad that it's written in Ruby, so implementation of things are much easier to understand (for me).

### > Github Pages
First issue I did not realize, and maybe I should have actually _read_ the instructions better,... there is a HUGE difference between creating a Github Page for a repo and for your account. I wanted to create one for my account but made it for my repo instead. Whoops.

Github [explains this fairly well](https://pages.github.com/), but here's my version for one-stop shopping goodness. I assume you're a developer and are knowledgable about the standard `git` type of commands.

On your local machine, install Jekyll.

{% highlight bash %}
$ gem install jekyll
{% endhighlight %}

#### > Github Pages for your github account
Create a new repo in Github called "your-github-username.github.io". In my case it's a repo called "rbatta.github.io" since my Github username is rbatta. When you git clone to your local machine, your `master` branch will be the one Github uses to publish your site. You get that? _Master branch is what Github uses to publish your site._ Git clone your new repo and hop into that directory.

_Note: If you create a repo and name it whatever you want and NOT "your-username.github.io", Github will see this as a Page for your Repo. When this happens, Github will use the branch called `gh-pages` instead of `master` to auto-publish your site. It's confusing and tripped me up massively in the beginning._

{% highlight bash %}
$ git clone git@github.com/your-github-username/your-github-username.github.io.git
$ cd your-github-username.github.io
{% endhighlight %}

For sanity's sake, create a branch that you can work out of instead. I will call this the `working-branch`. This will come in handy later. Trust me.

{% highlight bash %}
$ git checkout -b working-branch
{% endhighlight %}

Great! Now I realize this is an empty directory. It's cool. Time to install Jekyll.

#### > Jekyll setup
Create a new Jekyll site by running `jekyll new .` Make sure you've got that period in there, since that creates it within this directory.

{% highlight bash %}$ jekyll new .{% endhighlight %}

Jekyll will install a bunch of directories with _'s in them along with some other files, including a `_config.yml` file. This file will be the driver of just about everything: name it here and use it everywhere!

Create 2 directories called `_plugins` and `_drafts`. Because you may want/need them.

{% highlight bash %}$ mkdir _plugins _drafts{% endhighlight %}

#### > _config.yml
Fill in the information given. For `baseurl:` keep that as blank quotes, so that when you serve up your site, it'll load at `127.0.0.1:4000` instead of `127.0.0.1/baseurl-you-gave-it`.

If you change any of the variables, make sure you change them in the css and anywhere else in the other files.

Add in `future: false` if you'd like for Github Pages/Jekyll to honor your publish dates set in your posts. (If you put a future date, then the post won't show up until that date.)

#### > _posts/ and _drafts/
By default, anything that you put in the _posts directory will be published. Of course you need to make sure it's in the format of `yyyy-mm-dd-post-name` with whatever extension you use. I prefer markdown so all my posts end with `.md`. You're the author of your blogposts so choose whatever format you'd like: Textile or Markdown.

The _drafts folder is exactly that, for drafts. You don't need to append dates to it. In fact, when you're ready to publish it, that's when you can append dates. For that, we've got a handy tool, the [jekyll-rake-boilerplate Rakefile](https://github.com/gummesson/jekyll-rake-boilerplate). Copy that Rakefile into your repo. Now you've got some added jekyll capabilities like: `rake watch["drafts"]` which is the equivalent to `jekyll serve --watch --drafts`, and `rake draft["your post's title"]` which creates a new draft post with the given title. Sweetness. `rake publish` let's you pick the draft you want to publish and appends the date automagically to your post moving it from _drafts to _posts and eliminating the need to wonder what day it currently is.

To use the `rake draft["title"]` task, create a file called `_post.md` in the root directory. In your `_config.yml` add in the following so your rake task won't break when it's run:

    post:
      template: _post.md
      extension: md

#### > _plugins 
There are some cool plugins for added functionality for Jekyll. However when publishing to Github, that's stripped away and is not built when the site is published. Something something safety precautions. Ehhh. This is why we created a separate branch. The `working-branch` branch has all your drafts and posts and plugins and directories, but we will be creating the 'static' website and pushing that to the `master` branch to deploy to Github to publish. Wow, that came off a bit more complicated than I expected. Here's the workflow.

Start on working-branch {% icon fa-angle-right %} create draft {% icon fa-angle-right %} rake publish draft {% icon fa-angle-right %} jekyll build site {% icon fa-angle-right %} switch to master and merge/rebase {% icon fa-angle-right %} cleanup files {% icon fa-angle-right %} push to github {% icon fa-angle-right %} ??? {% icon fa-angle-right %} profit!

Let's start by adding in my favourite plugin, the [jekyll-font-awesome](https://gist.github.com/23maverick23/8532525) plugin. Create a file in `_plugins/` called `font_awesome.rb` and paste the contents from that gist into this file. If you're not familiar with fontawesome.io, it's a bunch of rad icons for everything. Paste the link below into your head.html file at around line 6: `_includes/head.html`, just like that.

      <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/font-awesome/4.3.0/css/font-awesome.min.css">

Now you can add icons by using the syntax `{.% icon fa-something %}` (without the period in there).

Once your blog is looking good to go and you're ready to publish it to Github for the whole wide world to see, you've got to do a couple of things. The method I've found that makes the most sense to me is this:

1. Git add, git commit.
2. Switch branch to master and merge working-branches to master then delete the branch.
3. Create a new branch, then switch back to master.
4. Build the site with Jekyll.
5. Delete everything in the directory EXCEPT your _site dir. 
6. Move the contents of the _site dir into root.
7. Delete _site
8. Git add, commit, and push.

Yeah I admit the whole git branch thing is weird, but since I'm merging, the working-branch branch is going to be the same as master, and with all the edits, that'll get weird, fast. Basically every time I merge to master, I'm at a state where I'm ready to publish, so I create a new branch to keep my drafts folders and my Rakefile and the like. I'm sure there's an easier way like pushing up to github and pulling down....

Anyway, here are the commands. After that I'll show my automation commands.

{% highlight bash %}
$ git add .
$ git commit -m "you know, some kinda commit message here about your new awesome post"
$ git checkout master
$ git merge working-branch
# you will probably have a ton of conflicting merges if it's not your 1st time
# fix them up with your editor. usually it's to say that files that used to exist
# no longer exist or were modified. no biggie.
# commit any changes after the merge.
$ git branch -D working-branch   # this deletes
$ git checkout -b working-branch # and this recreates
$ git checkout master
$ jekyll build
$ find . \( ! -path "./_site/*" ! -path "./.git/*" \) -delete
$ mv _site/ .
$ rm -rf _site
$ git add .
$ git commit -m "your awesome commit message before pushing to github"
$ git push origin master
{% endhighlight %}

Now check out your page, which should have been published by Github! It'll be at http://your-github-username.github.io!

{% icon fa-angle-double-up %} Level up +5

***

Questions? Comments? Hit me up at [risaonrails {% icon fa-paper-plane-o %}][email]!

[email]: mailto:risaonrails@gmail.com