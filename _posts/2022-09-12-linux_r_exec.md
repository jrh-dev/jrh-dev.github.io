---
layout: post
title : "Making R Scripts Executable on Linux"
toc : true
tags: [R, R advanced, Tips, Automation]
categories: R Advanced
author: <author_id>
---

![](https://img.shields.io/badge/Tested%20On-Ubuntu%2021.04-blue) ![](https://img.shields.io/badge/Language-R-blue)

If your experience is anything like mine you will find yourself with plenty of repetitive tasks to complete alongside your more interesting and exciting developmental work. Often these tasks are things like pulling up to date extracts and moving outputs around. Wherever possible I like to save time by automating the performing of these tasks, writing short utility scripts and scheduling them with CRON, or running them manually if required on an ad-hoc basis.

When working on linux I can make running my utility scripts even easier by making them executable.

## Creating a utility script

For some reason I want a script that I can schedule, or call from the terminal, that prints the current date and time to the console in addition to storing the output in a .txt file.

I create a new directory with the `mkdir` command, and move into it with `cd`.

``` bash
$ mkdir exec_r

$ cd exec_r
```

I will use my preferred text editor, [nano](https://www.nano-editor.org/), to create the script itself.

``` bash
$ nano exec_r_demo.R
```

The first line of the script must contain a shebang `#!` and the path to a valid interpreter, in this case `Rscript`.

I don't want my user-specific R profile to be considered when I run this script and I also don't want the session to be saved or a previous one restored, so I'm going to pass the `--vanilla` option. To be able to pass options in shebang lines I also need to specify the `-S` option.

Putting all of this together, the first line of my R script looks like this;

``` r
#!/usr/bin/env -S Rscript --vanilla
```

Now I can add my code.

``` r
#!/usr/bin/env -S Rscript --vanilla

st <- Sys.time()

print(st)

writeLines(st, "time.txt")
```

## Making a script executable

I can make my utility script executable using the `chmod` command.

``` bash
$ chmod +x exec_r_demo.R
```

## Executing a script

Executing the script is now as simple as specifying its path.

``` bash
$ ~/exec_r/exec_r_demo.R

[1] "2022-09-12 05:54:51 BST"
```

I can use the `ls` command to check the output was created, and can even go a step further and check the contents with `cat`.

``` bash
$ ls ~/exec_r/

exec_r_test.R time.txt

$ cat ~/exec_r/time.txt

2022-09-12 05:54:51 BST
```

I can schedule the script to run daily at 8am using CRON, by adding a line to my crontab.

``` bash
00 08 * * * ~/exec_r/exec_r_demo.R
```

## The Rscript path

For most users the `/usr/bin/env Rscript` path should be the preferred option as it represents the path most likely to work across a majority of systems. However, in the event that this doesn't work for you, there are a couple of other options.

The first option is to find the Rscript path using `which`.

``` bash
$ which Rscript

/usr/local/bin/Rscript
```

To use the path the shebang line can be changed to;

``` bash
#!/usr/local/bin/Rscript --vanilla
```

Another, and the least preferable option is to use the `/opt/` path. You should entries under `/opt/` for any version of R that you have installed. I'm currently using R 4.2.1, so could set my shebang line to;

``` bash
#!/opt/R/4.2.1/bin/Rscript --vanila`. 
```

The second option is the least portable and maintainable, it likely won't work for other users or on different systems, and may stop working if you upgrade your version of R.

## Next steps

Being able to run R scripts from the terminal and automate them can be a huge efficiency booster. You can also use variations of the shebang line to run bash `#!/usr/bin/env bash`, python `#!/usr/bin/env python3`, and other scripts.
