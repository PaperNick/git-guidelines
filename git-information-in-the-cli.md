# Git information in the CLI

### Motivation

The purpose of this wiki page is to guide you through the process of configuring your command-line interface (CLI) to show git information regarding the current project like:

* What branch are you currently on
* Is a merge in progress
* Is a rebase in progress
* And many more

### Showcase

After configuring your CLI, the following info is going to be shown when you're in a folder which uses git:

```
user@host:~$ cd project
user@host:~/project (master)$
user@host:~/project (master)$ git switch feature
user@host:~/project (feature)$
user@host:~/project (feature)$ git rebase master
user@host:~/project (feature|REBASE 1/2)$

user@host:~/project (feature)$ git merge master
user@host:~/project (feature|MERGING)$
```

* As soon as we navigated into the `project` directory, the CLI started showing git information like what branch we're currently on.

* As soon as we switched to the `feature` branch, the CLI printed `(feature)`.

* As soon as we initiated a rebase, the CLI printed rebase-related information showing on which step we're currently on, and how many steps remain - `(feature|REBASE 1/2)`.

## Configuration

### Linux

In the next steps, we're going to configure `Ubuntu 19.10` to show git information in the CLI. But keep in mind that those instructions are not exclusive to Ubuntu. If you're running a different distribution, the process is going to be pretty much the same.

1. Make sure you have `git` installed:

    ```
    sudo apt install git
    ```

2. Open your `~/.bashrc` file with an editor of your choice. I use vim in this example.

    ```
    vim ~/.bashrc
    ```
3. In this file, navigate to the `PS1` variable

    3.1 Here's how it originally looks:

    ```
    if [ "$color_prompt" = yes ]; then
        PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
    else
        PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
    fi
    ```

    3.2 You might notice that there are two `PS1` variables defined based on a condition

    3.3 Since we know that our CLI supports colors, we're going to modify the first `PS1` variable in the `if` condition

4. Add git information to your `PS1`

    4.1 Add `$(__git_ps1)` in your `PS1` variable

    4.2 We're going to add this at the end of our `PS1`

    ```
    PS1='...........[\033[00m\]$(__git_ps1)\$ '
    ```

    4.3 Here's how it looks after adding it:

    ```
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]$(__git_ps1)\$ '
    ```

5. Reloading the terminal session

    5.1 In order to view the changes that we've just applied, you need to reload your terminal session.

    5.2 Closing and opening a new terminal window should load the latest changes

    5.3 If you don't want to close your terminal session but want to see the latest changes, simply `source` your `.bashrc` file:

    ```
    source ~/.bashrc
    ```

6. That's it! After navigating to your project, you should see git information in the CLI.

#### Additional resources

If you wonder how exactly does `$(__git_ps1)` show information in the CLI, follow the explanation below.

In bash, `$()` is called command substitution. Basically, the expression in the brackets is evaluated, and the output from the expression is shown.

https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html

In our case, we're simply calling the bash function `__git_ps1` which prints the git information.

If you wonder what the code of the function `__git_ps1` is, you can find it here:

https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh

#### Troubleshooting

**Q:** After adding this snippet in `PS1`, I receive an error that `__git_ps1` is not defined.

**A:** You either don't have `git` installed, or for some reason, the `git-prompt.sh` script was not loaded by your shell.

You can manually download it from here and `source` it before calling the `__git_ps1` function is your `PS1`:

https://github.com/git/git/blob/master/contrib/completion/git-prompt.sh

```
vim ~/.bashrc
...
...
...
source git-prompt.sh
...
...
PS1='...........[\033[00m\]$(__git_ps1)\$ '
```
