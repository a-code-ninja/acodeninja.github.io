---
layout: post
title: dot files and the search for the perfect bash setup
---

## the prompt

What makes a good bash prompt?

Ubuntu 17.10 has a nice easy to look at prompt but it doesn't give you a lot of information.

```bash
lawrence@laptop:~$ _
```

It's nice to have some information relevent to your current task

```bash
[lawrence@laptop] [develop !? - lawrence@acode.ninja]
~/code/dotty > _
```

Or tasks you have backgrounded

```bash
[lawrence@laptop] [jobs: 1 running 2 stopped]
~/code/dotty > _
```

Or the exit code of your last command

```bash
[lawrence@laptop] [130]
~/code/dotty > _
```

The following functions give us all we need to put the information above into our
terminal prompt. You can find them in full [here](https://github.com/acodeninja/.dotty/blob/develop/modules/ps1/functions.sh)

```bash
#!/usr/bin/env bash

function nonzero_return() {
	...
}

function get_git_author() {
	...
}

function get_git_branch() {
	...
}

function get_git_dirty {
	...
}

function get_background_job_count {
	...
}

function get_load_average {
	...
}

function get_current_versions {
	...
}
```

We can then inject the information into our prompt with the following:

```bash
#!/usr/bin/env bash

THIS_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" && pwd )"

source $THIS_DIR/functions.sh

function generate_ps1 {
    local CURRENT_TERM_WIDTH="$(tput cols)"

    local COLOUR_GRAY="\033[1;30m"
    local COLOUR_LIGHT_GRAY="\033[0;37m"
    local COLOUR_CYAN="\033[0;36m"
    local COLOUR_RED="\033[0;31m"
    local COLOUR_YELLOW="\033[0;33m"
    local COLOUR_ORANGE="\033[1;31m"
    local COLOUR_NONE="\033[0m"

    local LAST_EXIT_CODE=$(nonzero_return)

    local USERNAME=$(whoami)
    local HOSTNAME=$(hostname)
    local GIT_CURRENT_BRANCH=$(get_git_branch)
    local GIT_CURRENT_AUTHOR=$(get_git_author)
    local BACKGROUND_JOBS=$(get_background_job_count)
    local GET_CLI_VERSIONS=$(get_current_versions)

    local TOP_LINE="\n$COLOUR_LIGHT_GRAY[$USERNAME@$HOSTNAME]$COLOUR_NONE"
          TOP_LINE="$TOP_LINE$COLOUR_CYAN$GIT_CURRENT_AUTHOR$COLOUR_NONE"
          TOP_LINE="$TOP_LINE$COLOUR_RED$BACKGROUND_JOBS$COLOUR_NONE"

    # if [ "$(tput cols)" -gt "100" ]; then
        TOP_LINE="$TOP_LINE$GET_CLI_VERSIONS"
    # fi

    local PROMPT="$(dirs)$COLOUR_YELLOW$GIT_CURRENT_BRANCH$COLOUR_NONE > "

    echo -e "$TOP_LINE\n$PROMPT"
}

export PS1="\`generate_ps1\`"
```

Using the above we get a really nice looking prompt with all the information you
could want at your fingertips.

![Nice looking prompt you have there]({{ "/assets/images/posts/2018-03-12/ps1-prompt.png" | absolute_url }})

## the aliases

Ubuntu includes some nice default aliases `ll` is an alias of `ls -al` and can have more arguments added to it `ll -h` works perfectly well.

### some i use

```bash
# colour grep output for ease of use
alias grep='grep --color=auto'
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'

# confirm file operations
alias mv='mv -i'
alias cp='cp -i'
alias ln='ln -i'

# handy ls aliases
alias ll='ls -al'
alias l='ls -l'
alias la='ls -a'

# handy php version switcher
function phpswitch {
    if [ "$(which php$1)" != "" ]; then
        alias php="php$1"
        echo "Switched to php$1 successfully"
    else
        echo "Could not find php$1"
    fi
}
```

## more to come

I will updating my .dotty project to include more and more useful bash shortcuts
and working towards the perfect setup as I go.

[.dotty - A set of bash terminal customisations geared toward developers working with linux.](https://github.com/acodeninja/.dotty)
