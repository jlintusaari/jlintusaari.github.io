---
layout: post
title:  "Making a GitHub hosted Jekyll blog with a custom theme"
date:   2018-03-28 22:37:55 +0300
categories: coding jekyll GitHub blog
published: true
---

I had been thinking of making a blog for a while.
I wanted a blogging platform that was simple, customizable,
static content based so that I could host it in [GitHub pages][github-pages],
and supported Markdown so that I could easily publish code projects from Jupyter notebooks and R's [knitr].
After a bit of searching I decided to go forward with [Jekyll] which is a Ruby based blogging platform that seems to have
the above features.
This blog post is the result of that project.


## Hosting in GitHub pages

[GitHub] is great. Not only does it provide a really streamlined platform for working with git repos, it also provides
free hosting for static content sites via it's [GitHub pages][github-pages].
What is neat with [GitHub pages][github-pages] for Jekyll is that [Jekyll] based blogs are automatically built, meaning
that the static content is automatically compiled from the source files, such as those Markdown files.
However, I ended up not using that neatness due to a missing gem (Ruby library) in the GitHub environment that my
custom theme was using.


## Developer environment

It's essential to be able to test your site in your local computer.
Because the site was to be hosted in GitHub, I needed to replicate the GitHub Ruby environment if I wanted
to use the automatic compilation of Jekyll based web-pages.
Luckily GitHub makes this really easy -- you just need to have 
`gem "github-pages", group: :jekyll_plugins` in your
`Gemfile` and you will have the same Ruby gems in your local environment that they are providing at GitHub.
The above assumes you already have a recent version of Ruby installed with [`bundler`][bundler]. 

### Themes

You can choose your blog theme from multiple [themes][gh-themes] or even use a custom themes via a
[`jekyll-remote-theme`][remote-theme] gem.
However, I had already chosen a [theme][whiteglass] that was not in that list and unfortunately used a
gem called `jekyll-archives` that was not available in the GitHub Ruby environment.
This caused the post tags to silently disappear from the posts (luckily I noticed it).
I figured the safest and easiest way forward at this stage would be to just build the pages locally, 
and push the compiled pages to GitHub (see [here][issue] for a Travis integration to automate the building).
That way I wouldn't have to worry about what's available in the GitHub environment and what's not.

So instead I added the required gems separately to the `Gemfile`. Below is the complete `Gemfile`:
```ruby
source "https://rubygems.org"

# Having only this line would replicate the GitHub gems to your environment
# gem "github-pages", group: :jekyll_plugins

# Jekyll gem
gem "jekyll", "~> 3.7.3"

# Whiteglass theme gems
gem "jekyll-whiteglass"
gem "jekyll-archives", group: :jekyll_plugins
gem "jekyll-paginate", group: :jekyll_plugins
gem "jekyll-sitemap", group: :jekyll_plugins
gem "jekyll-remote-theme", group: :jekyll_plugins
```

After installing with `bundle install` you can run the blog locally at `localhost:4000` with 
`bundle exec jekyll serve`.


## Hosting in GitHub

The last step is to have GitHub host the compiled pages. GitHub has made this really [simple][github-pages]. 
You only need to create a git repository with a special name `<username>.github.io` and all the static content you push 
there in the `master` branch will be available at `https://<username>.github.com`. 

A small nuisance in my case is that I need to manually compile the pages and then push the compiled pages to GitHub. 
For this I created a `source` branch that hosts the source files. From there I compile the static pages by running
`jekyll build` and then just copy the contents of the `_site` folder to the `master` branch and push.


[knitr]: https://yihui.name/knitr/
[GitHub]: https://github.com
[Jekyll]: https://jekyllrb.com/
[jekyll-in-github]: https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/
[github-pages]: https://pages.github.com/
[gh-themes]: https://pages.github.com/themes/
[whiteglass]: https://github.com/yous/whiteglass
[remote-theme]: https://github.com/benbalter/jekyll-remote-theme
[issue]: https://github.com/yous/whiteglass/issues/13
[bundler]: https://bundler.io/