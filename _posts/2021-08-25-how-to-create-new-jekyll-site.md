---
title: How to Create New Jekyll Site
author: Senzoi
date: 2021-08-25 14:10:00 +0800
categories: [Tutorial, Web Development]
tags: [jekyll]
---

In this post I will show you how to create new **Jekyll** site.

---

1. First you need to install all prerequisites and other, [tutorial here](how-to-install-jekyll-and-bundler-and-gem-on-ubuntu.html).
2. Create a new **Jekyll** site at `./starter`. 
```terminal
jekyll new starter
```
3. Change into your new directory.
```terminal
cd starter
```
4. Build the site and make it available on a local server.
```terminal
bundle install
bundle exec jekyll serve
```
Example Output:
```terminal
Configuration file: /home/xxxxx/Code/xxxxxxxxx/_config.yml
            Source: /home/xxxxx/Code/xxxxxxxxx
       Destination: /home/xxxxx/Code/xxxxxxxxx/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
       Jekyll Feed: Generating feed for posts
                    done in 0.24 seconds.
/var/lib/gems/2.7.0/gems/pathutil-0.16.2/lib/pathutil.rb:502: warning: Using the last argument as keyword parameters is deprecated
 Auto-regeneration: enabled for '/home/xxxxx/Code/xxxxxxxxx'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```
5. Browse the `Server address` to your browser.
```
http://127.0.0.1:4000/
```
`ctrl+c` to stop **Jekyll** from running.

---

Thanks for comming here.