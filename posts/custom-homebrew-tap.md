
I manage my mac using [ansible](https://www.ansible.com/) keeping my
[configuration as
code](https://confluence.atlassian.com/bamboo/what-is-configuration-as-code-894743909.html)
and that code under version control with [git](https://git-scm.com/).  I make prolific use of `homebrew`
plugin for ansible in order to install software using
[homebrew](https://brew.sh/) recipes, both standard command-line software and
also MacOS GUI software using [homebrew's cask
system](https://github.com/Homebrew/homebrew-cask). My ansible task looks something
like as follows:

```yaml
- name: Install homebrew cask packages
  homebrew_cask:
    name: '{{ item }}'
    state: installed
  loop: '{{ homebrew_cask_packages }}'
  tags:
    - brew
    - cask
```

Above, the `homebrew_cask_packages` is an array of casks that I want installed.
Among those is the [Docker
Desktop](https://www.docker.com/products/docker-desktop) app, which I mostly
use for local development of containerized applications.  I noticed today that
the [homebrew docker desktop cask](
https://github.com/Homebrew/homebrew-core/blob/HEAD/Formula/docker.rb) installs
v19.03.13 of the docker desktop community edition. 
