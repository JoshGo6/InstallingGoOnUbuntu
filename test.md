# **How To Install Go and Set Up a Local Programming Environment on Ubuntu 18.04**

![Go logo](./assets/images/image1.png "Go logo")

## Introduction

Google developed the programming language [Go](https://golang.org/) out of a need for a language that was easily programmable, compiled quickly, and runs efficiently in production. Addtionally, while Go is a versatile, general-purpose language, it’s particularly well-suited for programs using networking/distributed, earning it a reputation as "the language of the cloud”. Go also simplifies formatting, by making the format part of the language specification, and deployment, by compiling to a single binary. Learning Go is straightforward, with a very small set of keywords, making it a good choice for beginners and experienced developers alike.

This tutorial guides you through installing and configuring a programming workspace with Go via the command line in an for Ubuntu 18.04 environment, but the principles described here apply to other Debian Linux distributions.

## Prerequisites

You will need a computer or virtual machine with Ubuntu 18.04 installed, admin access to the machine, and an internet connection. You can download this operating system via the [Ubuntu 18.04 releases page](http://releases.ubuntu.com/releases/18.04/). Additionally, this tutorial assumes you are comfortable using Ubuntu at the command line. For more information about the Linux command line, see the [Introduction to the Linux Terminal](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) tutorial. 


## Download and verify installation file

To download and verify the Go installation file, do the following:

1. Navigate to your home (`~`) directory:

    ```bash
    cd ~
    ```

1. Copy the URL of the current binary tarball file from the [official Go downloads page](https://golang.org/dl/), and note the SHA256 hash listed next to it. You’ll use this hash to [verify the downloaded file](https://www.digitalocean.com/community/tutorials/how-to-verify-downloaded-files).

    
    ![Tarball file with SHA256 for verifying download](./assets/images/sha256-optimized.png "Tarball download")


1. Instead of using `sudo apt install` to install Go, use `curl` to retrieve the tarball from the URL you copied, which allows you to configure Go:

    ```bash
    curl -LO https://go.dev/dl/go1.22.5.linux-amd64.tar.gz 
    ```

    Using `-L0` is the equivalent of invoking both the `-L` option, which allows the server to complete the request if there is a URL redirect,  and the `-O` option, which writes the target (`go1.22.5.linux-amd64.tar.gz`) to a file of the same name in the directory (`~`) in which the `curl` command is run. In other words, this command creates the file `~/go1.22.5.linux-amd64.tar.gz`. As the command runs, the command line updates to show the status of the tarball downloading. When the download completes, the command line prompt reappears:

    ```bash
    Output
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100    75  100    75    0     0    398      0 --:--:-- --:--:-- --:--:--   401
    100 65.7M  100 65.7M    0     0  2989k      0  0:00:22  0:00:22 --:--:-- 3281k
    ```

1. Verify the download using `sha256sum`:

    ```bash
    sha256sum go1.22.5.linux-amd64.tar.gz
    ```
    The hash that is displayed from running this command should match the hash that was on the downloads page. If it does not, the file may have been corrupted, and you should download the file again.

    ```bash
    Output
    904b924d435eaea086515bc63235b192ea441bd8c9b198c507e85009e6e4c7f0  go1.22.5.linux-amd64.tar.gz
    ```

## Extract the tarball file

The tarball file is an archive file that contains other files and directories. After downloading it, extract and install its contents to your desired location. We recommend installation in `/usr/local`, which you can do by following these steps: 

1. Verify that Go is not yet  installed in `/usr/local`:

    ```bash
    ls /usr/local
    ```

   `ls` lists the contents of the directory supplied as an argument, `/usr/local`:

    ```bash
    Output
    aws-cli  etc    include  man   share
    bin      games  lib      sbin  src
    ```

    Since a `go` directory does not appear in the output, Go is not installed yet at this location. 
 

1. Use `tar` to extract the file from your home directory to `/usr/local`. 

    ```bash
    sudo tar -xz -f go1.22.5.linux-amd64.tar.gz -C /usr/local  
    ```

    `sudo` is the Ubuntu command that invokes admin privileges, which you need, since you are writing to a protected location that contains system files. `tar` is the utility that handles archive files, including ones that end in `tar.gz`. The `-xz` options indicate that `tar` should extract the file (`-x`) using the `gzip` (`-z`) algorithm. The `-f` option indicates that we will be supplying the filename, `go1.22.5.linux-amd64.tar.gz`. Finally, `-C` indicates the target directory in wich to extract the file, `/usr/local`. (There are [numerous sub-commands and options](https://www.howtogeek.com/248780/how-to-compress-and-extract-files-using-the-tar-command-on-linux/#extract-a-tar-file) that `tar` can handle.)

1. If the system asks for your admin password, enter it:

    ```bash
    [sudo] password for user: 
    ```

1. Check that the file was installed successfully in `/usr/local` by listing that directory's contents:

    ```bash
    ls /usr/local
    ```

    If the installation succeeded, the output of `ls` now shows the `go` directory:

    ```bash
    Output
    aws-cli  etc    go       lib  sbin   src
    bin      games  include  man  share
    ```

## Create your Go workspace

Now that Go is installed, create a programming workspace that contains two directories: `src` and `bin`. We recommend creating these directories directly under `~/go`. To do this, run the following: 

```bash
cd ~/go 
mkdir bin  
mkdir src
```

The `cd` commands navigates you to the correct directory (if you are not already in that directory). The two `mkdir` commands after that create the following directory structure:

```
└── ~
    └── go
        ├── bin
        └── src
```

## Understanding the workspace

* `src`: The directory that contains Go source files. A source file is a file that you write using the Go programming language. Source files are used by the Go compiler to create an executable binary file.
* `bin`: The directory that contains executables built and installed by the Go tools. Executables are binary files that run on your system and perform tasks. These are typically the programs compiled by your source code or other downloaded Go source code.

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

## Set environment variables

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

```bash
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
```

## Create and run a program

Now that you have configured the Go workspace, test it by creating and running a short “Hello, World!” program, by doing the following:

1. Navigate to `~/go/src`.

    ```bash
    cd ~/go/src
    ``` 
    
2. Use `nano` or another text editor to create a new file named `hello.go`.

    ```bash
    nano hello.go
    ```

3. When `nano` (or the editor of your choice) opens, store the following code in the `hello.go` file, which prints `Hello, World!` to your terminal, upon execution.

    ```go
    package main
    import "fmt"

    func main() {
        fmt.Println("Hello, World!")
    }   
    ```

In Go, every file belongs to a package. Since `hello.go` is not called by another package (which would make it a library), the file is  a standalone package, which always begins with `package main` as its first line. The next line imports the `fmt` library, since standalone packages are not called by other packages but *can* import libraries, which contain additional functionality. Importing `fmt` allows `hello.go` to use the functionality in that library instead of having to author that functionality all over again. The `fmt` library contains formatting functions, including `Println`, which prints text to the terminal. 

When the file runs, the system calls the `main` function first, which comes next in the file. This function, in turn, calls the `Println` function from the imported `fmt` package to print the string `Hello, World!` to the terminal. There is no more code in the file, so program execution completes at that point, and the system returns to the command prompot.

Exit `nano` by pressing the `CTRL` and `X` keys. When prompted to save the file, press `Y` and then `ENTER`, which exits nano and returns you to the command prompt. Now run `hello.go`:

```
go run hello.go
```

The terminal produces the following output:

```bash
Output
Hello, World!
```

If your system produced this output, you have now verified that your Go workspace is properly configured.

## Conclusion

Thanks for learning with the DigitalOcean Community. Check out our offerings for compute, storage, networking, and managed databases.
