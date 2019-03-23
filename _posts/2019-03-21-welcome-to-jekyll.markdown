---
layout: post
title:  "From Blogger to GitHub Pages using Centos 7"
date:   2019-03-21 11:07:21 +0200
categories: jekyll update
---
I have been using Google's Blogger platform to host this blog, but I decided to migrate it to [GitHub Pages](https://pages.github.com/) in case Blogger follows Google+ to the Great BitBucket in the Sky.

This post describes how to create and maintain a personal web site on GitHub Pages, and to migrate your Blogger content to the new site.

## GitHub Pages and Jekyll
GitHub Pages is a static website hosting service for personal, organizational, or project-specific pages that are driven directly from your GitHub repository. You can use either HTML or Markdown to create the pages. It is a static site hosting service so it doesn't support server-side code such as PHP, Ruby, or Python.

GitHub Pages can work with any static site generator, but for ease of use it is [tightly integrated](https://help.github.com/en/articles/about-github-pages-and-jekyll) with the popular static site generator [Jekyll](https://jekyllrb.com/). You feed it markdown text and it cranks out a static website.

Jekyll uses [kramdown](https://kramdown.gettalong.org/quickref.html) to parse a superset of Markdown syntax to generate the HTML pages.

## github.io
Follow the instructions on the GitHub Pages [website](https://pages.github.com/) to create a GitHub account if you don't already have one, then create a new repository called `<username>.github.io` where <*username*> is the name of your GitHub account. 

Clone the repository to your development environment so that you can work on it using your favourite tools, then push the changes back up to GitHub. After a moment or two, you will see the fruits of your labours by browsing to `https://<username>.github.io`.

You could continue to work on this simple local copy to build up a functionally rich website, but you will have to set up a Jekyll development environment to do things like migrate posts from Blogger. 

The next section describes how to do this and use Jekyll to generate the website.
## Jekyll development environment
### Install RVM and Ruby
Jekyll is a Ruby Gem that must be [installed](https://jekyllrb.com/docs/installation/) on your system, which in my case is a Centos 7 workstation. These instructions should work for other RPM-based distros like RHEL or Fedora.

The pre-requisites are Ruby version 2.4.0 or above, as well as GCC and Make.

Install the development tools group which includes GCC and Make:
```bash
$ sudo yum update -y
$ sudo yum install @development-tools
```
The version of Ruby in the Centos 7 repo is 2.0, so build the Ruby Version Manager [RVM](https://rvm.io/) and use it to install the latest version.

Get the GPG keys and install RVM:
```bash
$ sudo gpg2 --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
$ sudo \curl -sSL https://get.rvm.io | bash -s stable
```
For reasons best known to the RVM developers, their installation instructions prefix the curl command with a backslash to suppress any aliases for curl. It is not a typo. 

Add your Linux user to the RVM group so that it can be used as a non-root user:
```bash
$ sudo usermod -a -G rvm <devuser>
$ sudo getent group rvm
rvm:x:1001:<devuser>
```
where `<devuser>` is your Linux username.

Logout and login again to activate the group membership.

List all known rubies to find the latest version of Ruby:
```bash
$ rvm list known | less
```
Install the latest version (2.6 in this case) and check the version numbers:
```bash
$ rvm install ruby-2.6
    (be patient...)
$ rvm use 2.6 --default

$ ruby -v
ruby 2.6.0p0 (2018-12-25 revision 66547) [x86_64-linux]

$ gem -v
3.0.1
```
Now install Jekyll and the gem bundler:
```bash
$ gem install jekyll bundler
```
### Generate a Jekyll website
Use Jekyll to generate this website from scratch rather than rely on GitHub Pages to do the right thing, as follows:
```bash
$ jekyll new <username>.github.io
```
Fire it up:
```bash
$ cd <username>.github.io
$ bundle exec jekyll serve
```
Now browse to [http://localhost:4000](http://localhost:4000) to see your new site.

Edit [`_config.yml`](https://github.com/GeraldScott/geraldscott.github.io/blob/master/_config.yml) to change the blog title and other site-specific details. The inaugural post is in `_posts/yyyy-mm-dd-welcome-to-jekyll.markdown`. Edit it as you see fit.

### Publish to github.io
Before you publish the website, edit the [Gemfile](https://github.com/GeraldScott/geraldscott.github.io/blob/master/Gemfile) to delete the Jekyll gem and replace it with a gem that contains themes and GitHub Pages functionality by uncommenting the line `gem "github-pages", group: :jekyll_plugins`. Update the bundle:
```bash
$ vi Gemfile
$ bundle update
```
Initialize the current working directory as a Git repository:
```bash
$ git init
```
Stage and commit the files:
```bash
$ git commit -m "Initial commit"
```
If you have not already done so, create the repository `<username>.github.io`, then link your newly generated website to the repo by adding the URL of the project repo to the origin of the local git repo and push the contents to GitHub:
```bash
$ git remote add origin git@github.com:<username>/<username>.github.io.git
$ git push -u origin master --force
```
The `--force` option will overwrite the contents of the GitHub repo with the contents of the newly generated website, so be careful.
## Migrate Blogger posts
Migrating your Blogger posts is straighforward because Jekyll provides [importers](https://import.jekyllrb.com/docs/home/) to move from other blog platforms. 

Follow the [instructions](https://import.jekyllrb.com/docs/blogger/) to [create a backup file](https://support.google.com/blogger/answer/41387?visit_id=636889153776042945-750428619&rd=1) of your Blogger posts called `blog-MM-DD-YYYY.xml`. Import them into Jekyll:
```bash
$ ruby -r rubygems -e 'require "jekyll-import";
    JekyllImport::Importers::Blogger.run({
      "source"                => "/path/to/blog-MM-DD-YYYY.xml",
      "no-blogger-info"       => false, # not to leave blogger-URL info (id and old URL) in the front matter
      "replace-internal-link" => false, # replace internal links using the post_url liquid tag.
    })'
```
This will generate the posts in `_posts`.

Note that “Labels” will be included in export as “Tags”.

If you uploaded images to your Blogger posts, they will be visible as links to your original blog, so you will have to download them and insert them locally before you decommission the old blog.
 
