---
title: Handling Citibike Data
date: 2020-06-07 16:00:00 -0500
categories: [Tech Blog, Operation Demo]
tags: [github page]
---

## Use a Jekyll theme
To use an open source Jekyll theme available on github.com:
1. Fork that repo to your own account (you may not need to fork it to have it works, but to give credits to the author, I highly recommend to do so).
For example: [jekyll-theme-chirpy].
2. Clone the repo to your local disk:
```
git clone https://github.com/username/repo_name
```
3. Bundle the website:
```
bundle install
```
Some Jekyll themes specify versions of dependencies in the Gemfile. If any version related problem happens, check the Gemfile.
4. Serve the website. Most of the time, we can just run:
```
bundle exec jekyll serve
```
But be sure to read the manual of the Jekyll theme you are using. For example, in [jekyll-theme-chirpy], the author provides a bash file to build extra widgets and serve the website. For this case, run:
```
bash tools/run.sh
```
5. Open http://localhost:4000/ to see the website.

## Publish a website on Github
To publish a website,

1. Navigate to 
2. 
[jekyll-theme-chirpy]: 

