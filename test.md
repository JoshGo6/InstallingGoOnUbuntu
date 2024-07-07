# **How To Install Go and Set Up a Local Programming Environment on Ubuntu 18.04**

![alt_text](./assets/images/image1.png "image_tooltip")

## Introduction

Google developed the programming language [Go](https://golang.org/) out of a need for a language that was easily programmable, compiled quickly, and executed efficiently in production. Addtionally, while Go is a versatile, general-purpose language, it’s particularly well-suited for programs using networking/distributed, earning it a reputation as "the language of the cloud”. Go also simplifies formatting, by making the format part of the language specification, and deployment, by compiling to a single binary. Learning Go is straightforward, with a very small set of keywords, making it a good choice for beginners and experienced developers alike.

This tutorial guides you through installing and configuring a programming workspace with Go via the command line in an for Ubuntu 18.04 environment, but the principles described here apply to other Debian Linux distributions.

## Prerequisites

You will need a computer or virtual machine with Ubuntu 18.04 installed, admin access to the machine, and an internet connection. You can download this operating system via the [Ubuntu 18.04 releases page](http://releases.ubuntu.com/releases/18.04/). Additionally, this tutorial assumes you are comfortable using Ubuntu at the command line. For more information about the Linux command line, see the [Introduction to the Linux Terminal](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) tutorial. 


## Download and install Go

To download and install Go, do the following:

1. Navigate to your home (`~`) directory:

    ```
    cd ~
    ```

1. Copy the URL of the current binary release tarball from the [official Go downloads page](https://golang.org/dl/), and note the SHA256 hash listed next to it. You’ll use this hash to [verify the downloaded file](https://www.digitalocean.com/community/tutorials/how-to-verify-downloaded-files).

1. Instead of using `sudo apt install` to install Go, use `curl` to retrieve the tarball from the URL you copied, which allows you to configure Go:

    ```
    curl -LO https://go.dev/dl/go1.22.5.linux-amd64.tar.gz 
    ```

    Using `-L0` is the equivalent of invoking both the `-L` option, which allows the server to complete the request if there is a URL redirect,  and the `-O` option, which writes the target (`go1.22.5.linux-amd64.tar.gz`) to a file of the same name in the directory (~) in which the `curl` command is executed. In other words, this command creates the file `~/go1.22.5.linux-amd64.tar.gz`. As the command runs, the command line updates to show the status of the tarball downloading. When the download completes, the command line prompt reappears:

    ```
    joshg@DESKTOP-L38CGNS:~$ curl -LO https://go.dev/dl/go1.22.5.linux-amd64.tar.gz
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100    75  100    75    0     0    398      0 --:--:-- --:--:-- --:--:--   401
    100 65.7M  100 65.7M    0     0  2989k      0  0:00:22  0:00:22 --:--:-- 3281k
    ```

1. Use `sha256sum` to verify the tarball:

sha256sum go1.12.1.linux-amd64.tar.gz

The hash that is displayed from running the above command should match the hash that was on the downloads page. If it does not, then this is not a valid file and you should download the file again.

Next, extract the downloaded archive and install it to the desired location on the system. It’s considered best practice to keep it under `/usr/local`:

sha256sum go1.12.1.linux-amd64.tar.gz

You will now have a directory called `go` in the `/usr/local` directory.

Note: Although `/usr/local/go` is the officially-recommended location, some users may prefer or require different paths.

In this step, you downloaded and installed Go on your Ubuntu 18.04 machine. In the next step you will configure your Go workspace.


## Step 2 — Creating Your Go Workspace

You can create your programming workspace now that Go is installed. The Go workspace will contain two directories at its root:

* `src`: The directory that contains Go source files. A source file is a file that you write using the Go programming language. Source files are used by the Go compiler to create an executable binary file.
* `bin`: The directory that contains executables built and installed by the Go tools. Executables are binary files that run on your system and execute tasks. These are typically the programs compiled by your source code or other downloaded Go source code.

The `src` subdirectory may contain multiple version control repositories (such as [Git](https://git-scm.com/), [Mercurial](https://www.mercurial-scm.org/), and [Bazaar](http://bazaar.canonical.com/)). This allows for a canonical import of code in your project. _Canonical_ imports are imports that reference a fully qualified package, such as `github.com/digitalocean/godo`.

You will see directories like `github.com`, `golang.org`, or others when your program imports third party libraries. If you are using a code repository like `github.com`, you will also put your projects and source files under that directory. We will explore this concept later in this step.

Here is what a typical workspace may look like:

.
├── bin
│   ├── buffalo                                      # command executable
│   ├── dlv                                          # command executable
│   └── packr                                        # command executable
└── src
    └── github.com
        └── digitalocean
            └── godo
                ├── .git                            # Git repository metadata
                ├── account.go                      # package source
                ├── account_test.go                 # test source
                ├── ...
                ├── timestamp.go
                ├── timestamp_test.go
                └── util
                    ├── droplet.go
                    └── droplet_test.go

The default directory for the Go workspace as of 1.8 is your user’s home directory with a `go` subdirectory, or `$HOME/go`. If you are using an earlier version of Go than 1.8, it is still considered best practice to use the `$HOME/go` location for your workspace.

Issue the following command to create the directory structure for your Go workspace:

mkdir -p $HOME/go/{bin,src}

The `-p` option tells `mkdir` to create all `parents` in the directory, even if they don’t currently exist. Using `{bin,src}` creates a set of arguments to `mkdir` and tells it to create both the `bin` directory and the `src` directory.

This will ensure the following directory structure is now in place:

└── $HOME
    └── go
        ├── bin
        └── src

Prior to Go 1.8, it was required to set a local environment variable called `$GOPATH`. `$GOPATH` told the compiler where to find imported third party source code, as well as any local source code you had written. While it is no longer explicitly required, it is still considered a good practice as many third party tools still depend on this variable being set.

You can set your `$GOPATH` by adding the global variables to your `~/.profile`. You may want to add this into `.zshrc` or `.bashrc` file as per your shell configuration.

First, open `~/.profile` with `nano` or your preferred text editor:

nano ~/.profile

Set your `$GOPATH` by adding the following to the file:

export GOPATH=$HOME/go

When Go compiles and installs tools, it will put them in the `$GOPATH/bin` directory. For convenience, it’s common to add the workspace’s `/bin` subdirectory to your `PATH` in your `~/.profile`:

export PATH=$PATH:$GOPATH/bin

This will allow you to run any programs you compile or download via the Go tools anywhere on your system.

Finally, you need to add the `go` binary to your `PATH`. You can do this by adding `/usr/local/go/bin` to the end of the line:

export PATH=$PATH:$GOPATH/bin:/usr/local/go/bin

Adding `/usr/local/go/bin` to your `$PATH` makes all of the Go tools available anywhere on your system.

To update your shell, issue the following command to load the global variables:

. ~/.profile

You can verify your `$PATH` is updated by using the `echo` command and inspecting the output:

echo $PATH

You will see your `$GOPATH/bin` which will show up in your home directory. If you are logged in as `root`, you would see `/root/go/bin` in the path.

You will also see the path to the Go tools for `/usr/local/go/bin`:

Verify the installation by checking the current version of Go:

go version

And we should receive output like this:

go version go1.12.1 linux/amd64

Now that you have the root of the workspace created and your `$GOPATH` environment variable set, you can create your future projects with the following directory structure. This example assumes you are using `github.com` as your repository:

$GOPATH/src/github.com/username/project

So as an example, if you were working on the `[https://github.com/digitalocean/godo](https://github.com/digitalocean/godo)` project, it would be stored in the following directory:

$GOPATH/src/github.com/digitalocean/godo

This project structure will make projects available with the `go get` tool. It will also help readability later. You can verify this by using the `go get` command and fetch the `godo` library:

go get github.com/digitalocean/godo

This will download the contents of the `godo` library and create the `$GOPATH/src/github.com/digitalocean/godo` directory on your machine.

You can check to see if it successfully downloaded the `godo` package by listing the directory:

ll $GOPATH/src/github.com/digitalocean/godo

You should see output similar to this:

drwxr-xr-x 4 root root  4096 Apr  5 00:43 ./
drwxr-xr-x 3 root root  4096 Apr  5 00:43 ../
drwxr-xr-x 8 root root  4096 Apr  5 00:43 .git/
-rwxr-xr-x 1 root root     8 Apr  5 00:43 .gitignore*
-rw-r--r-- 1 root root    61 Apr  5 00:43 .travis.yml
-rw-r--r-- 1 root root  2808 Apr  5 00:43 CHANGELOG.md
-rw-r--r-- 1 root root  1851 Apr  5 00:43 CONTRIBUTING.md
.
.
.
-rw-r--r-- 1 root root  4893 Apr  5 00:43 vpcs.go
-rw-r--r-- 1 root root  4091 Apr  5 00:43 vpcs_test.go

In this step, you created a Go workspace and configured the necessary environment variables. In the next step you will test the workspace with some code.


## Step 3 — Creating a Simple Program

Now that you have the Go workspace set up, create a “Hello, World!” program. This will make sure that the workspace is configured properly, and also gives you the opportunity to become more familiar with Go. Because we are creating a single Go source file, and not an actual project, we don’t need to be in our workspace to do this.

From your home directory, open up a command-line text editor, such as `nano`, and create a new file:

nano hello.go

Write your program in the new file:

package main

import "fmt"

func main() {
	fmt.Println("Hello, World!")
}

This code will use the `fmt` package and call the `Println` function with `Hello, World!` as the argument. This will cause the phrase `Hello, World!` to print out to the terminal when the program is run.

Exit `nano` by pressing the `CTRL` and `X` keys. When prompted to save the file, press `Y` and then `ENTER`.

Once you exit out of `nano` and return to your shell, run the program:

go run hello.go

The `hello.go` program will cause the terminal to produce the following output:

Hello, World!

In this step, you used a basic program to verify that your Go workspace is properly configured.


## Conclusion

Congratulations! At this point you have a Go programming workspace set up on your Ubuntu machine and can begin a coding project!

Thanks for learning with the DigitalOcean Community. Check out our offerings for compute, storage, networking, and managed databases.
