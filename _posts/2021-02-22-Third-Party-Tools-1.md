---
layout: post
title: 'Knowledge Points of Third Party Tools'
subtitle: 'Linux terminal session record and playback tool: asciinema'
date: 2021-02-22
categories: Third Party Tools
cover: 'https://on2171g4d.bkt.clouddn.com/jekyll-theme-h2o-postcover.jpg'
tags: Third-Party-Tools asciinema
---

# Overview

---

The `asciinema` is a third party tool to recode and share your terminal sessions on the web. You can enjoy a lightweight, purely text-based approach to terminal recording. [Here](https://asciinema.org) is the asciinema official website.

# How to Install

---

There are several ways to get asciinema recorder, but in this blog only how to install via `pip`, other ways can check official docs: [Here](https://asciinema.org/docs/installation).  
## Installing via Pip
asciinema is available on PyPI and can be installed with pip(Python3 required):  
```shell
# Install asciinema for $USER
$ pip3 install --user asciinema
```
This is the universal installation method for all operating systems, which always provides the latest version.

> If the warning of "asciinema needs an ASCII or UTF-8 character encoding to run. Check the output of `locale` command" after you run `$ asciinema`, then you can run command `export LC_CTYPE="en_US.UTF-8"`. The solution is from [issue#160](https://github.com/asciinema/asciinema/issues/160).

# How to Use

---

asciinema is composed of multiple commands, similar to `git`, `apt-get` or `brew`.  
When you run `$ asciinema` with no arguments help messahe is displayed, listing all available commands with their options.
## asciinema rec [filename] : Recode terminal session
- By running `asciinema rec [filename]` you start a new recording session.  
    - If the `filename` argument is omitted then (after asking for comfirmation) the resulting recording (called asciicast) is uploaded to asciinema-server (by default to [asciinema.org](https://asciinema.org), you can set the server address yourself) where it can be watched and shared.  
    - If the `filename` argument is given then the asciicast is saved to a local file. It can later be uploaded to asciinema server with command `asciinema upload <filename>`.  
- By ending the shell(hit `Ctrl+D` or type `exit`) you stop the recording.  

> The command(process) that is recorded can be specified with `-c` option, and defaults to `$ HELL` which is what you want in most cases. If the recorded process is not a shell then recording finished when the process exits.

### Available options:
- --stdin :Enable stdin(keyboard) recording
> Stdin recording allows for capturing of all characters typed in by the user in the currently recorded shell.This may be used by a player (e.g. [asciinema-player](https://github.com/asciinema/asciinema-player)) to display pressed keys. It's disabled by default, and has to be explicitly enabled via `--stdin` option.
- --append :Append to existing recording
- --raw :Save raw STDOUT output, without timing information or other metadata
- --overwrite :Overwrite the recording if it already exists
- -c,--command=<command> :Specify command to record, defaults to $SHELL
- -e,--env=<var-names> :List of environment variables to capture, defaults to SHELL,TERM
- -t,--title=<title> :Specify the title of the asciicast
- -i,--idle-time-limit=<sec> :Limit recorded terminal inactivity to max <sec> seconds
- -y,--yes :Answer "yes" to all prompts(e.g. upload confirmation)
- -q,--quiet :Be quiet, suppress all notices/warnings(implies -y)

## asciinema play [filename] : Replay recorded asciicast in a terminal
This command replayed given asciicast (as recorded by `rec` command) directly in your terminal.  
It replays the recorded session using timing information saved in the asciicast.  
- Playing from a local file
```shell
asciinema play /path/to/asciicast.cast
```
- Playing from HTTP(S) URL
```shell
asciinema play https://asciinema.org/a/22124.cast
asciinema play http://example.com/demo.cast
```
- Playing from asciinema page URL
```shell
asciinema play https://ascinema.org/a/21124
asciinema play http://example.com/blog/post.html 
```
> requires `<link rel="alternate" type="/application/x-asciicast" href="/my/ascii.cast">` in page's HTML

- Playing from stdin
```shell
cat /path/to/asciicast.cast | asciinema play -
ssh user@host cat asciicast.cast | asciinema play -
```
- Playing from IPFS
```shell
asciinema play dweb:/ipfs/QmNe7FsYaHc9SaDEAEXbaagAzNw9cH7YbzN4xV7jV1MCzK/ascii.cast
```

### Available options:
- -i,--idle-time-limit=<sec> -Limit replayed terminal inactivity to max <sec> seconds
- -s,--speed=<factor> -Playback speed (can be fractional)

> Tip: For the best playback experience it is recommended to run `asciinema play` in a terminal of dimensions not smaller than the one used for recording, as there’s no “transcoding” of control sequences for new terminal size.

### Available Keyboard shortcuts
- `Space` -toggle pause
- `.` - step through a recording a frame at a time (when paused)
- `Ctrl+c` -exit

## asciinema cat [filename] : Print full output of recorded asciicast to a terminal
`asciinema cat existing.cast >output.txt` gives the same result as recording via `asciinema rec --raw output.txt`  

## asciinema upload [filename] : Upload recorded asciicast to asciinema.org site
This command uploads given asciicast (recorded by `rec` command) to asciinema.org, where it can be watched and shared.  
> `asciinema rec demo.cast` + `asciinema play demo.cast` + `asciinema upload demo.cast` is a nice combo if you want to review an asciicast before publishing it on asciinema.org.  

## asciinema auth : Link your install ID with your asciinema.org user account
To manage your recordings (change title/theme, delete) via command `asciinema auth`.  
This command displays the URL to open in a web browser to do that. You may be asked to log in first.  
Install ID is a random ID (UUID v4) generated locally when you run asciinema for the first time, and saved at `$HOME/.config/asciinema/install-id`.  
> Tip: A new install ID is generated on each machine and system user account you use asciinema on, so in order to keep all recordings under a single asciinema.org account you need to run asciinema auth on all of those machines.  
> Tip: asciinema versions prior to 2.0 confusingly referred to install ID as “API token”.  

# Configuration file

---
You can configure asciinema by creating config file at `$HOME/.config/asciinema/config`.  

## The structure of the configuratin file

Configuration is split into sections : [api], [record], [play].

### [api] section
- url = ___
    - API server URL, default: https://asciinema.org.  
    - If you run your own instance of asciinema-server then set its address here.  
    - It can also be overriden by setting `ASCIINEMA_API_URL` environment variable.  

### [record] section
- command = ___
    - Command to record, default: $SHELL
- stdin = ___
    - Enable stdin (keyboard) recording, default: no
- env = ___
    - List of environment variables to capture, default: SHELL,TERM
- idle_time_limit = ___
    - Limit recorded terminal inactivity to max n seconds, default: off
- yes = ___
    - Answer "yes" to all interactive prompts, default: no
- quiet = ___
    - Be quiet, suppress all notices/warnings, default: no

### [play] section
- speed = ___
    - Playback speed (can be fractional), default: 1
- idle_time_limit = ___
    - Limit replayed terminal inactivity to max n seconds, default: off

Configuration Example
```
[api]
url = https://asciinema.example.com
[record]
command = /bin/bash -l
stdin = yes
env = SHELL,TERM,USER
idle_time_limit = 2
yes = true
quiet = true
[play]
speed = 2
idle_time_limit = 1
```

> Config directory location can be changed by setting `$ASCIINEMA_CONFIG_HOME` environment variable.

> If `$XDG_CONFIG_HOME` is set on Linux then asciinema uses `$XDG_CONFIG_HOME/asciinema` instead of `$HOME/.config/asciinema`.

> asciinema versions prior to 1.1 used `$HOME/.asciinema`. If you have it there you should `mv $HOME/.asciinema $HOME/.config/asciinema`.