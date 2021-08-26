---
title: How to Install phpMyAdmin on Ubuntu
author: Senzoi
date: 2021-08-26 10:43:00 +0800
categories: [Tutorial, Database]
tags: [phpmyadmin]
---

While many users need the functionality of a database management system like MySQL, they may not feel comfortable interacting with the system solely from the MySQL prompt. phpMyAdmin was created so that users can interact with MySQL through a web interface.

In this post, I will show you how to install phpMyAdmin on Ubuntu.

---

## Prerequisites

In order to complete this guide, you will need:
- An Ubuntu server. This server should have a non-root user with administrative privileges and a firewall configured with ufw. To set this up, follow my tutorial about [install Apache2 and `ufw`](/posts/how-to-install-apache2-on-ubuntu), [MySQL](/posts/how-to-install-mysql-on-ubuntu) and [PHP](/posts/how-to-install-php-on-ubuntu).

Additionally, there are important security considerations when using software like phpMyAdmin, since it:
- Communicates directly with your MySQL installation
- Handles authentication using MySQL credentials
- Executes and returns results for arbitrary SQL queries

For these reasons, and because it is a widely-deployed PHP application which is frequently targeted for attack, you should never run phpMyAdmin on remote systems over a plain HTTP connection.

If you do not have an existing domain configured with an SSL/TLS certificate, you can follow this guide on securing Apache with Let’s Encrypt on Ubuntu 20.04. This will require you to register a domain name, create DNS records for your server, and set up an Apache Virtual Host.

---

## Step 1: Install phpMyAdmin

You can use APT to install phpMyAdmin from the default Ubuntu repositories.

As your non-root sudo user, update your server’s package index:
```terminal
sudo apt update
```

Following that you can install the phpmyadmin package. Along with this package, the [official documentation](https://docs.phpmyadmin.net/en/latest/require.html) also recommends that you install a few PHP extensions onto your server to enable certain functionalities and improve performance.

If you followed the prerequisite LAMP stack tutorial, several of these modules will have been installed along with the php package. However, it’s recommended that you also install these packages:
- `php-mbstring`: A module for managing non-ASCII strings and convert strings to different encodings
- `php-zip`: This extension supports uploading .zip files to phpMyAdmin
- `php-gd`: Enables support for the GD Graphics Library
- `php-json`: Provides PHP with support for JSON serialization
- `php-curl`: Allows PHP to interact with different kinds of servers using different protocols

Run the following command to install these packages onto your system. Please note, though, that the installation process requires you to make some choices to configure phpMyAdmin correctly. We’ll walk through these options shortly:
```terminal
sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl
```

Here are the options you should choose when prompted in order to configure your installation correctly:
- For the server selection, choose apache2 
> Warning: When the prompt appears, “apache2” is highlighted, but not selected. If you do not hit `SPACE` to select Apache, the installer will not move the necessary files during installation. Hit `SPACE`, `TAB`, and then `ENTER` to select Apache.
- Select `Yes` when asked whether to use `dbconfig-common` to set up the database
- You will then be asked to choose and confirm a MySQL application password for phpMyAdmin

> **PS**: Assuming you installed MySQL by [following](/posts/how-to-install-mysql-on-ubuntu) my step, you may have decided to enable the Validate Password plugin. As of this writing, enabling this component will trigger an error when you attempt to set a password for the phpmyadmin user:
![error](https://i.ibb.co/gFgVsDH/Screenshot-from-2021-08-26-10-17-06.png){: width="1280" height="720"}
To resolve this, select the abort option to stop the installation process. Then, open up your MySQL prompt:
```terminal
sudo mysql
```
Or, if you enabled password authentication for the root MySQL user, run this command and then enter your password when prompted:
```terminal
mysql -u root -p
```
From the prompt, run the following command to disable the Validate Password component. Note that this won’t actually uninstall it, but just stop the component from being loaded on your MySQL server:
```console
UNINSTALL COMPONENT "file://component_validate_password";
```
Output
```console
Query OK, 0 rows affected (0,00 sec)
```
Following that, you can close the MySQL client:
```console
exit
```
Then try installing the phpmyadmin package again and it will work as expected:
```terminal
sudo apt install phpmyadmin
```
Once phpMyAdmin is installed, you can open the MySQL prompt once again with sudo mysql or mysql -u root -p and then run the following command to re-enable the Validate Password component:
```console
INSTALL COMPONENT "file://component_validate_password";
```
Output
```console
Query OK, 0 rows affected (0,01 sec)
```

The installation process adds the phpMyAdmin Apache configuration file into the `/etc/apache2/conf-enabled/` directory, where it is read automatically. To finish configuring Apache and PHP to work with phpMyAdmin, the only remaining task in this section of the tutorial is to is explicitly enable the `mbstring` PHP extension, which you can do by typing:
```terminal
sudo phpenmod mbstring
```
Afterwards, restart Apache for your changes to be recognized:
```terminal
sudo systemctl restart apache2
```

phpMyAdmin is now installed and configured to work with Apache. However, before you can log in and begin interacting with your MySQL databases, you will need to ensure that your MySQL users have the privileges required for interacting with the program.

---

## Step 2: Adjust User Authentication and Previleges

In this step, you can do it on my previous post about [how to install MySQL on Ubuntu](/posts/how-to-install-mysql-on-ubuntu/#step-3-create-a-dedicated-mysql-user-and-granting-privileges).

Now access the web interface by visiting your server’s domain name or public IP address followed by `/phpmyadmin`:

```console
https://`your_domain_or_IP`/phpmyadmin
```

> To see `your_domain_or_IP` just type `hostname -I` in your terminal.

![phpMyAdmin](https://i.ibb.co/BjCm9qg/Screenshot-from-2021-08-26-10-36-03.png){: width="972" height="589" }

Log in to the interface, either as root or with the new username and password you just configured.

When you log in, you’ll see the user interface, which will look something like this:
![phpMyAdmin dashboard](https://i.ibb.co/yQcZSMG/Screenshot-from-2021-08-26-10-38-55.png){: width="972" height="589" }

Now that you’re able to connect and interact with phpMyAdmin, all that’s left to do is harden your system’s security to protect it from attackers.

---

## Step 3: Secure Your phpMyAdmin Instance

ecause of its ubiquity, phpMyAdmin is a popular target for attackers, and you should take extra care to prevent unauthorized access. One way of doing this is to place a gateway in front of the entire application by using Apache’s built-in `.htaccess` authentication and authorization functionalities.

To do this, you must first enable the use of `.htaccess` file overrides by editing your phpMyAdmin installation’s Apache configuration file.

Use your preferred text editor to edit the `phpmyadmin.conf` file that has been placed in your Apache configuration directory. Here, we’ll use `nano`:
```terminal
sudo nano /etc/apache2/conf-available/phpmyadmin.conf
```

Add an AllowOverride All directive within the `<Directory /usr/share/phpmyadmin>` section of the configuration file, like this:
```conf
<Directory /usr/share/phpmyadmin>
    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php
    AllowOverride All
    . . .
```

When you have added this line, save and close the file. If you used nano to edit the file, do so by pressing `CTRL + X`, `Y`, and then `ENTER`.

To implement the changes you made, restart Apache:
```terminal
sudo systemctl restart apache2
```

Now that you have enabled the use of `.htaccess` files for your application, you need to create one to actually implement some security.

In order for this to be successful, the file must be created within the application directory. You can create the necessary file and open it in your text editor with root privileges by typing:
```terminal
sudo nano /usr/share/phpmyadmin/.htaccess
```
Within this file, enter the following information:
```htaccess
AuthType Basic
AuthName "Restricted Files"
AuthUserFile /etc/phpmyadmin/.htpasswd
Require valid-user
```

Here is what each of these lines mean:
- `AuthType Basic`: This line specifies the authentication type that you are implementing. This type will implement password authentication using a password file.
- `AuthName`: This sets the message for the authentication dialog box. You should keep this generic so that unauthorized users won’t gain any information about what is being protected.
- `AuthUserFile`: This sets the location of the password file that will be used for authentication. This should be outside of the directories that are being served. We will create this file shortly.
- `Require valid-user`: This specifies that only authenticated users should be given access to this resource. This is what actually stops unauthorized users from entering.

When you are finished, save and close the file.

The location that you selected for your password file was /etc/phpmyadmin/.htpasswd. You can now create this file and pass it an initial user with the htpasswd utility:
```terminal
sudo htpasswd -c /etc/phpmyadmin/.htpasswd `username`
```
> Don't forget to change the username

You will be prompted to select and confirm a password for the user you are creating. Afterwards, the file is created with the hashed password that you entered.

If you want to enter an additional user, you need to do so without the -c flag, like this:
```terminal
sudo htpasswd /etc/phpmyadmin/.htpasswd additionaluser
```

Then restart Apache to put .htaccess authentication into effect:
```terminal
sudo systemctl restart apache2
```

Now, when you access your phpMyAdmin subdirectory, you will be prompted for the additional account name and password that you just configured:
```terminal
https://`domain_name_or_IP`/phpmyadmin
```

After entering the Apache authentication, you’ll be taken to the regular phpMyAdmin authentication page to enter your MySQL credentials. By adding an extra set of non-MySQL credentials, you’re providing your database with an additional layer of security. This is desirable, since phpMyAdmin has been vulnerable to security threats in the past.

---

Thanks for comming.