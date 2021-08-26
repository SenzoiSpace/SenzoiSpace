---
title: How to Install Apache2 On Ubuntu
author: Senzoi
date: 2021-08-25 16:20:00 +0800
categories: [Tutorial, Networking]
tags: [apache]
image:
  src: https://i.ibb.co/b2yXQk8/How-to-Install-Apache2-on-Ubuntu-Banner.png
  width: 1280
  height: 720
---

The Apache HTTP server is the most widely-used web server in the world. It provides many powerful features including dynamically loadable modules, robust media support, and extensive integration with other popular software.

In this post, I will show you how to install **Apache2** on Ubuntu.

---

## Step 1: Install Apache

Apache is available within Ubuntu’s default software repositories, making it possible to install it using conventional package management tools.

Run the the following command.
```terminal
sudo apt update
sudo apt install apache2
```

---

## Step 2: Adjust the Firewall

Before testing Apache, it’s necessary to modify the firewall settings to allow outside access to the default web ports. Assuming that you followed the instructions in the prerequisites, you should have a UFW firewall configured to restrict access to your server.

During installation, Apache registers itself with UFW to provide a few application profiles that can be used to enable or disable access to Apache through the firewall.

List the ufw application profiles by typing:
```terminal
sudo ufw app list
```
Output
```output
Available applications:
  Apache
  Apache Full
  Apache Secure
  CUPS
```

As indicated by the output, there are three profiles available for Apache:
- Apache: This profile opens only port 80 (normal, unencrypted web traffic)
- Apache Full: This profile opens both port 80 (normal, unencrypted web traffic) and port 443 (TLS/SSL encrypted traffic)
- Apache Secure: This profile opens only port 443 (TLS/SSL encrypted traffic)

It is recommended that you enable the most restrictive profile that will still allow the traffic you’ve configured. Since we haven’t configured SSL for our server yet in this guide, we will only need to allow traffic on port 80:
```terminal
sudo ufw allow 'Apache'
```
Output
```output
Rules updated
Rules updated (v6)
```

You can verify the change by typing:
```terminal
sudo ufw status
```
Output
```output
Status: active

To                         Action      From
--                         ------      ----
Apache                     ALLOW       Anywhere                  
22/tcp                     ALLOW       Anywhere                  
Apache (v6)                ALLOW       Anywhere (v6)             
22/tcp (v6)                ALLOW       Anywhere (v6)
```

**PS** If you have your `ufw` status inactive. Try the following command.
```terminal
sudo ufw allow ssh
sudo ufw enable
```
Output
```output
Rules updated
Rules updated (v6)
Firewall is active and enabled on system startup
```
As indicated by the output, the profile has been activated to allow access to the Apache web server.

---

## Step 3: Check your Web Server

At the end of the installation process, Ubuntu 20.04 starts Apache. The web server should already be up and running.

Check with the systemd init system to make sure the service is running by typing:

```terminal
sudo systemctl status apache2
```
Output
```output
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
     Active: active (running) since Wed 2021-08-25 13:02:43 WIB; 6min ago
       Docs: https://httpd.apache.org/docs/2.4/
    Process: 20029 ExecReload=/usr/sbin/apachectl graceful (code=exited, status>
   Main PID: 18605 (apache2)
      Tasks: 55 (limit: 9444)
     Memory: 6.6M
     CGroup: /system.slice/apache2.service
             ├─18605 /usr/sbin/apache2 -k start
             ├─20033 /usr/sbin/apache2 -k start
             └─20066 /usr/sbin/apache2 -k start
```

As confirmed by this output, the service has started successfully. However, the best way to test this is to request a page from Apache.

You can access the default Apache landing page to confirm that the software is running properly through your IP address. If you do not know your server’s IP address, you can get it a few different ways from the command line.

Try typing this at your server’s command prompt:
```terminal
hostname -I
```
Output
```output
10.0.2.15
```

You will get back a few addresses separated by spaces. You can try each in your web browser to determine if they work.

When you have your server’s IP address, enter it into your browser’s address bar:

```output
http://10.0.2.15/
```

**PS** Don't forget to change `10.0.2.15` into to your own IP Address.

You should see the default Ubuntu Apache web page:

![Apache2](https://i.ibb.co/fvHC4Nx/Screenshot-from-2021-08-25-13-17-23.png){:.ioda}

This page indicates that Apache is working correctly. It also includes some basic information about important Apache files and directory locations.

---

## Step 4: Manage the Apache Process

Now that you have your web server up and running, let’s go over some basic management commands using systemctl.

- To stop your web server, type:
```terminal
sudo systemctl stop apache2
```
- To start the web server when it is stopped, type:
```terminal
sudo systemctl start apache2
```
- To stop and then start the service again, type:
```terminal
sudo systemctl restart apache2
```
- If you are simply making configuration changes, Apache can often reload without dropping connections. To do this, use this command:
```terminal
sudo systemctl reload apache2
```
- By default, Apache is configured to start automatically when the server boots. If this is not what you want, disable this behavior by typing:
```terminal
sudo systemctl disable apache2
```
- To re-enable the service to start up at boot, type:
```terminal
sudo systemctl enable apache2
```
Apache should now start automatically when the server boots again.

---

## Step 5: Setting Up Virtual Hosts (Recommended)

When using the Apache web server, you can use virtual hosts (similar to server blocks in Nginx) to encapsulate configuration details and host more than one domain from a single server. We will set up a domain called `senzoispace`, but you should replace this with your own domain name. If you are setting up a domain name with DigitalOcean, please refer to our Networking Documentation.

Apache on Ubuntu 20.04 has one server block enabled by default that is configured to serve documents from the /var/www/html directory. While this works well for a single site, it can become unwieldy if you are hosting multiple sites. Instead of modifying /var/www/html, let’s create a directory structure within /var/www for a `senzoispace` site, leaving /var/www/html in place as the default directory to be served if a client request doesn’t match any other sites.

**PS** You can rename `senzoispace` as you want as long as there are no special character there.

Create the directory for `senzoispace` as follows:
```terminal
sudo mkdir /var/www/senzoispace
```

Next, assign ownership of the directory with the $USER environment variable:
```terminal
sudo chown -R $USER:$USER /var/www/senzoispace
```

The permissions of your web roots should be correct if you haven’t modified your umask value, which sets default file permissions. To ensure that your permissions are correct and allow the owner to read, write, and execute the files while granting only read and execute permissions to groups and others, you can input the following command:
```terminal
sudo chmod -R 755 /var/www/your_domain
```

Next, create a sample `index.html` page using `nano` or your favorite editor:
```terminal
sudo nano /var/www/your_domain/index.html
```

Inside, add the following sample HTML:
```html
<html>
    <head>
        <title>Welcome to Your_domain!</title>
    </head>
    <body>
        <h1>Success!  The your_domain virtual host is working!</h1>
    </body>
</html>
```
Save and close the file when you are finished.

In order for Apache to serve this content, it’s necessary to create a virtual host file with the correct directives. Instead of modifying the default configuration file located at `/etc/apache2/sites-available/000-default.conf` directly, let’s make a new one at `/etc/apache2/sites-available/your_domain.conf`: 
```terminal
sudo nano /etc/apache2/sites-available/your_domain.conf
```
Paste in the following configuration block, which is similar to the default, but updated for our new directory and domain name:
```nano
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    ServerName your_domain
    ServerAlias www.your_domain
    DocumentRoot /var/www/your_domain
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Notice that we’ve updated the DocumentRoot to our new directory and ServerAdmin to an email that the your_domain site administrator can access. We’ve also added two directives: ServerName, which establishes the base domain that should match for this virtual host definition, and ServerAlias, which defines further names that should match as if they were the base name.

Save and close the file when you are finished.

Let’s enable the file with the a2ensite tool: 
```terminal
sudo a2ensite your_domain.conf
```

Disable the default site defined in `000-default.conf`:
```terminal
sudo a2dissite 000-default.conf
```

Next, let’s test for configuration errors: 
```terminal
sudo apache2ctl configtest
```
Output
```output
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Set the 'ServerName' directive globally to suppress this message
Syntax OK
```
**PS** As long as the `Syntax` is **OK**, we good to continue.

Restart Apache to implement your changes:
```terminal
sudo systemctl restart apache2
```

Apache should now be serving your domain name. You can test this by navigating to `http://10.0.2.15/`(Change it to your own IP), where you should see something like this:

![New Apache2](https://i.ibb.co/zXk7Wqp/Screenshot-from-2021-08-25-13-43-41.png){:.ioda}

---

## Step 6: Getting Familiar with Important Apache Files and Directories

Now that you know how to manage the Apache service itself, you should take a few minutes to familiarize yourself with a few important directories and files.

**Content**
- `/var/www/html`: The actual web content, which by default only consists of the default Apache page you saw earlier, is served out of the /var/www/html directory. This can be changed by altering Apache configuration files.

**Server Configuration**
- `/etc/apache2`: The Apache configuration directory. All of the Apache configuration files reside here.
- `/etc/apache2/apache2.conf`: The main Apache configuration file. This can be modified to make changes to the Apache global configuration. This file is responsible for loading many of the other files in the configuration directory.
- `/etc/apache2/ports.conf`: This file specifies the ports that Apache will listen on. By default, Apache listens on port 80 and additionally listens on port 443 when a module providing SSL capabilities is enabled.
- `/etc/apache2/sites-available/`: The directory where per-site virtual hosts can be stored. Apache will not use the configuration files found in this directory unless they are linked to the sites-enabled directory. Typically, all server block configuration is done in this directory, and then enabled by linking to the other directory with the a2ensite command.
- `/etc/apache2/sites-enabled/`: The directory where enabled per-site virtual hosts are stored. Typically, these are created by linking to configuration files found in the sites-available directory with the a2ensite. Apache reads the configuration files and links found in this directory when it starts or reloads to compile a complete configuration.
- `/etc/apache2/conf-available/`, `/etc/apache2/conf-enabled/`: These directories have the same relationship as the sites-available and sites-enabled directories, but are used to store configuration fragments that do not belong in a virtual host. Files in the conf-available directory can be enabled with the a2enconf command and disabled with the a2disconf command.
- `/etc/apache2/mods-available/`, `/etc/apache2/mods-enabled/`: These directories contain the available and enabled modules, respectively. Files ending in .load contain fragments to load specific modules, while files ending in .conf contain the configuration for those modules. Modules can be enabled and disabled using the a2enmod and a2dismod command.

**Server Logs**
- `/var/log/apache2/access.log`: By default, every request to your web server is recorded in this log file unless Apache is configured to do otherwise.
- `/var/log/apache2/error.log`: By default, all errors are recorded in this file. The LogLevel directive in the Apache configuration specifies how much detail the error logs will contain.
