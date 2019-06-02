---
layout: post
title:  "Full Proof Shell Script"
date:   2019-03-19
desc: "How to write an ideal shell script"
keywords: "Linux,Shell,blog,Script,DevOps"
categories: [Linux]
tags: [Linux,Shell,DevOps,Scripting]
icon: icon-html
---

I am a lazy DevOps Engineer. So whenever I came across the same task more than 2 times I automate that. Although now we have many automation tools, still the first thing that hit into our mind for automation is bash or shell script.
After making a lot of mistakes and messy scripts :), I am sharing my experiences for writing a good shell script which not only looks good but also it will reduce the chances of error.

The things that every code should have:-

- **A minimum effort in the modification.**
- **Your program should talk in itself, so you don’t have to explain it**.
- **Reusability, Of course, I can’t write the same kind of script or program again and again**.

I am a firm believer in learning by doing. So let’s create a problem statement for ourselves and then try to solve it via shell scripting with best practices :). I would like to have solutions in the comment section of this blog.

**Problem Statement:-** Write a shell script to install and uninstall a package(vim) depending on the arguments. The script should tell if the package is already installed. If no argument is passed it should print the help page.

So without wasting time let’s start for writing an awesome shell script. Here is the list of things that should always be taken care of while writing a shell script.

## Lifespan of Script
If your script is procedural(each subsequent steps relies on the previous step to complete), do me a favor and add set -e in starting of the script so that the script exists on the first error. For example:-

```bash
#!/bin/bash

set -e # Script exists on first failure
set -x # For debugging purpose

install_package() {
    yum install docker
}

list_docker_images() {
    docker images # depends on success of install_package function
}
```

## Functions
Ahha, Functions are my most favorite part of programming. There is a saying

> “Any fool can write code that a computer can understand. Good programmers write code that humans can understand.”

To achieve this always try to use functions and name them properly so that anyone can understand the function just by reading its name. Functions also provide the concept of reusability. It also removes the duplicating of code, how? let’s see this

```shell
#!/bin/bash

install_package() {
    local PACKAGE_NAME="$1"
    yum install "${PACKAGE_NAME}" -y
}

install_package "vim"
```

## Command Sanity
Usually, scripts call other scripts or binary. When we are dealing with commands there are chances that commands will not be available on all systems. So my suggestion is to check them before proceeding.

```shell
#!/bin/bash

check_package() {
    local PACKAGE_NAME="$1"
    if ! command -v "${PACKAGE_NAME}" > /dev/null 2>&1
    then
        printf "${PACKAGE_NAME} is not installed.\n"
    else
        printf "${PACKAGE_NAME} is already installed.\n"
    fi
}

check_package "vim"
```

## Help Page
If you guys are familiar with Linux, you have certainly noticed that every Linux command has its help page. The same thing can be true for the script as well. It would be really helpful to include --help flag.

```shell
#!/bin/bash

INITIAL_PARAMS="$*"

help_function() {
    {
        printf "Usage:- ./script <option>\n"
        printf "Options:\n"
        printf "  -a ==> Install all base softwares\n"
        printf "  -r ==> Remove base softwares\n"
    }
}

arg_checker() {
    if [ "${INITIAL_PARAMS}" == "--help" ]; then
        help_function
    fi
}

arg_checker
```

## Logging
Logging is the most critical thing for everyone whether he is a developer, sysadmin or DevOps. Debugging seems to be impossible without logs. As we know most applications generate logs for understanding that what is happening with the application, the same practice can be implemented for shell script as well. For generating logs we have a bash utility called logger.

```shell
#!/bin/bash

DATE=$(date)

declare DATE

check_file() {
    local FILENAME="$1"
    if ! ls "${FILENAME}" > /dev/null 2>&1
    then
        logger -s "${DATE}: ${FILENAME} doesn't exists"
    else
        logger -s "${DATE}: ${FILENAME} found successfuly"
    fi
}

check_file "/etc/passwd"
```

## Variables
I like to name my variables in Capital letters with an underscore, In this way, I will not get confused with the function name and variable name. Never give a,b,c etc. as a variable name instead of that try to give a proper name to a variable as well just like functions.

```shell
#!/bin/bash

# Use declare for declaring global variables
declare GLOBAL_MESSAGE="Hey, I am a global message"

# Use local for declaring local variables inside the function
message_print() {
    local LOCAL_MESSAGE="Hey, I am a local meesage"
    printf "Global Message:- ${GLOBAL_MESSAGE}\n"
    printf "Local Message:- ${LOCAL_MESSAGE}\n"
}

message_print
```

## Cases
Cases are also a fascinating part of shell script. But the question is when to use this? According to me if your shell program is providing more than one functionality basis on the arguments then you should go for cases. For example:- If your shell utility provides the capability of installing and uninstalling the software.

```shell
#!/bin/bash

print_message() {
    MESSAGE="$1"
    echo "${MESSAGE}"
}

case "$1" in
    -i|--input)
        print_message "Input Message"
        ;;
    -o|--output)
        print_message "Output Message"
        ;;
    --debug)
        print_message "Debug Message"
        ;;
    *)
        print_message "Wront Input"
        ;;
esac
```

In this blog, we have covered functions, variables, the lifespan of a script, logging, help page, command sanity.
I hope these topics help you in your daily life while using the shell script. If you have any feedback please let me know through comments.
Cheers Till the next Time!!!!
