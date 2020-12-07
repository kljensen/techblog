I find myself often downloading files from a web browser, e.g. in
my email, and soon thereafter wanting to move those files from the
place where they've been downloaded to where they're needed, typically
on the command line. After wasting untold sum hours on this over
many years, I finally decided to make that process more ergonomic
with this little shell function.


```zsh
mvd () {
    # The destinations defaults to the current directory, "."
    DESTINATION=${1:-.}
  
    # List, select, and move files to the destination
    /bin/ls -t $HOME/Downloads | \
        fzf |\
        sed "s@^@$HOME/Downloads/@"| 
        tr "\n" "\0"| \
        xargs -t -0 -J % mv "%" $DESTINATION
}
```

I've called the function `mvd` for "move from downloads". The function can be
called like `mvd foo` in which case it presents me with an interactive dialog
(via [fzf](https://github.com/junegunn/fzf)) through which I can choose which
files I want from the `~/Downloads` folder and it moves those into the current
working directory. Alternatively, if I pass a destination directory like `mvd
/var/foo`, it will move the selected files into the `/var/foo` directory.

I give the `ls` command the `-t` argument so that the most recently modified
files are at the top. These are typically the files I most recently downloaded.
The `tr` command and the `-0` argument to `xargs` are needed to handle
filenames that have spaces in them. 
