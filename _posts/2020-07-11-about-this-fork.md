---
layout: post
title:  "Switching to a Modern Blog Theme"
author: srinath
tags: 
- Jekyll
image: assets/images/switching-themes/mediumish-jekyll-template.png
permalink: /jekyll/2020/07/11/about-this-fork.html
---

I recently switched my blog's theme from [minima](https://github.com/jekyll/minima) to
a [forked version](https://github.com/srinathh/mediumish-theme-jekyll) of 
the [Mediumish Jekyll theme](https://www.wowthemes.net/mediumish-free-jekyll-template/),
a fantastic full featured Jekyll website theme released by [Sal](https://www.wowthemes.net/).
This theme is based on [Bootstrap](https://getbootstrap.com/), is wonderfully responsive
and best of all, released under a permissive [MIT license](https://opensource.org/licenses/MIT).
Huge props to the author for releasing such a beautiful and functional theme with a permissive license.

The original theme has number of dependencies on third party services that could be 
liabilities in today's legal environment for simple personal blogs. Some features 
in the original are also in my opinion not suitable for simple static blogs 
that can be hosted on Github pages. So I have stripped out those components and made
some fixes for better scalability. You can find the forked theme [here](https://github.com/srinathh/mediumish-theme-jekyll).

### Key Changes
#### Third Party Dependencies removed
- Ad Sense
- Disqus
- Gravataar

#### Jekyll Plugins un-supportd by Github Pages removed
- jekyll-toc
- jekyll-archive

#### Other Changes
- Loading JQuery from offical CDN vs. serving loacally
- Removed lazy loading of styles & font
- Removed Lunr search - client side search acoss lots of posts inefficient
- Star Rating - not very useful without server backend
- Outdated social sharing URLs fixed, Reddit sharing added
- Added an Email button & added conditional check for Follow

Since this is not a gem based theme, to use it you need to manually clone the theme repository,
copy over your posts and make some manual changes to configuration. However, the result is 
highly worth the effort.