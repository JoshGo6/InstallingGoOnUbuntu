# How To Install and Configure Go on Ubuntu 22.04.4 LTS

Google developed [Go](https://golang.org/) out of a need for a programming language that is uncomplicated, compiles quickly, and runs efficiently in production. Addtionally, while Go is a versatile, general-purpose language, it’s particularly well-suited for programs using networking or distributed systems, earning it a reputation as "the language of the cloud”. Go also simplifies formatting, by making the format of your code part of the language specification, and it streamlines deployment by compiling to a single binary. Learning Go is straightforward, with a small set of keywords, making it a good choice for beginners and experienced developers alike.

This tutorial guides you through installing and configuring a programming workspace with Go via the command line in an for Ubuntu 22.04.4 LTS  environment, but the principles described here apply to other Debian Linux distributions.

## Prerequisites

You will need a computer or virtual machine with Ubuntu 22.04.4 LTS installed, admin access to the machine, and an internet connection. You can download this operating system via the [Ubuntu 22.04.4 LTS release page](http://releases.ubuntu.com/jammy). Additionally, this tutorial assumes you are comfortable using Ubuntu at the command line. For more information about the Linux command line, see the [Introduction to the Linux Terminal](https://www.digitalocean.com/community/tutorials/an-introduction-to-the-linux-terminal) tutorial. 


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
cd ~
mkdir go
cd go
mkdir bin  
mkdir src
```

The `cd ~` changes the directory to your home directory. The `mkdir go` command creates a `go` directory there, and `cd go` navigates you to that directory. The last two `mkdir` commands then create two directories, `bin` and `src`, under that location, which results in the following directory structure:

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

When compiling your Go programs, you could type in full paths for the Go compiler and other files, but it's more convenient, as well as standard practice to use environment variables. Environment variables store full paths that can be referenced with the variable name, which becomes a shorthand.

As an example, the `$PATH` variable is an environment variable that your operating system uses to look for executable files and scripts when it can't find those files in the directory in which a command is run. When you run the Go compiler, you issue the command `go run`. Without an environment variable, you would have to run `/usr/local/go/bin/go run`. After setting up a few environment variables (including `$PATH`), you can type `go run`, instead.

There are several ways of setting the necessary environment variables, but we recommend setting them by editing your `.bashrc` file. If you are using a shell other than `bash`, such as `zsh`, edit the corresponding file (for example, `.zshrc`). Set the (environment) variables by doing the following:

1. Open the `.bashrc` file, which is located in your home directory, using nano:

    ```bash
    nano ~/.bashrc
    ```

1. Scroll to the bottom of the file and enter the following, beginning on a new line:

    ```bash
    export GOPATH="$HOME/go"
    export USERBINARIES="$GOPATH/bin"
    export GOBINARYPATH="/usr/local/go/bin"
    export PATH="$PATH:$GOPATH:$USERBINARIES:$GOBINARYPATH"
    ``` 
    This creates three new variables and updates the `$PATH` variable to include the relevant directories that Go uses. `GOPATH="$HOME/go"`, creates the first variable, `$GOPATH`, which is set to `$HOME/go`. This allows third party tools that look for `GOPATH` to know where your workspace is, so files can be automatically put there. Note that in setting `$GOPATH`, we have used another environment variable, `$HOME` and added `/go` to the end of that path. (At the command line, when you type `cd ~`, this is equivalent to typing `cd $HOME`. When setting environment variables, we cannot use the `~` shorthand.)

    `USERBINARIES` is created by next by taking the value of `$GOPATH` and appending `/bin` to the end of that path, resulting in `$USERBINARIES` being set to `$HOME/go/bin`. When Go compiles and installs tools, it puts them in this directory.

    The third new variable created is `GOBINARYPATH`, which is the path to the Go binary that you installed using `tar`. To allow you, Go, and third-party tools to run all Go programs from anywhere on your system, the last line of the file updates `$PATH` by appending the values of each of these new variables to the existing value of `$PATH`.

1. Save the edited `.bashrc` by entering `CTRL+O` and then pressing `Enter`, when prompted.

1. After `nano` confirms that the file has been written to, exit `nano` by entering `CTRL+X`.

1. To update your shell so that it can use the three new environment variables as well as the updated value of `$PATH`, run:

    ```bash
    source ~/.bashrc
    ```

    This instructs Ubuntu to reload `~/.bashrc` so the new environment variables and the updated `$PATH` are now accessible to the OS.

1. Verify that `$PATH` has been updated by running:

    ```bash
    echo $PATH
    ```

    ```bash
    Output
    . (Lines of text)
    .
    .   
    /home/test-user/go:/home/test-user/go/bin:/usr/local/go/bin
     ```

    If you are logged in as `root`, you would see `/root/go/bin` in the path.

1. Verify the installation by checking the current version of Go:
    ```bash
    go version
    ```
    
    You should receive output similar to the following, if the installation and environment variables are set correctly:

    ```bash
    go version go1.22.5 linux/amd64
    ```

1. Create a directory structure that contains a `GitHub` directory for source control, by running:

    ```bash
    go install github.com/digitalocean/godo@latest
    ```

    This installs the `Godo`, which is a Go client library for accessing the DigitalOcean V2 API (application programming interface).

    Your output may vary, but may look like the following:

    ```bash
    Output
    go: downloading github.com/digitalocean/godo v1.118.0
    go: github.com/digitalocean/godo@latest (in github.com/digitalocean/godo@v1.118.0):
    The go.mod file for the module providing named packages contains one or more replace directives. It must not contain directives that would cause it to be interpreted differently than if it were the main module.
    ```

1. Verify that the command installed `Godo` and created the directory structure by running:

    ```bash
    ls -lR1  
    ```

   If the installation was successful, the command generates a substantial amount of output:

    ```bash
    Output
    .
    .
    .
    /home/test-user/go/pkg/mod/github.com/digitalocean/godo@v1.118.0:
    total 1384
    -r--r--r-- 1 test-user test-user  2435 Jul  8 23:09 1-click.go
    -r--r--r-- 1 test-user test-user  1408 Jul  8 23:09 1-click_test.go
    -r--r--r-- 1 test-user test-user 23279 Jul  8 23:09 CHANGELOG.md
    -r--r--r-- 1 test-user test-user  2716 Jul  8 23:09 CONTRIBUTING.md
    -r--r--r-- 1 test-user test-user  2694 Jul  8 23:09 LICENSE.txt
    -r--r--r-- 1 test-user test-user  5712 Jul  8 23:09 README.md
    -r--r--r-- 1 test-user test-user  1898 Jul  8 23:09 account.go
    -r--r--r-- 1 test-user test-user  4021 Jul  8 23:09 account_test.go
    -r--r--r-- 1 test-user test-user  2598 Jul  8 23:09 action.go    
    .
    .
    .
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

1. Exit `nano` by pressing the `CTRL` and `X` keys. When prompted to save the file, press `Y` and then `ENTER`, which exits nano and returns you to the command prompt. 

1. Run `hello.go`:

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

