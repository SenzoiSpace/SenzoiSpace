---
title: How to Install Jekyll & Bundler & Gem on Ubuntu
author: Senzoi
date: 2021-08-25 12:10:00 +0800
categories: [Tutorial, Web Development]
tags: [jekyll, bundler, gem]
---

In this post I will show you how to install **Jekyll** & **Bundler** & **Gem** on **Ubuntu**.

---

Install **Ruby** and other [prerequisites](https://jekyllrb.com/docs/installation/#requirements).

```terminal
sudo apt-get install ruby-full build-essential zlib1g-dev
```

---

**PS**
Avoid installing RubyGems packages (called gems) as the **root** user. Instead, set up a gem installation directory for your user account. The following commands will add environment variables to your `~/.bashrc` file to configure the gem installation path:

```terminal
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

---

Last step, install **Jekyll** and **Bundler**:

```terminal
gem install jekyll bundler
```

---

That’s it! You’re ready to start using **Jekyll**.