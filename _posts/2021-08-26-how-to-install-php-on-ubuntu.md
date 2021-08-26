---
title: How to Install PHP on Ubuntu
author: Senzoi
date: 2021-08-26 09:24:00 +0800
categories: [Tutorial, Web Development]
tags: [php]
---

PHP is one of the most used server-side programming languages. Many popular CMS and frameworks such as WordPress, Magento, and Laravel are written in PHP.

This guide covers the steps necessary to install PHP on latest Ubuntu (20.04) and integrate it with Nginx and Apache.

At the time of writing, the default latest Ubuntu (20.04) repositories include PHP 7.4 version. We’ll also show you how to install previous PHP versions. Before choosing which version of PHP to install, make sure that your applications support it.

---

## Method 1: Install PHP with Apache

If you’re using [Apache](/posts/how-to-install-apache2-on-ubuntu/) as your web server, run the following commands to install PHP and Apache PHP module:
```terminal
sudo apt update
sudo apt install php libapache2-mod-php
```

Once the packages are installed, [restart Apache](posts/how-to-install-apache2-on-ubuntu/#step-4-manage-the-apache-process) for the PHP module to get loaded:
```terminal
sudo systemctl restart apache2
```

---

## Method 2: Install PHP with Nginx

Unlike [Apache](/posts/how-to-install-apache2-on-ubuntu/), Nginx doesn’t have built-in support for processing PHP files. We’ll use PHP-FPM (“fastCGI process manager”) to handle the PHP files.

`I'm sorry but this method is on prod.`

---

## Install PHP extensions

PHP extensions are compiled libraries that extend the core functionality of PHP. Extensions are available as packages and can be easily installed with `apt`:
```terminal
sudo apt install php-[extname]
```

For example, to install json and curl extensions, you would run the following command:
```terminal
sudo apt install php-json php-curl
```

---

## Test PHP

To test whether the webserver is configured properly for PHP processing, create a new file named info.php inside the `/var/www/html` directory with the following code:

```terminal
sudo nano /var/www/html/info.php
```

Now write this following code:
```php
<?php

phpinfo();
```

Save the file, open your browser, and visit: `http://your_server_ip/info.php`.
> To check your server IP, use `hostname -I` in your terminal.

You’ll see information about your PHP configuration as shown on the image below:
![Info.php](https://i.ibb.co/YW2pp2b/Screenshot-from-2021-08-26-09-51-16.png){: width="1280" heigth:"720"}

> **PS** If you follow my tutorial installing Apache2, you may need to change the `html` in `/var/www/html` to your domain. In my case `/var/www/senzoispace`.

---

## Install Previous PHP Version

[Ondřej Surý](https://github.com/oerdnj), a Debian developer, maintains a repository that includes multiple PHP versions. To enable the repository , run:
```terminal
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
```

You can now install any PHP version you need by appending the version number to the package name:
```terminal
sudo apt install php[version]
```

For example, to install PHP 7.0 and few common PHP modules, you would run:
```terminal
sudo apt install php7.0 php7.0-common php7.0-opcache php7.0-mcrypt php7.0-cli php7.0-gd php7.0-curl php7.0-mysql
```
