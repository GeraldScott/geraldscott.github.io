---
layout: post
title:  "Changing GitHub Pages theme"
date:   2019-04-08 16:15:21 +0200
categories: jekyll githubpages
---
After migrating Blogger to GitHub Pages, I wanted to try one of the [supported themes](https://pages.github.com/themes/) instead of using the default `Minima` theme.

According to the documentation, it should be easy enough: just edit `_config.yml` and replace `minima` with the name of the new theme.

Actually it was not so easy because the other themes have a more simplistic layout scheme than the Minima theme, so when you serve up the site, you end up with error messages about missing layouts. 

The end result is a blank page and lots of beating your head on the keyboard.

## Jekyll layouts
The default Jekyll theme `minima` has four built-in layouts that are used by different pages of the blog.

When you switch to one of the other GitHub Pages themes, the new theme might only have the `default.html` layout, so when you switch from Minima to Tactile or Cayman you will get a blank page because the other layouts are missing.

If you examine the source code of the [`minima`](https://github.com/jekyll/minima) theme, you will see the four layout files in the `_layouts` folder:
* default.html: the default base layout 
* home.html: layout for the home page
* page.html: layout for other pages such as About, Contacts etc
* post.html: layout for the blog posts

All you have to do is to copy the missing layout files from the theme into your own `_layouts` folder
```
.
├── _layouts
│   ├── home.html
│   ├── page.html
│   └── post.html
```
Don't copy the `default.html` file because that will prompt Jekyll to look for a whole lot of missing assets. Just copy the three missing files.

Now you can change the theme without triggering error messages, and you have the option of changing the layouts yourself.

