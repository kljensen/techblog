
Homebrew now has a `bundle` command that can produce a Brewfile
that lists all the installed packages on your system, but not
their dependencies. That is, these are the _leaves_ in your
dependency graph. Using the bundle command you can easily remove
unused dependencies from your system:

```sh
brew bundle dump --file=- | brew bundle --force cleanup --file=- 
```

That first command dumps a Brewfile to stdout and the second command
consumes it, removing unused packages from your system.
