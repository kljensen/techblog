---
software:
  - name: neovim
    version: v0.4.4
  - name: pass
    version: v1.7.3 
  - name: zsh
    version: 5.7.1 
---
This article is about how to tweak your vim/neovim settings using environment
variables.

This morning I was using a shell script
(the [pass](https://www.passwordstore.org/, which I use as a journal/diary) password store)
that employed the `EDITOR` environment
variable in order to edit some content.
I realized that I
wanted my editor, [neovim](https://neovim.io/) to do automatic line wrapping
when opened from this shell script.
The file types that `pass` opens have a `.txt`
extension, but I don't want line wrapping in `.txt` files generally.  So, I
tried to set `EDITOR` to `nvim -c 'set wrap|set textwidth=20|set
formatoptions+=t'` in a small wrapper around `pass`. The `-c` option [sets some vim options via the command
line](https://vi.stackexchange.com/questions/2973/setting-a-vim-option-from-the-command-line).
But, this didn't work when [called from the pass shell script](https://git.zx2c4.com/password-store/tree/src/password-store.sh#n501)
because of how my shell split the arguments. 

Next I thought about [setting the vim file
type](https://vim.fandom.com/wiki/Filetype.vim) using the _path_ of the file,
rather than merely the extension (`.txt`). But, that seemed like a bit of a
pain because it seemed like vim/neovim's file type recognition system uses
globs instead of regular expressions.

Then, I considered setting the [`VIMINIT` environment
variable](http://vimdoc.sourceforge.net/htmldoc/starting.html) but if `VIMINIT`
is set, none of your `.vimrc` or your `$XDG_CONFIG_HOME/nvim/init.vim` scripts
will be loaded: `VIMIT` replaces them and I couldn't find an environment
variable for vim/neovim that _supplements_ my configuration files.

In the end, I decided to write a wrapper around `pass`, called something like `klj_journal`
that calls `pass` and sets the `KLJ_JOURNAL` environment variable to some non-empty value.
Then, in my vim/neovim configuration file, I have a stanza as follows

```vimscript
if exists('$KLJ_JOURNAL')
    set wrap
    set textwidth=80
    set formatoptions+=t
    set wrapmargin=0
    echo "Turned on options for KLJ journal"
endif
```

This stanza checks for the existence of `KLJ_JOURNAL` in the environment
and then sets my line wrapping preferences accordingly. It feels like a
decent solution.


