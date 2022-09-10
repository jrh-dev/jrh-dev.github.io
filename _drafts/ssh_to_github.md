---
layout: post
title : "Setup an SSH Connection to GitHub"
toc : true
tags: [Tutorial, Git, GitHub, SSH]
categories: Git Github
author: <author_id>
---

<!-- insert intro -->

## Generating an SSH key

### Windows

In powershell;

```
ssh-keygen
```

### Linux

In terminal;

```
ssh-keygen
```

# Use SSH key to access Github

## Setup
* Open the ssh key (typically found in "C:\\Users\\<user_name>\\.ssh" on windows) with a text editor. 

* Copy the contents to clipboard.

* Navigate to Github settings > SSH and GPG keys > Click 'New SSH key`.

![](img/github1.png)

* Give the key a useful title, suggesting the machine it relates to.

* Paste the key into the 'Key' field.

![](img/github2.png)

## Usage

*Example to clone this repo.*

* Navigate to https://github.com/jrh-dev/Productivity

* Under the 'Code' drop down within a repository, click on the 'SSH' tab and copy the link.

![](img/github3.png)

* Run `git clone git@github.com:jrh-dev/Productivity.git` from the terminal to clone into the current directory.

* From then on use git as usual. When performing certain operations you may be propmted to enter the SSH key's passphrase if one exists.