# Kubernetes Fundamentals
https://kubebyexample.com/learning-paths
### Introduction

- Command line interface or CLI for Kubernetes (kubectl) is primary way way developers interact with clusters
- This document is an overview of the available tools and capabilities of kubernetes

#### File permissions

The following command grants write permissions to the file's group:

```bash
$ chmod g+w my-file
$ ls -l my-file
-rw-rw-r--. 1 jdob jdob 12 May 19 10:52 my-file
```

### Viewing file contents

The ```awk``` command is useful for parsing columns numbered starting at 1. Arguments can be passed to AWK
to control which of the columns are outputted. This means we can only display pod names:

```bash
$ kubectl get pods | awk '{ print $1 }'
NAME
my-pod-1
my-pod-2
```

We can also extend this to display two columns in a sentence:

```bash
$ kubectl get pods | awk '{ print $1 " is " $3 }'
NAME is STATUS
my-pod-1 is Running
my-pod-2 is Pending
```

We can also use line numbers with AWK to display from a certain line:
```bash
$ kubectl get pods | awk 'NR>1 { print $1 " is " $3 }'
my-pod-1 is Running
my-pod-2 is Pending
```

```grep``` is another useful tool for using pattern matching on the piped output from a query:
```bash
$ kubectl get pods | grep Running
my-pod-1 1/1 Running 0 9s
```

### Working on the web
The most common tool for transferring across HTTP and HTTPs is curl. Here is an example GET request:
```bash
$ curl http://localhost:8080/greeting
Hello World
```

We can use the ```-X``` command to specify which HTTP method to use. The ``-d` flag indicates the content type and headers
are passed via the ```-H``` flag. The ```@``` sign specifies the file to read data from:

```bash
$ curl -X POST -d @request.json -H "Content-Type: application/json" http://localhost:8080/upload
```

You can also write the result of a ```curl``` request to a file:
```bash
$ curl -o result.txt http://localhost:8080/greeting
$ cat result.txt
Hello World
```

Some machines use the ```wget``` command for downloading files:
```bash
$ wget http://localhost:8080/hello-world.png
$ ls
hello-world.png
```

### Unpacking collections of files

The ```unzip``` command unpacks ZIP files which conventionally end with the ```.zip``` extension. You can
view files before unzipping with the ```-l``` command:

```bash
$ unzip -l project.zip
Archive: project.zip
Length Date Time Name
--------- ---------- ----- ----
0 05-20-2021 16:15 project/
0 05-20-2021 16:15 project/other-file
26 05-20-2021 16:15 project/my-file
--------- -------
26 3 files
```

We then unzip the files:
```bash

$ unzip ../project.zip
Archive: ../project.zip
creating: project/
extracting: project/other-file
extracting: project/my-file

$ ls
project

$ ls project
my-file other-file
```

### Tarballs
Another common packaging format is ```tarball```. These are similar to ```.zip``` files. They are encoded in a different format.
We can view the contents with ```-t``` command:

```bash
$ tar -tf project.tar
project/
project/other-file
project/my-file
```

Tarballs are unpacked using the ```-x``` flag:
```bash
$ tar -xf project.tar
$ ls
project
$ ls project
my-file other-file
```

#### What are Linux, open source software, and a distribution?
- phones, tvs, cloud are all run with linux
- containers are run with linux
- linux like ansible is open source which means it is contributed through collective contributions

#### Bash shell
- The ```#``` shows that you are logged as a root user
- The man page is useful for information about commands
```bash 

NAME
     ls – list directory contents

SYNOPSIS
     ls [-@ABCFGHILOPRSTUWabcdefghiklmnopqrstuvwxy1%,] [--color=when] [-D format] [file ...]

DESCRIPTION
     For each operand that names a file of a type other than directory, ls displays its name as well as any
     requested, associated information.  For each operand that names a file of type directory, ls displays
     the names of files contained within that directory, as well as any requested, associated information.

     If no operands are given, the contents of the current directory are displayed.  If more than one operand
     is given, non-directory operands are displayed first; directory and non-directory operands are sorted
     separately and in lexicographical order.

     The following options are available:

     -@      Display extended attribute keys and sizes in long (-l) output.

     -A      Include directory entries whose names begin with a dot (‘.’) except for . and ...  Automatically
             set for the super-user unless -I is specified.

     -B      Force printing of non-printable characters (as defined by ctype(3) and current locale settings)
             in file names as \xxx, where xxx is the numeric value of the character in octal.  This option is
             not defined in IEEE Std 1003.1-2008 (“POSIX.1”).

     -C      Force multi-column output; this is the default when output is to a terminal.
```

- The command ```ls```
```bash
➜  kubernetes git:(main) ✗ ls
README.md
➜  kubernetes git:(main) ✗ 
```
- The command ```ls -all``` 
```bash
➜  kubernetes git:(main) ✗ ls -all
total 16
drwxr-xr-x   5 tspencer  staff   160 16 Mar 17:55 .
drwxr-xr-x   3 tspencer  staff    96 16 Mar 17:06 ..
drwxr-xr-x  12 tspencer  staff   384 16 Mar 17:28 .git
drwxr-xr-x   8 tspencer  staff   256 16 Mar 17:50 .idea
-rw-r--r--   1 tspencer  staff  5093 16 Mar 17:55 README.md 
```

#### Kernel and user spaces
- kernel is the heart of the linux operating program
- networking, scheduling running programs, security
- kernel space - supports user programs in user space
- unique ids of processes are commonly called PIDs
- the ```ps``` command shows us processes that are running

```bash
➜  kubernetes git:(main) ✗ ps
  PID TTY           TIME CMD
 6812 ttys000    0:00.57 -zsh
```

This command shows me the processes running on my computer:
```bash
ps -ef | awk '{ print $8}'
```
This is the output:
```bash
CMD
/sbin/launchd
/usr/libexec/logd
/usr/libexec/UserEventAgent
```

On a linux machine kernel processes would be signified by ```[]```.

### File management in Linux
```ln -ls``` shows file properties:

```bash
➜  kubernetes git:(main) ✗ ls -l
total 16
-rw-r--r--  1 tspencer  staff  6130 16 Mar 18:20 README.md
drwxr-xr-x  2 tspencer  staff    64 16 Mar 18:23 directory
lrwxr-xr-x  1 tspencer  staff    33 16 Mar 18:27 hello.txt -> /Users/tspencer/Desktop/hello.txt
```

Here I have a normal file signified by ```1``` which is the REAMDE.md a directory specified
by ```2``` and the ```d``` flag. I also have a symlink specified by the ```l``` flag.
This means that any changes in ```hello.txt``` on my desktop are shown in hello.txt in the current directory
and vice versa. You can create a symlink with the following command:
```bash
ln -s /Users/name/Downloads /Users/name/Desktop
```

The ```file``` command shows information on a file:
```bash
➜  kubernetes git:(main) ✗ file README.md
README.md: Unicode text, UTF-8 text
```
Filenames in linux are case sensitive.

### The file system hierarchy
Everything starts from ```/``` the root of the file system.


