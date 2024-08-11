# Install and configure Go on Ubuntu 22.04.4 LTS

Google developed [Go](https://golang.org/) out of a need for a simple yet powerful programming language. Go compiles quickly as a single binary, and it runs efficiently. While it's a good general-purpose language, it's particularly well-suited for networking and distributed systems, earning it a reputation as "the language of the cloud.” Learning Go is straightforward, since it contains a small set of keywords, making it a good choice for both beginner and experienced developers. This tutorial details command line installation and configuration of Go in Ubuntu 22.04.4 LTS, but the principles described here apply to other Debian Linux distributions, too.

## Prerequisites

For this tutorial, you need a computer running Ubuntu 22.04.4 LTS and admin access, an internet connection, and a good grasp of Linux command line syntax.  Download Ubuntu via the [Ubuntu 22.04.4 LTS release page](http://releases.ubuntu.com/jammy). For more information about the Linux command line, see the [Introduction to the Linux Terminal](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) tutorial. 

## Download and verify the installation file

Begin by downloading and verifying the Go installation file. Using a web browser, navigate to the [official Go downloads page](https://golang.org/dl/), copy the URL of the current binary tarball file, and note the SHA256 hash listed next to it. You’ll use this hash to [verify the downloaded file](https://www.digitalocean.com/community/tutorials/how-to-verify-downloaded-files).

![Tarball file with SHA256 for verifying download](./assets/images/sha256-optimized.png "Tarball download")

Using the URL you copied, download the tarball to your home directory. 

```bash
cd ~
curl -LO https://go.dev/dl/go1.22.5.linux-amd64.tar.gz 
```

`-L` allows the server to complete the request if there is a URL redirect,  and `-O` writes the target, `go1.22.5.linux-amd64.tar.gz`, to a file of the same name in your home directory. As `curl` runs, the command line updates to show the status of the tarball downloading. 

```bash
Output
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100    75  100    75    0     0    398      0 --:--:-- --:--:-- --:--:--   401
100 65.7M  100 65.7M    0     0  2989k      0  0:00:22  0:00:22 --:--:-- 3281k
```

When the download completes, verify the download using `sha256sum`:

```bash
sha256sum go1.22.5.linux-amd64.tar.gz
```
The hash that is displayed from running this command should match the hash that was on the downloads page. If it does not, the file may have been corrupted, and you should download the file again.

```bash
Output
904b924d435eaea086515bc63235b192ea441bd8c9b198c507e85009e6e4c7f0  go1.22.5.linux-amd64.tar.gz
```

## Extract the tarball file

The tarball is an archive file containining files and directories. After downloading it, extract and install its contents to your desired location. We recommend installation in `/usr/local`. First verify that Go is not yet  installed in `/usr/local`.

```bash
ls /usr/local
```

If a `go` directory does not appear in the output, Go is not installed yet at this location. 

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

The `-x` option indicate that `tar` should extract the file, `-z` specifies  using the `gzip` algorithm, and `-f` indicates that you'll be supplying the filename, `go1.22.5.linux-amd64.tar.gz`. Finally, `-C` specifies the target directory for extracting the file, `/usr/local`. If you're prompted for your admin password, enter it:

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

After a successful installation, delete the tarball, since it is no longer needed. 

```bash
rm ~/go1.22.5.linux-amd64.tar.gz 
```

## Create your workspace

Now that you have installed Go, you can create your workspace, a directory structure containing the directories `bin`, `src`, and `pkg` below the root directory. We recommend creating these directories directly under `~/go`.

```bash
mkdir -p ~/go/{bin,src,pkg}
```

The `-p` option creates the intermediate `go` directory first, since it doesn't yet exist. Your directory structure now looks like this:

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
- `pkg`. Third-party libraries (packages) containing source code you can use in your Go programs.

## Set environment variables

You will now add several environment variables and add to `PATH` to make it easier to run Go. To do this, we recommend  editing your `.bashrc` file (or, if you aren't using bash, editing the configuration file for your shell, for example .zshrc`). Set the environment variables by doing the following:

Open your `.bashrc` file using nano:

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

These four lines create the following new environment variables, and update `PATH` to include the paths stored in them:

- `GOPATH`. Path to your workspace. This allows third-party tools that work with Go to find your Go workspace to put packages there.
- `USERBINARIES`. Path Go uses to create binaries that Go compiles. 
- `GOBINARYPATH`. Path to the binary that runs the Go compiler. 

To allow you, Go, and third-party tools to run all Go programs from anywhere on your system, the final line updates `PATH` to include these three new paths.

Save the edited `.bashrc` by entering `CTRL+O` and then pressing `Enter`, when prompted. After `nano` confirms you've written to the file, exit `nano` by typing `CTRL+X`. Next update your shell to use the updated `PATH`.

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

Now that you've installed Go and created a workspace, you are ready to test it by creating and running a short program. Use `nano` to create a new file, `~/go/src/hello.go`.

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

Save the file by entering `CTRL+O` and then pressing `Enter`. After `nano` confirms that the file has been written, exit `nano` by entering `CTRL+X`. Next run `hello.go`:

```
go run hello.go
 ```

The terminal produces the following output:

 ```bash
 Output
 Hello, World!
 ```

If your system produces this output, your Go installation is complete and working.
