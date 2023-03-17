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
```bash
tom@tom-ubuntu:~/Projects$ ls /
bin   cdrom  etc   lib    lib64   lost+found  mnt  proc  run   snap  swapfile  tmp  var
boot  dev    home  lib32  libx32  media       opt  root  sbin  srv   sys       usr

```
- The ```bin``` directory keeps executables usable by all users of the system
- The ```sbin``` is for system directories. These are programs typically only usable for the root user.
- The usr stands for unix system resources where the binaries are kept e.g. ```ls```
- Systems commands are kept in ```/usr/sbin```
- The boot directory is where the kernel is kept
- ```dev``` is where device files are kept
- ```etc``` stands for extended text configurations. This is where the configurations are kept such as
the ssh server
- ```/home``` directories are for normal users
- root users home directory is ```/root```
- ```lib``` and ```lib64``` keep application libraries and shared code
- ```tmp``` is for temporary space. Files in this directory are kept for 10 days
- ```var``` stands for variable data for transient and temporary files such as logging

### Vim
Vim operates in different modes. Command mode is for special commands. Change into insert mode with ```i```.
Hit escape to go out of insert mode. Extended command mode is ```:```. Write a file with ```:write```.
```:q``` to quit. ```y``` is to yank text. ```p``` pastes. ```10p``` pastes ten times.
```dd``` deletes a line. ```10dd``` deletes ten lines. ```o``` puts the cursor on a new line.
Escape puts us back into command mode. ```ZZ``` closes the file.

### Linux users
Users on linux can be organized into groups. ```useradd``` adds a user.

```bash
tom@tom-ubuntu:~/Projects$ sudo useradd john
[sudo] password for tom: 
tom@tom-ubuntu:~/Projects$ id john
uid=1001(john) gid=1001(john) groups=1001(john)
```

### Linux file permissions
Read, write, execute. Then we specify user, group and other.
```bash
  u   g   o
-|---|---|---
```
For example:
```bash
-|---|---|---
  rwx rwx rwx
```
This command shows us permissions on a directory:
```bash
tom@tom-ubuntu:~$ ls -ld common
drwxrwxr-x 2 tom tom 4096 Mar 17 00:02 common
```
Here tom is the owner of the above directory.

### Networking
Show network connections with:
```bash
nmcli connection show
```

### Sysetm startup processes
Systemd is responsible for the initialization of the system and management of daemons.
Use the ```systemctl``` command to interact with systemd:
```bash
tom@tom-ubuntu:~$ systemctl list-unit-files 
UNIT FILE                                      STATE           PRESET  
proc-sys-fs-binfmt_misc.automount              static          -       
-.mount                                        generated       -       
boot-efi.mount                                 generated       -       
dev-hugepages.mount                            static          -       
dev-mqueue.mount                               static          -       
proc-sys-fs-binfmt_misc.mount                  disabled        disabled
snap-bare-5.mount                              enabled         enabled 
snap-core-14447.mount                          enabled         enabled 
snap-core-14784.mount                          enabled         enabled 
snap-core18-2708.mount                         enabled         enabled 
snap-core18-2714.mount                         enabled         enabled 
snap-core20-1822.mount                         enabled         enabled 
snap-core20-1828.mount                         enabled         enabled 
```

### Linux containers
Containers contain application runtime in portable isolated portable units.

### What is Kubernetes?
- Kubernetes is a portable extensible program for orchestrating containers
- Kubernetes is hosted by CNCF. It is an open source technology.
- Instead of declaring an API to achieve workload state Kubernetes fulfills the required workload state.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: myboot
  name: myboot
spec:
  replicas: 4
  selector:
    matchLabels:
      app: myboot
  template:
    metadata:
      labels:
        app: myboot
    spec:
      containers:
        - name: myboot
          image: quay.io/rhdevelopers/myboot:v1
          ports:
            containerPort: 8080
```
This specifies I want four instances. If I have three nodes Kubernetes distributes the instances to the nodes appropriately.
Kubernetes is self-healing, service discover and load balancing through a simple name, storage orchestration. It also automates roll outs and roll backs.
It provides optimal resources and configuration management.

### kubectl
Kubectl create namespace:
```bash
tom@tom-ubuntu:~$ kubectl create namespace myspace
namespace/myspace created
```

We can also set the config context:
```bash
tom@tom-ubuntu:~$ kubectl config set-context --current --namespace=myspace
Context "minikube" modified.
```

Kubectl now shows the following:
```bash
tom@tom-ubuntu:~$ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority: /home/tom/.minikube/ca.crt
    extensions:
    - extension:
        last-update: Fri, 17 Mar 2023 01:15:46 GMT
        provider: minikube.sigs.k8s.io
        version: v1.29.0
      name: cluster_info
    server: https://192.168.49.2:8443
  name: minikube
contexts:
- context:
    cluster: minikube
    extensions:
    - extension:
        last-update: Fri, 17 Mar 2023 01:15:46 GMT
        provider: minikube.sigs.k8s.io
        version: v1.29.0
      name: context_info
    namespace: myspace
    user: minikube
  name: minikube
current-context: minikube
kind: Config
preferences: {}
users:
- name: minikube
  user:
    client-certificate: /home/tom/.minikube/profiles/minikube/client.crt
    client-key: /home/tom/.minikube/profiles/minikube/client.key

```

We can also create a deployment:
```bash
tom@tom-ubuntu:~$ kubectl create deployment myapp --image=quay.io/rhdevelopers/quarkus-demo:v1
deployment.apps/myapp created

```
Also, we can scale the deployment:
```bash
tom@tom-ubuntu:~$ kubectl scale deployment myapp --replicas=2
deployment.apps/myapp scaled
```
We can create a service for the deployment:
```bash
tom@tom-ubuntu:~$ kubectl expose deployment myapp --port=8080 --type=LoadBalancer
service/myapp exposed
```

We can now see the service:
```bash
tom@tom-ubuntu:~$ kubectl get service
NAME    TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
myapp   LoadBalancer   10.108.43.184   <pending>     8080:30956/TCP   29s

```

We can also describe the service:
```bash
tom@tom-ubuntu:~$ kubectl describe service myapp
Name:                     myapp
Namespace:                myspace
Labels:                   app=myapp
Annotations:              <none>
Selector:                 app=myapp
Type:                     LoadBalancer
IP Family Policy:         SingleStack
IP Families:              IPv4
IP:                       10.108.43.184
IPs:                      10.108.43.184
Port:                     <unset>  8080/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30956/TCP
Endpoints:                10.244.0.3:8080,10.244.0.4:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>

```

### What is a pod?
The pod is the minimum unit that kubernetes can manage. A pod is a set of one or more containers.
"A pod is a number of animals such as whales clustered together". Often we have just one container in a pod.
All containers in a pod are executed in the same node of the cluster. In a service mesh there will be more than one container in a pod.
All containers in a pod share the same stack, ip address and ports. All containers in a pod share the same lifecycle.
This yaml file contains the current pod, the name and the container image:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: quarkus-demo
spec:
  containers:
    - name: quarkus-demo
      image: quay.io/rhdevelopers/quarkus-demo:v1
```
We can apply the pod:
```bash
tom@tom-ubuntu:~/Projects/kubernetes-by-example/pod-example$ kubectl apply -f pod.yml
pod/quarkus-demo created
```
When we get the pods we have quarkus-demo running:
```bash
tom@tom-ubuntu:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-79f884648d-99lbd   1/1     Running   0          25m
myapp-79f884648d-fw9sw   1/1     Running   0          26m
quarkus-demo             1/1     Running   0          49s
```

We can delete the pod:
```bash
tom@tom-ubuntu:~$ kubectl delete pod quarkus-demo
pod "quarkus-demo" deleted

```
We can check by getting the pods:
```bash
tom@tom-ubuntu:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-79f884648d-99lbd   1/1     Running   0          27m
myapp-79f884648d-fw9sw   1/1     Running   0          28m
```

### What is a ReplicaSet?
Kubernetes uses a declarative approach. A ReplicaSet is a kubernetes abstraction which
tries to fulfil your request for a certain number of instances of your containers. If you creaete a ReplicaSet with two instances
kubernetes will deploy the instances across the available clusters.
This is a ReplicaSet:
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: rs-quarkus-demo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: quarkus-demo
  template:
    metadata:
      labels:
        app: quarkus-demo
        env: dev
    spec:
      containers:
        - name: quarkus-demo
          image: quay.io/rhdevelopers/quarkus-demo:v1
```

This creates two pods:
```bash
tom@tom-ubuntu:~/Projects/kubernetes-by-example/replica-set$ kubectl apply -f replica_set.yml 
replicaset.apps/rs-quarkus-demo created
```
We can see the two pods created:
```bash
tom@tom-ubuntu:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-79f884648d-99lbd   1/1     Running   0          33m
myapp-79f884648d-fw9sw   1/1     Running   0          34m
rs-quarkus-demo-9n4td    1/1     Running   0          15s
rs-quarkus-demo-n26zv    1/1     Running   0          15s
rs-quarkus-demo-p6lf6    1/1     Running   0          15s
```

If I delete a pod kubectl creates another pod to replace the deleted pod:
```bash
tom@tom-ubuntu:~$ kubectl delete po rs-quarkus-demo-9n4td
pod "rs-quarkus-demo-9n4td" deleted
tom@tom-ubuntu:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
myapp-79f884648d-99lbd   1/1     Running   0          35m
myapp-79f884648d-fw9sw   1/1     Running   0          36m
rs-quarkus-demo-9c8r6    1/1     Running   0          2s
rs-quarkus-demo-n26zv    1/1     Running   0          119s
rs-quarkus-demo-p6lf6    1/1     Running   0          119s
```
