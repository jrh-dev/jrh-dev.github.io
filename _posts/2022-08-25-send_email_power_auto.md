---
layout: post
title : "Send Emails with R using Power Automate"
toc : true
tags: [R, Power Automate, Automation, Email]
categories: R Notifications
author: <author_id>
---

Power Automate allows us to build automated workflows and can link into many applications. This guide demonstrates using Power Automate to create an API endpoint that we can use to trigger the sending of an email from Outlook using R. The automated process, or flow in Microsoft parlance, provides a simple and secure way to send emails using R without the installation of additional packages.

## Creating a ***flow***

We start by navigating to the [Power Automate](https://make.powerautomate.com/) home page. Once signed in we can click 'Create' on the navigation bar to the left.

![](/assets/img/send_email_power_auto_img/img01.png)

We then select the 'Instant cloud flow' option from the choices presented under the 'Three ways to make a flow - Start from blank' section.

![](/assets/img/send_email_power_auto_img/img02.png)

The next page allows us to give our flow a name, in this case we're going to name it 'send from me' which we can abbreviate to 'sfm'.

For the 'Choose how to trigger this flow' section, we will select 'When an HTTP request is received', and click 'Create'.

![](/assets/img/send_email_power_auto_img/img03.png)

## Creating a JSON schema

We now need to think about the information we want to send to Power Automate from an R process. The URL we use will be encrypted (HTTPS), though we likely won't want to send confidential data. Whilst the actual size limit for POST requests (our method for sending information to Power Automate) varies, the method may not be practical for sending large amounts of data.

In our case we want to specify;

-   A sender email address (who will the email come from - the user must have rights to access the account specified)
-   A recipient/s email address
-   A subject line for the email
-   A body for the email

We will be using the JSON format to build a schema so that Power Automate knows how to parse the HTTP request that we send. All of our values are going to be strings, so our JSON schema will look like this;

```json 
{
    "emailfrom": "string",
    "emailto": "string",
    "emailsubject": "string",
    "emailbody": "string"
}    
```

Our newly created flow initially contains only one step, 'When a HTTP request is received', if we click on it we can start configuration.

After clicking on 'Use sample payload to generate schema', we can paste our JSON schema into the text box, and Power Automate will automatically generate the schema in the format it requires once we click "Done".

![](/assets/img/send_email_power_auto_img/img05.png)

![](/assets/img/send_email_power_auto_img/img06.png)

## Configuring the email

Now that we have finished telling Power Automate what to do when our HTTP request is received we can click on 'next step' and choose 'Send an email (V2)' from the available operations.

![](/assets/img/send_email_power_auto_img/img07.png)

Having added the 'Send an email (V2)' step, we can now populate the parameters. Clicking in the 'To' text box and then on the hyper-linked text underneath it to the right, 'Add dynamic content', will allow us to use the values from the JSON schema we set up earlier.

We can add the relevant elements from our JSON schema to each field by dragging and dropping.

![](/assets/img/send_email_power_auto_img/img08.png)

When we have finished we click 'Save' at the bottom of the page.

If we now click back onto the 'When a HTTP request is received' step, we can see that the URL we need to send our HTTP request to has now been generated.

![](/assets/img/send_email_power_auto_img/img09.png)

## Sending the HTTP request

### Using R

We can create the HTTP request in R using the `httr` package.

Note that the single quotes (`'`) used within `data` are escaped (with `\`). The `\n` syntax is used to specify a new line and is used here in order to abide to the JSON schema.

```r
library(httr)

headers <- c(
  `Content-Type` = 'application/json'
)

data <- paste0(
  '{\n \'emailfrom\': \'jim@example.com\',\n',
  '\'emailto\': \'jim@example.com\',\n',
  '\'emailsubject\': \'Test Subject\',\n',
  '\'emailbody\': \'Test body of email text.\'\n}'
  )   

res <- httr::POST(
  url = 'https://generated_url/workflows/12345',
  httr::add_headers(.headers=headers),
  body = data
  )
```

### Using bash

There are many ways in which R can be used to send HTTP requests directly, however, formatting JSON headers correctly can be challenging. One simple solution when using R on top of a Linux OS is to use bash scripting, and then call the bash script from R. This method has the advantage of being callable from other languages and the command line.

### Create a bash shell script

From Rstudio we can click `File > New File > Text File`.

![](/assets/img/send_email_power_auto_img/img10.png)

We will save the file to our home directory, naming it `sfm.sh`

The first line of the script will be;

```bash
#!/bin/bash
```

This line instructs the operating system to use the bash program to execute the code that follows.

We next specify the arguments we want to be able to accept when we call the shell script. The arguments are positional, so we would first supply the `url`, then `emailfrom`, etc.

```bash
url="$1"
emailfrom="$2"
emailto="$3"
sub="$4"
bod="$5"
```

The final part of the script constructs the HTTP request itself.

```bash
curl --location --request POST "$url" \
--header 'Content-Type: application/json' \
--data "{
    'emailfrom': '$emailfrom',
    'emailto': '$emailto',
    'emailsubject': '$sub',
    'emailbody': '$bod'
}"
```

Our full bash script will now look like this.

```bash
#!/bin/bash
url="$1"
emailfrom="$2"
emailto="$3"
sub="$4"
bod="$5"
curl --location --request POST "$url" \
--header 'Content-Type: application/json' \
--data "{
    'emailfrom': '$emailfrom',
    'emailto': '$emailto',
    'emailsubject': '$sub',
    'emailbody': '$bod'
}"
```

We can now use the bash script from the command line to send an email by using the `bash` command, then specifying the path to the script we just created. We can pass each of our 5 arguments by typing them one after the other, remembering that our script uses them positionally.

```bash
bash "~/sfm.sh" "https://generated_url/workflows/12345" "jim@example.com" "jim@example.com" "Test Subject" "Test body of email text."
```

We can use the `system()` function to execute the same syntax from R.

```r
system('bash "~/sfm.sh" "https://generated_url/workflows/12345" "jim@example.com" "jim@example.com" "Test Subject" "Test body of email text."')
```

We can also create a function to make calling the bash script within R simpler, are more intuitive. This also makes it easier to reuse the function for different purposes.

```r
# function definition
sfm <- function(bash, url, emailfrom, emailto, subject, body) {
  system(glue::glue('bash "{normalizePath(bash)}" "{url}" "{emailfrom}" "{emailto}" "{subject}" "{body}"'))
  return(invisible())
}

# usage
sfm(
  bash = "~/sfm.sh", 
  url = "https://generated_url/workflows/12345", 
  emailfrom = "jim@example.com", 
  emailto = "jim@example.com", 
  subject = "Test Subject", 
  body = "Test body of email text."
)
```

## Maintaining flows

We can view flows that we have created by returning to the [Power Automate](https://make.powerautomate.com/) home page and clicking 'My flows' on the navigation bar to the left.

![](/assets/img/send_email_power_auto_img/img11.png)

Clicking on the name of an individual flow presents a useful overview. Including a 28 day run history. If a flow has failed it is possible to get further details by clicking on the links under 'Status'.

![](/assets/img/send_email_power_auto_img/img12.png)

## Next steps

The Power Automate [website](https://powerautomate.microsoft.com/en-gb/) offers further support, tutorials, templates, and suggestions for automating typical workflows.
