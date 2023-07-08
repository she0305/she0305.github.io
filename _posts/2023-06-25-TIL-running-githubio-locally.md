---
title: "Running Github.io Locally"
layout: splash
read_time: true
header:
  overlay_image: assets/images/githubIo.png
categories:
- TIL
last_modified_at: 2023-06-25
---

# Debugging Without Local Server Made Me Old 😭

For people like me, who don't want to pay for hosting, Github.io can be a great alternative. It's free and easy to use. However, it can be a bit tricky to set up. Especially if you are trying to customize your page.


I struggled to figure out how to run Github.io locally, and not surprisingly, I ended up wasting so much time. I had to push my changes to Github and check if it worked every time I made a change. (Are you surprised yet on my patience 🫠? Cause I am lol.) So, I decided to write a blog post about it so that I can save someone's time and energy.

Unfortunately, this blog is not about explaining why I love Github.io or how to setup a customized blog. As I will be simply sharing the steps and commends that allows to run local server for Github.io, if you are curious about what Github.io is, you can check out [this link](https://pages.github.com/).

First, you need to install github-pages gem and jekyll.
```bash
gem install github-pages
```

then you can go ahead build the site locally. You will see `_site` folder in your directory, which converts your markdown files into html files.
```bash
jekyll build
```

lastly, you can run the local server. This will run build and serve the site locally.
```bash
jekyll serve
```

Credit to [this blog](https://kbroman.org/simple_site/pages/local_test.html) for helping me to figure out how to run Github.io locally.
