---
title: Creating a Github Page
date: 2020-06-06 16:00:00 -0500
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
To publish a website using Git commands.

1. Initiate Git:
```
git init
```
2. Add changes:
```
git add .
```
3. Commit changes:
```
git commit -m "any description of the change"
```
4. Connect with the remote repo:
```
git remote add origin [url of your repo]
```
5. Push to remote:
```
git push -u origin master
```
6. Open the **Settings** of the repo, in **GitHub Pages** -> **Source** section, select **master branch**.
7. Wait a few seconds, you will see "Your site is published at https://username.github.io/repo_name/". Click this url, you will see your website is published.

**Special notes for the [jekyll-theme-chirpy]**: 
Before using git commands, [jekyll-theme-chirpy] needs to build categories and tags, so run this first:
```
bash tools/run.sh
```
Then you will see a folder '.container'. Copy everything in this folder and replace files in the root directory. Finally, go for git step 1 as shown above.

The author did provide some tools (in /tools) helping to skip this copy-paste step, but they were not working very well in my end, so I choose to just use copy and paste.

[jekyll-theme-chirpy]:(https://github.com/cotes2020/jekyll-theme-chirpy) 

## Other tips
If permission is needed for the folder:
```
sudo chown -R $USER ~/folder_name
```

