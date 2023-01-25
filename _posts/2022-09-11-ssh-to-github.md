---
layout: article
title : "Setup an SSH Connection to GitHub"
toc : true
tags: [Tutorial, Git, GitHub, SSH]
author: <author_id>
show_edit_on_github: false
excerpt_separator: <!--more-->
---

Using Secure Shell Protocol (SSH) is a safe, secure, and efficient way to read and write data from repositories hosted on GitHub. Connecting via SSH authenticates your connection using a private key file stored on your local machine, which can also be password protected for additional security.

<!--more-->

This brief guide demonstrates how to setup SSH for GitHub.

## Generating an SSH key

We are going to use the command line to interact with our SSH keys. Windows users should consider PowerShell, whereas Linux users should use their preferred terminal emulator (I like [Konsole](https://konsole.kde.org/)).

### Checking for an existing key

Before we create a key, we can check whether we already have one stored on our machine. Default key names are `id_rsa`, `id_cdsa`, `id_ecdsa_sk`, `id_ed25519`, and `id_ed25519_sk`.

By default, Windows stores SSH keys in `C:\Users\<user>\.ssh`. We can use the `ls` command to view the contents of the directory.

{% highlight powershell %}
$ ls C:\Users\<user>\.ssh

    Directory: C:\Users\<user>\.ssh

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        06/07/2022     17:56             56 config
-a----        17/04/2022     16:43           2655 id_rsa
-a----        17/04/2022     16:43            566 id_rsa.pub
-a----        15/08/2022     20:47           1882 known_hosts
-a----        17/04/2022     16:49            443 known_hosts.old
{% endhighlight %}

We can see in the example that an existing private key `id_rsa` and public key `id_rsa.pub` have been found.

For linux users, the default location is a hidden `.ssh` folder in the users home directory. We can use the `ls` command to view the contents of the directory.

{% highlight shell %}
$ ls /home/<user>/.ssh

id_rsa  id_rsa.pub  known_hosts
{% endhighlight %}

Again, in the example we see that an existing private key `id_rsa` and public key `id_rsa.pub` have been found.

### Creating a key

If you don't already have a suitable key, generating one is very simple using the command `ssh-keygen`. The `ssh-keygen` command is from the [OpenSSH](https://en.wikipedia.org/wiki/OpenSSH) tool. The tool comes installed by default with modern versions of Windows and many Linux distributions.

For both Windows and Linux users, generating a new key is simple.

{% highlight shell %}
$ ssh-keygen
{% endhighlight %}

During the creation process you will be prompted to ***'Enter passphrase'***. This is an optional step, but using a passphrase does enhance security.

Whilst we can leave the passphrase blank, if a key is not protected and an attacker gains access to our system they will also be able to access our GitHub account. As such, adding a passphrase is highly recommended.

The newly created keys will by default be placed in;

**Windows** 

`C:\Users\<user>\.ssh`

**Linux** 

`/home/<user>/.ssh`

You will notice that two files are created by `ssh-keygen`, a private key (`id_rsa`), and a public key (`id_rsa.pub`). The public key is the one that you will add to GitHub. The private key should be treated like any other secret and not be shared with or revealed to anyone.

## Add an SSH key to GitHub

Once we have found a suitable key or created one, we need to add the public key to GitHub.

We first log in to GitHub, then click on the profile icon in the top right of any page and select ***'Settings'***.

![Image](/assets/img/ssh-to-github/img01.png){:.shadow}

In the ***'Access'*** section of the settings page sidebar we click on ***'SSH and GPG keys'***.

![Image](/assets/img/ssh-to-github/img02.png){:.shadow}

Next, we click on the green ***'New SSH key'*** button towards the top right of the page.

![Image](/assets/img/ssh-to-github/img03.png){:.shadow}

Now we need to copy our public key to the clipboard. The simplest way to do this is to return to PowerShell or the terminal and use the `cat` command to print the contents of our public key to the console. 

{% highlight powershell %}
$ cat c:\Users\<user>\.ssh\id_rsa.pub
{% endhighlight %}

{% highlight shell %}
$ cat /home/<user>/.ssh/id_rsa.pub 
{% endhighlight %}

We can now copy our public key from the console and paste it into the ***'Key'*** text box. We also need to give the key a title, which could be the machine it relates to, for example 'work-laptop', 'home-desktop', etc. ***'Key type'*** should be set to ***'Authentication Key'***.

![Image](/assets/img/ssh-to-github/img04.png){:.shadow}

We complete the process by clicking the green ***'Add SSH key'*** button.

## Accessing GitHub with SSH

We can now clone a repository from GitHub using our SSH key.

We first obtain the SSH link from the repository we want to clone.

![Image](/assets/img/ssh-to-github/gif01.gif){:.shadow}

Then from the terminal, we use `git clone`.

{% highlight shell %}
git clone git@github.com:jrh-dev/git_demo.git
{% endhighlight %}

## Next steps

You've set up secure SSH access to GitHub, but if you're new to Git why not check out my [Introduction to Git](https://jrh-dev.github.io/posts/intro_to_git/)?

Want to learn more? GitHub provides [extensive documentation](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/about-ssh) for using SSH, including details on which public key algorithms it accepts.

