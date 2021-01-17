Like many people, I struggled to get "true color" and italic fonts working
in my tripartite combination of [alacritty](https://github.com/alacritty/alacritty),
[tmux](https://github.com/tmux/tmux) and 
[neovim](https://neovim.io/) on Mac OS.

The solution is in [this gist](https://gist.github.com/bbqtd/a4ac060d6f6b9ea6fe3aabe735aa9d95).

The source of the problem is that Mac OS ships with an old [ncurses](https://invisible-island.net/ncurses/)
library that does not have the [terminfo](https://en.wikipedia.org/wiki/Terminfo) for
alacritty and "tmux-256colors". I found also that the [homebrew](https://brew.sh/)
ncurses terminfo database, which is at `/usr/local/Cellar/ncurses/6.2/share/terminfo/`
on my machine, had a "tmux-256colors" entry, but it didn't work for me. Alas! 😭

So, I ended up following the instructions in that gist, putting those into the 
following [ansible](https://www.ansible.com/) recipe

```yaml
- name: Install terminfo for tmux-256color
  args:
    creates: "/Users/{{ ansible_user_id }}/.terminfo/74/tmux-256color"
  shell: |
    DIR=$(mktemp -d)
    if [ ! -d "$DIR" ]; then
      exit 1
    fi
    cd $DIR
    curl -LO https://invisible-island.net/datafiles/current/terminfo.src.gz
    gunzip terminfo.src.gz
    /usr/bin/tic -xe tmux-256color terminfo.src
    rm terminfo.src
    cd
    rmdir $DIR
```

And in my `.tmux.conf` the following

```
set  -g default-terminal 'tmux-256color'
set -ga terminal-overrides ',alacritty*:RGB:sitm=\E[3m'
```

To test everything is working OK, download [this shell
script](https://gist.githubusercontent.com/lifepillar/09a44b8cf0f9397465614e622979107f/raw/24-bit-color.sh)
and run it.  When you run that you should see a beautiful, smooth gradient
of colors across your terminal, without discernible borders between the colors.
Also try

```
echo -e "\e[3m foo \e[23m"
```

which should print "foo" in italics. Of course, you'll need to specify
an italic font for such cases. In my `alacritty.yaml` I have the following,
among other content

```yaml
font:
  # The size to use.
  size: 14
  offset:
    x: 0
    y: 2
  glyph_offset:
    x: -1
    y: 0
  # For retina only
  use_thin_strokes: true

  # The normal (roman) font face to use.
  normal:
    family: MesloLGMDZ Nerd Font
    # Style can be specified to pick a specific face.
    style: Regular

  # The bold font face
  bold:
    family: MesloLGMDZ Nerd Font
    style: Bold

  # The italic font face
  italic:
    family: MesloLGMDZ Nerd Font
    style: Italic
```
