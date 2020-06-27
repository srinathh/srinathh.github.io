---
layout: post
title: "Migrating a blog to Jekyll"
tags: jekyll blog tips
author: "Hariharan Srinath"
---

In this post, I capture my learnings from migrating [my blog](https://srinathh.github.io) to [Jekyll](https://jekyllrb.com/)
from [Hugo](https://gohugo.io/). My main motivation was to get a lower friction writing experience that will hopefully
help me re-start blogging. Since Github Pages automatically rebuilds
[Jekyll Websites](https://help.github.com/en/github/working-with-github-pages/setting-up-a-github-pages-site-with-jekyll)
with every commit, new posts can be even be written directly on Github website rather than needing a development environment.

## Keeping old page link structures
When migrating blogs, we typically want to retain existing page addresses so that any incoming links and search results 
do not get disrupted. Since Jekyll's site structure may not correspond to the old blogging system, the easiest
way to ensure the pages retain the same address is to add a `permalink` in the post front-matter to hardcode the old
links. This way while moving the old blog posts to the standard Jekyll `_posts` folder, the post address remains the same.

```
---
layout: post
title: "ArdGrafix6100: Fast Arduino driver for Nokia 6100 Color LCDs"
tags: programming coding embedded arduiono
permalink: /opensource/ardgrafix6100/
author: "Hariharan Srinath"
---
```
