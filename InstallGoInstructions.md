# Install and Configure Go on Ubuntu

Google created [Go](https://golang.org/) out of a need for a simple yet powerful programming language. Go compiles quickly as a single binary, and it runs efficiently. While it's a good general-purpose language, it's particularly well-suited for networking and distributed systems, earning it a reputation as "the language of the cloud.” Learning Go is straightforward, since it contains a small set of keywords, making it a good choice for both beginner and experienced developers. This tutorial details the command-line installation and configuration of Go on Ubuntu 22.04.4 LTS, but the principles described here apply to other Debian Linux distributions, too. These topics are covered:

- [Prerequisites](#prerequisites)
- [Download and verify the installation file](#download-and-verify-the-installation-file)
- [Extract the tarball file](#extract-the-tarball-file)
- [Create your workspace](#create-your-workspace)
- [Set environment variables](#set-environment-variables)
- [Create and run a program](#create-and-run-a-program)

## Prerequisites

For this tutorial, you need a computer running Ubuntu 22.04.4 LTS and admin access, an internet connection, and a basic understanding of Linux command-line syntax.  Download Ubuntu via the [Ubuntu 22.04.4 LTS release page](http://releases.ubuntu.com/jammy). For more information about the Linux command line, see the [Introduction to the Linux Terminal](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) tutorial. 

## Download and verify the installation file

Begin by downloading and verifying the Go installation file. Using a web browser, navigate to the [official Go downloads page](https://golang.org/dl/), copy the URL of the current binary tarball file, and note the SHA256 hash listed next to it. You’ll use this hash to [verify the downloaded file](https://www.digitalocean.com/community/tutorials/how-to-verify-downloaded-files).

![Tarball file with SHA256 for verifying download](./assets/images/sha256-optimized.png "Tarball download")

Using the URL you copied, download the tarball to your home directory. 

```bash
cd ~
curl -L -O https://go.dev/dl/go1.22.5.linux-amd64.tar.gz 
```

`-L` allows the server to complete the request if there is a URL redirect,  and `-O` writes the target, `go1.22.5.linux-amd64.tar.gz`, to a file of the same name in your home directory. As `curl` runs, the command line shows the download progress of the tarball. When the download completes, verify the file using `sha256sum`:

```bash
sha256sum go1.22.5.linux-amd64.tar.gz
```
The displayed hash should match the hash listed on the downloads page. If it doesn't, the file may be corrupted, and you should download it again.

```bash
Output
904b924d435eaea086515bc63235b192ea441bd8c9b198c507e85009e6e4c7f0  go1.22.5.linux-amd64.tar.gz
```

## Extract the tarball file

The tarball is an archive file containing files and directories. After downloading it, extract and install its contents to your desired location. We recommend installation in `/usr/local`. First, verify that Go isn't yet  installed in `/usr/local`.

```bash
ls /usr/local
```

If a `go` directory does not appear in the output, Go isn't installed yet at this location. 

```bash
Output
aws-cli  etc    include  man   share
bin      games  lib      sbin  src
# Go does not appear in the output.
```

Use `tar` to extract the file from your home directory to `/usr/local`. 

```bash
sudo tar -xz -f go1.22.5.linux-amd64.tar.gz -C /usr/local  
```

The `-x` option indicates that `tar` should extract the file, `-z` specifies  using the `gzip` algorithm, and `-f` indicates that you'll be supplying the filename, `go1.22.5.linux-amd64.tar.gz`. Finally, `-C` specifies the target directory for extracting the file, `/usr/local`. If you're prompted for your admin password, enter it:

```bash
[sudo] password for user: 
```

Check that the file was installed successfully in `/usr/local`.

```bash
ls /usr/local
```

If the installation succeeded, the output of `ls` now shows the `go` directory:

```bash
Output
aws-cli  etc    go       lib  sbin   src
bin      games  include  man  share
```

After a successful installation, delete the tarball, since it's no longer needed. 

```bash
rm ~/go1.22.5.linux-amd64.tar.gz 
```

## Create your workspace

Now that you've installed Go, you can create your workspace, a directory structure containing the directories `bin`, `src`, and `pkg` inside your root directory. We recommend choosing `~/go` as your root directory.

```bash
mkdir -p ~/go/{bin,src,pkg}
```

The `-p` option creates the root `go` directory first, since it doesn't exist yet. Your workspace now looks like this:

```
└── $HOME       
    └── go
        ├── bin   # executables
        ├── src   # source files
        └── pkg   # third-party libraries
```

Each of these directories contains a different type of file:

- `bin`. Executables built and installed by the Go compiler from your source code. 
- `src`. Your Go source files. The Go compiler uses these to create the executables in `bin`. 
- `pkg`. Third-party libraries (packages) containing source code you can use in your Go projects.

## Set environment variables

Now update `PATH` to make it easier to run Go. To do this, we recommend  editing your `.bashrc` file. (If you aren't using bash, edit your shell's configuration file, for example `.zshrc`). Use `nano` to edit `.bashrc`.

```bash
nano ~/.bashrc
```

Scroll to the bottom of the file and enter the following, beginning on a new line:

```bash
export GOPATH="$HOME/go"
export USERBINARIES="$GOPATH/bin"
export GOBINARYPATH="/usr/local/go/bin"
export PATH="$PATH:$GOPATH:$USERBINARIES:$GOBINARYPATH"
``` 

The first three lines create the following new environment variables:

- `GOPATH`. Path to your workspace. This allows third-party tools that work with Go to find the root directory. 
- `USERBINARIES`. Path Go uses to create binaries that Go compiles from your source code. 
- `GOBINARYPATH`. Path to the Go compiler. 

To allow you, Go, and third-party tools to run Go programs easily from anywhere on your system, the final line added to `.bashrc` updates `PATH` to include these three new paths. Save the edited `.bashrc` in `nano` with `CTRL+O` and then `Enter`. After `nano` confirms you've written to the file, exit `nano` by typing `CTRL+X`. Next, update your shell to use the updated `PATH`.

```bash
source ~/.bashrc
```

Verify that `PATH` has been updated.

```bash
   echo $PATH
```

Typically, the output is fairly long, but the end of it should now contain the new paths for Go to use.

```bash
Output
. 
.
.   
/home/test-user/go:/home/test-user/go/bin:/usr/local/go/bin
 ```

Verify that the environment variables are working correctly by checking the current version of Go:

```bash
go version
```
    
Your output should be similar to the following: 

```bash
Output
go version go1.22.5 linux/amd64
```

## Create and run a program

Now that you've installed Go and created a workspace, you are ready to test it by creating and running a short program. Use `nano` to create the file `~/go/src/hello.go`.

```bash
nano ~/go/src/hello.go
```

When `nano` opens, enter the following in the file:

```go
package main
import "fmt"

func main() {
    fmt.Println("Hello, World!")
}   
```

Save the file with `CTRL+O` and then `Enter`. After `nano` confirms you've written to the file, exit `nano` by typing `CTRL+X`. Next, run `hello.go`.

```
go run ~/go/src/hello.go
 ```

The terminal displays the following output:

 ```bash
 Output
 Hello, World!
 ```

If your system displays this output, your Go installation is complete and functioning properly.
