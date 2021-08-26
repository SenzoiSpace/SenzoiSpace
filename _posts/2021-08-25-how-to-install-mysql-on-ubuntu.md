---
title: How to Install MySQL on Ubuntu
author: Senzoi
date: 2021-08-25 20:10:00 +0800
categories: [Tutorial, Database]
tags: [mysql]
image:
  src: https://i.ibb.co/SmNkT2F/How-to-Install-My-SQL-on-Ubuntu-Banner.png
  width: 1280
  height: 720
---

[MySQL](https://www.mysql.com/) is an open-source database management system, commonly installed as part of the popular LAMP (Linux, Apache, MySQL, PHP/Python/Perl) stack. It implements the relational model and uses Structured Query Language (better known as SQL) to manage its data.

In this post, I will show you how install **MySQL** on Ubuntu.

---

**Prerequisites**

To follow this tutorial, you will need:

- One Ubuntu server with a non-root administrative user and a firewall configured with UFW, read this post to [setup UFW and Apache2](http://127.0.0.1:4000/how-to-install-apache2-on-ubuntu.html).

---

## Step 1: Install MySQL

On Ubuntu, you can install MySQL using the APT package repository. At the time of this writing, the version of MySQL available in the default Ubuntu repository is version 8.0.19.

To install it, update the package index on your server if you’ve not done so recently:
```terminal
sudo apt update
```

Then install the `mysql-server` package:
```terminal
sudo apt install mysql-server
```

This will install MySQL, but will not prompt you to set a password or make any other configuration changes. Because this leaves your installation of MySQL insecure, we will address this next.

---

## Step 2: Configure MySQL
For fresh installations of MySQL, you’ll want to run the DBMS’s included security script. This script changes some of the less secure default options for things like remote root logins and sample users.

Run the security script with `sudo`:
```terminal
sudo mysql_secure_installation
```

This will take you through a series of prompts where you can make some changes to your MySQL installation’s security options. The first prompt will ask whether you’d like to set up the Validate Password Plugin, which can be used to test the password strength of new MySQL users before deeming them valid.

If you elect to set up the Validate Password Plugin, any MySQL user you create that authenticates with a password will be required to have a password that satisfies the policy you select. The strongest policy level — which you can select by entering 2 — will require passwords to be at least eight characters long and include a mix of uppercase, lowercase, numeric, and special characters:

Output
```output
Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: Y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 0
0 // In my case I use 0 for Low Security Password
```

Regardless of whether you choose to set up the Validate Password Plugin, the next prompt will be to set a password for the MySQL root user. Enter and then confirm a secure password of your choice:

```output
Please set the password for root here.

New password: 

Re-enter new password:
```

Note that even though you’ve set a password for the root MySQL user, this user is not currently configured to authenticate with a password when connecting to the MySQL shell.

If you used the Validate Password Plugin, you’ll receive feedback on the strength of your new password. Then the script will ask if you want to continue with the password you just entered or if you want to enter a new one. Assuming you’re satisfied with the strength of the password you just entered, enter Y to continue the script:

```output
Estimated strength of the password: 50 
Do you wish to continue with the password provided?(Press y|Y for Yes, any other key for No) : Y
```

From there, you can press Y and then ENTER to accept the defaults for all the subsequent questions. This will remove some anonymous users and the test database, disable remote root logins, and load these new rules so that MySQL immediately respects the changes you have made. But in my case, I have my own options:

```output
Remove anonymous users? (Press y|Y for Yes, any other key for No) : N
...
Disallow root login remotely? (Press y|Y for Yes, any other key for No) : Y
...
Remove test database and access to it? (Press y|Y for Yes, any other key for No) : N
...
Reload privilege tables now? (Press y|Y for Yes, any other key for No) : Y
Success.
```

Once the script completes, your MySQL installation will be secured. You can now move on to creating a dedicated database user with the MySQL client.

---

## Step 3: Create a Dedicated MySQL User and Granting Privileges

Upon installation, MySQL creates a root user account which you can use to manage your database. This user has full privileges over the MySQL server, meaning it has complete control over every database, table, user, and so on. Because of this, it’s best to avoid using this account outside of administrative functions. This step outlines how to use the root MySQL user to create a new user account and grant it privileges.

In Ubuntu systems running MySQL 5.7 (and later versions), the root MySQL user is set to authenticate using the auth_socket plugin by default rather than with a password. This plugin requires that the name of the operating system user that invokes the MySQL client matches the name of the MySQL user specified in the command, so you must invoke mysql with sudo privileges to gain access to the root MySQL user:

```terminal
sudo mysql
```

Once you have access to the MySQL prompt, you can create a new user with a CREATE USER statement. These follow this general syntax:

**PS**: There is a known issue with some versions of PHP that causes problems with `caching_sha2_password`. If you plan to use this database with a PHP application — phpMyAdmin, for example — you may want to create a user that will authenticate with the older, though still secure, `mysql_native_password` plugin instead:
```console
CREATE USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

If you aren’t sure, you can always create a user that authenticates with `caching_sha2_plugin` and then `ALTER` it later on with this command:
```console
ALTER USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

After creating your new user, you can grant them with `ALL_PREVILEGES` privilege, which will provide them with broad superuser privileges akin to the **root** user’s privileges, like so::
```console
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' WITH GRANT OPTION;
```

Following this, it’s good practice to run the FLUSH PRIVILEGES command. This will free up any memory that the server cached as a result of the preceding CREATE USER and GRANT statements:
```console
FLUSH PRIVILEGES;
```

Then you can exit the MySQL client:
```console
exit
```

In the future, to log in as your new MySQL user, you’d use a command like the following:
```terminal
mysql -u username -p
```

The -`p` flag will cause the MySQL client to prompt you for your MySQL user’s password in order to authenticate.

Finally, let’s test the MySQL installation.

---

## Step 4: Testing MySQL

Regardless of how you installed it, MySQL should have started running automatically. To test this, check its status.
```terminal
systemctl status mysql.service
```
Output
```output
● mysql.service - MySQL Community Server
     Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset:>
     Active: active (running) since Wed 2021-08-25 13:53:55 WIB; 25min ago
   Main PID: 22975 (mysqld)
     Status: "Server is operational"
      Tasks: 39 (limit: 9444)
     Memory: 360.5M
     CGroup: /system.slice/mysql.service
             └─22975 /usr/sbin/mysqld
```

If MySQL isn’t running, you can start it with.
```terminal
sudo systemctl start mysql
```

For an additional check, you can try connecting to the database using the mysqladmin tool, which is a client that lets you run administrative commands. For example, this command says to connect as a MySQL user named **username** (`-u username`), prompt for a password (`-p`), and return the version. Be sure to change **username** to the name of your dedicated MySQL user, and enter that user’s password when prompted:
```terminal
sudo mysqladmin -p -u username version
```
Output
```output
mysqladmin  Ver 8.0.26-0ubuntu0.20.04.2 for Linux on x86_64 ((Ubuntu))
Copyright (c) 2000, 2021, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version		8.0.26-0ubuntu0.20.04.2
Protocol version	10
Connection		Localhost via UNIX socket
UNIX socket		/var/run/mysqld/mysqld.sock
Uptime:			27 min 9 sec

Threads: 2  Questions: 72  Slow queries: 0  Opens: 238  Flush tables: 3  Open tables: 157  Queries per second avg: 0.044
```

This means MySQL is up and running.

---

Thanks for comming here.