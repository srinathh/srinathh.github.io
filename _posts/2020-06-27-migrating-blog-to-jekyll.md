---
layout: post
title: "Migrating a blog to Jekyll"
tags: jekyll blog tips
author: "Hariharan Srinath"
---

In this article, I share tips for migrating a blog to [Jekyll](https://jekyllrb.com/) from other 
platforms like [Hugo](https://gohugo.io/) which I was previously using. My main motivation to shift 
was to get a lower friction writing experience and hopefully re-start blogging more frequently. Since Github Pages automatically 
[rebuilds Jekyll Websites](https://help.github.com/en/github/working-with-github-pages/setting-up-a-github-pages-site-with-jekyll)
with every commit, writing a new post can be as simple as creating a new file in the `_posts` folder on
Github website rather than requiring a develpoment environment.

This post assumes you are already familiar with the basics of [Jekyll](https://jekyllrb.com/) and have gone through the 
[tutorial](https://help.github.com/en/github/working-with-github-pages/setting-up-a-github-pages-site-with-jekyll)
provided by Github to setup Github pages with Jekyll. The tips here cover customization to make the blog migration 
work just the way you want.

## Keeping old post addresses when migrating
When migrating blogs, we typically want to retain existing post addresses so that any incoming links and search results 
do not get disrupted. Blog posts in Jekyll are typically placed under the `_posts` folder and have a filename of the 
form `yyyy-mm-dd-post-name.md` and appear on the web with the path `/year/month/date/post-file-name` which may not
correspond to how the pages appeared in the old blogging system.

The easiest way to ensure the old posts retain the same address as previously is to hard-code the old address using the
`permalink` setting in the post front-matter. For example here is the front-matter of my
[Nile color theme](/blog/nile-color-theme/) post. 

```yml
---
layout: post
title: "A color theme inspired by the beautiful scenary of the Nile riverbank in Egypt"
tags: design
permalink: /blog/nile-color-theme/
author: "Hariharan Srinath"
---
```

## Using non-standard themes 
Github Pages supports [a dozen or so themes](https://pages.github.com/themes/) out of the box which can be applied
using the [theme chooser](https://help.github.com/en/github/working-with-github-pages/adding-a-theme-to-your-github-pages-site-with-the-theme-chooser)
in the website. You can however also use any other theme hosted publically on Github by configuring 
the `remote_theme` option it in your `_config.yml` file.

Here's an example of how I use the latest version of the `minima` which supports a `dark` mode option instead
of the old stable version supported by `Github`. To use a `remote_theme`, point the setting to the
repository path under Github like below. In the case of [`minima`](https://github.com/jekyll/minima), this is
`jekyll/minima`

```yml
remote_theme: jekyll/minima
minima:
  skin: dark
```

## Overriding default colors and styles in `minima`
In Jekyll, the colors and styles of a website or a blog is defined in files in sub-folders like 
`_sass` and `assets/css`. When a Jekyll site uses themes, these folders are not present
in the user's site folder because they're pulled in automatically by Jekyll from the theme.

While this allows for a plug and play experience, the first thing we'd usually want to do after migrating
a blog is to customize its look and feel. We can do this by overriding the colors and styles settings 
in the these theme files. 

In Jekyll, a file at a particular in a particular relative path in the user site folder overrides any
corresponding file in the theme. So to override any setting, the best practice is:
1. Clone the theme repository down to your development machine
2. Identify the specifc file which contains the setting you want to override 
3. Copy over the file to your own site **in the same relative folder path** creating folders if necessary
4. Make the the change in the version of the file in your site folder

The latest version of the deault [`minima`](https://github.com/jekyll/minima), theme conveniently provides 
two files where [custom styles](https://github.com/jekyll/minima/blob/master/_sass/minima/custom-styles.scss) 
and [SASS variables](https://github.com/jekyll/minima/blob/master/_sass/minima/custom-variables.scss) can be 
defined to override the default settings without making changes in the theme files themselves.
- `/_sass/minima/custom-styles.scss`
- `/_sass/minima/custom-variables.scss`

To override, simply create a `/_sass/minima` folder, copy over these two files and start adding them in. Below are
examples of changes I have made to make my site title bigger, bolder and change its color.

### `custom-styles.scss`
```scss
// Placeholder to allow defining custom styles that override everything else.
// (Use `_sass/minima/custom-variables.scss` to override variable defaults)

.site-title {
  @include relative-font-size(2.5);
  font-weight: 500;
}
```

### `custom-variables.scss`
```scss
// Placeholder to allow overriding predefined variables smoothly.

$site-title-color: #e9ff59;  
```
## Overriding the layout of pages and posts
Overriding the layout of pages and posts is simliar to overriding styles. Layouts are contained in `_include`
and `_layout` folders in the theme and to override, we copy over the corresponding file to our site folder
and make the change. The final layout applied to a page is often composed of a hierarchy of 
layouts and partials - so it may take a bit of detective work to determine exactly where to make a change.
Reading through the [documentation](https://jekyllrb.com/docs/) is highly recommended.

Here is an example of a change I have made in the `_layouts/post.html` file where I add a `DRAFT` word
before a post title if the page status is draft. The is expressed using an if statement
from the [Liquid](https://shopify.github.io/liquid/) templating language. The statement checks if the 
`page.draft` variable is set and if so, appends the word `DRAFT`to the title. 

```html
---
layout: default
---
<article class="post h-entry" itemscope itemtype="http://schema.org/BlogPosting">

  <header class="post-header">
    <h1 class="post-title p-name" itemprop="name headline">{% if page.draft %}DRAFT:&nbsp;{% endif %}{{ page.title | escape }}</h1>
```

## Conclusion
This article hopefully addresses some of the common questions that arise when migrating blogs to Jekyll and
covers customization of themes in a simple way. Please drop me an email at srinathh at gmail in case you have 
any coments or thoughts.