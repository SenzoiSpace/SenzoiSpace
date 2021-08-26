---
title: How to Install NodeJS & NPM on Ubuntu
author: Senzoi
date: 2021-08-25 11:00:00 +0800
categories: [Tutorial, Web Development]
tags: [nodejs, npm]
---

NodeJS is a cross-platform JavaScript runtime environment built on Chrome’s JavaScript, designed to execute JavaScript code on the server-side. It is generally used to build back-end applications, but it is also popular as a full-stack and front-end solution. npm is the default package manager for NodeJS and the world’s largest software registry.

In this post, I will show you how to install **NodeJS** and **npm** on Ubuntu.

---

## Option 1: Install NodeJS and npm from the Ubuntu repository
At the time of writing, the NodeJS version included in the Ubuntu 20.04 repositories is 10.19.0 which is the previous TLS version.
1. Run this commands to update package index and install **NodeJS** and **npm**.
```terminal
sudo apt update
sudo apt install nodejs npm
```
The command above will install a number of packages, including the tools necessary to compile and install native addons from npm.
2. Once done, verify the installation by running:
```terminal
nodejs --version
```
Output
```output
v10.19.0
```
For npm:
```terminal
npm --version
```
Output
```output
6.14.4
```

---

## Option 2: Instal NodeJS and npm from NodeSource

NodeSource is a company focused on providing enterprise-grade Node support. It maintains an APT repository containing multiple NodeJS versions. Use this repository if your application requires a specific version of NodeJS.

At the time of writing, NodeSource repository provides the following versions:
- v14.x - The latest stable version.
- v13.x
- v12.x - The latest LTS version.
- v10.x - The previous LTS version.

We’ll install NodeJS version `14.x`:

1. Run the following command as a user with sudo privileges to download and execute the NodeSource installation script:
```terminal
curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
```
The script will add the NodeSource signing key to your system, create an apt repository file, install all necessary packages, and refresh the apt cache.

If you need another **NodeJS** version, for example `12.x`, change the `setup_14.x` with `setup_12.x`.
2. Once the NodeSource repository is enabled, install **NodeJS** and **npm**:
```terminal
sudo apt install nodejs
```
3. Verify that the **NodeJS** and **npm** were successfully installed by printing their versions:
```terminal
nodejs --version
```
Output
```output
v14.17.5
```
For npm:
```terminal
npm --version
```
Output
```output
6.14.4
```

To be able to compile native addons from npm you’ll need to install the development tools:
```terminal
sudo apt install build-essential
```

---

## Option 3: Instal NodeJS and npm using NVM

**NVM** (Node Version Manager) is a bash script that allows you to manage multiple NodeJS versions on a per-user basis. With NVM you can install and uninstall any NodeJS version that you want to use or test.

Visit the [nvm GitHub repository](https://github.com/nvm-sh/nvm#installing-and-updating) page and copy either the `curl` or `wget` command to download and install the nvm script:
```terminal
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

Do not use **sudo** as it will enable nvm for the root user.

The script will clone the project’s repository from Github to the `~/.nvm` directory:

Output
```terminal
=> Close and reopen your terminal to start using nvm or run the following to use it now:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion
```
As the output above says, you should either close and reopen the terminal or run the commands to add the path to nvm script to the current shell session. You can do whatever is easier for you.

Once the script is in your **PATH**, verify that **nvm** was properly installed by typing:
```terminal
nvm --version
```
Output
```output
0.38.0
```
To get a list of all NodeJS versions that can be installed with **nvm**, run:
```terminal
nvm list-remote
```
The command will print a huge list of all available Node.js versions.

To install the latest available version of Node.js, run:
```terminal
nvm install node
```
Output
```output
...
Checksums matched!
Now using node v14.2.0 (npm v6.14.4)
Creating default alias: default -> node (-> v14.17.5)
```
Once the installation is completed, verify it by printing the Node.js version:
```terminal
node --version
```
output
```output
v14.17.5
```

Let’s install two more versions, the latest LTS version and version 10.9.0:
```terminal
nvm install --lts
nvm install 10.9.0
```

You can list the installed Node.js versions by typing:
```terminal
nvm ls
```

The output should look something like this:
```output
>      v10.9.0
       v12.16.3
        v14.2.0
default -> node (-> v14.2.0)
node -> stable (-> v14.2.0) (default)
stable -> 14.2 (-> v14.2.0) (default)
iojs -> N/A (default)
unstable -> N/A (default)
lts/* -> lts/erbium (-> v12.16.3)
lts/argon -> v4.9.1 (-> N/A)
lts/boron -> v6.17.1 (-> N/A)
lts/carbon -> v8.17.0 (-> N/A)
lts/dubnium -> v10.20.1 (-> N/A)
lts/erbium -> v12.16.3
```
The entry with an arrow on the right (> v10.9.0) is the Node.js version used in the current shell session and the default version is set to v14.2.0. Default version is the version that will be active when opening new shells.

If you want to change the currently active version enter:
```terminal
nvm use 12.16.3
```
Output
```output
Now using node v12.16.3 (npm v6.14.4)
```

To change the default Node.js version, run the following command:
```terminal
nvm alias default 12.16.3
```

For more detailed information about how to use the nvm script, visit the [project’s GitHub](https://github.com/nvm-sh/) page.

---

Thanks for comming here.