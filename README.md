## MyGit [![Paypal donate](https://www.paypalobjects.com/en_US/i/btn/btn_donate_LG.gif)](https://www.paypal.com/donate/?business=HZF49NM9D35SJ&no_recurring=0&currency_code=CAD)

Bash script doing git actions with group support.

### Table Of Content
<!-- TOC -->

- [Table Of Content](#table-of-content)
- [Who & Why](#who--why)
- [Features](#features)
- [Limitation](#limitation)
- [Usage](#usage)
  - [Configuration File](#configuration-file)
  - [Testing](#testing)
  - [Debug](#debug)
  - [Info](#info)
    - [remote](#remote)
    - [group](#group)
  - [Selector](#selector)
    - [-g/--group](#-g--group)
    - [-r/--remote](#-r--remote)
  - [Git Base Commands](#git-base-commands)
    - [init](#init)
    - [push](#push)
      - [--master](#--master)
      - [--all](#--all)
    - [fetch](#fetch)
  - [API Repo Commands](#api-repo-commands)
    - [Remote Information](#remote-information)
    - [Visibility Flags](#visibility-flags)
    - [new](#new)
    - [vis/visibility](#visvisibility)
    - [del/delete](#deldelete)
      - [Github](#github)
    - [desc/description](#descdescription)
    - [topic/topics](#topictopics)
    - [ls/list](#lslist)
  - [API Workflow Commands](#api-workflow-commands)
    - [List Workflow](#list-workflow)
    - [Dispatch](#dispatch)
- [New Repository Workflow](#new-repository-workflow)
- [Repository](#repository)
- [Contributors](#contributors)
- [Change Log](#change-log)
- [License](#license)

<!-- /TOC -->
<!--more-->

> [go-mygit](https://github.com/J-Siu/go-mygit) replaced this project.

### Who & Why

- Creating repositories for same set of remote servers repeatedly
- Setting up repositories on multiple machines repeatedly
- Working with repositories that push to same set of git servers

### Features

- Info
  - [x] debug
  - [x] remote
  - [x] group
- Selector
  - [x] -g/--group
  - [x] -r/--remote
- Git Base Commands
  - [x] fetch
  - [x] init
  - [x] push
    - [x] --master
    - [x] --all
- API Base Commands
  - [x] repo/repository
    - [x] --pri/--private
    - [x] --pub/--public
    - [x] del/delete
    - [x] ls/list
      - [x] --archive
    - [x] new
    - [x] vis/visibility
    - [x] desc/description
    - [x] topic/topics
  - [x] disp/dispatch
    - [x] list
    - [x] dispatch

### Limitation

- Strict option/argument order
- Minimum command line error detection
- Current supported git servers
  - gitea
  - github
  - gogs
- API commands must be executed at root of repository

### Usage

```sh
mygit version 0.2.3
License : MIT License Copyright (c) 2020 John Siu
Support : https://github.com/J-Siu/mygit/issues
Debug   : export _DEBUG=true
Usage   :
mygit remote                                                       # Show remotes in config
mygit group                                                        # Show groups in config
mygit [-g <group>] [-r <remote>] fetch                             # Git fetch
mygit [-g <group>] [-r <remote>] init [<repository name>]          # Git init
mygit [-g <group>] [-r <remote>] push [--all|--master]             # Git push
mygit [-g <group>] [-r <remote>] repo                              # API get remote repository information
mygit [-g <group>] [-r <remote>] repo del                          # API delete remote repository
mygit [-g <group>] [-r <remote>] repo desc "<description>"         # API change remote repository description
mygit [-g <group>] [-r <remote>] repo ls [--archive]               # API list all remote repository
mygit [-g <group>] [-r <remote>] repo new [--pri|--pub]            # API create remote repository
mygit [-g <group>] [-r <remote>] repo topic "<topics...>"          # API change remote repository topic
mygit [-g <group>] [-r <remote>] repo vis/visibility [--pri|--pub] # API change remote repository visibility
-- GitHub only --
mygit [-g <group>] [-r <remote>] wf|workflow                       # API workflow list
mygit [-g <group>] [-r <remote>] wf|workflow disp|dispatch <event> # API workflow dispatch
```

#### Configuration File

Following configuration will be used in all examples:

```sh
# This should be put in ~/.mygit.conf

# <remote name>

# MY_GIT[<remote name>.del]=""	# API token with delete scope. Mainly for Github.
# MY_GIT[<remote name>.grp]=""	# Group name of remote.
# MY_GIT[<remote name>.ssh]=""	# SSH hostname / SSH config host.
# MY_GIT[<remote name>.tok]=""	# API token.
# MY_GIT[<remote name>.uri]=""	# API URI.
# MY_GIT[<remote name>.usr]=""	# API username.
# MY_GIT[<remote name>.ven]=""	# Vendor: github|gites|gogs
# MY_GIT[<remote name>.pri]=""	# Private visibility: true|false. Default to true if not set.

MY_GIT[gh.del]="1234567890abcdef1234567890abcdef12345678"
MY_GIT[gh.grp]="external"
MY_GIT[gh.ssh]="git@github.com"
MY_GIT[gh.tok]="234567890abcdef1234567890abcdef123456789"
MY_GIT[gh.uri]="https://api.github.com"
MY_GIT[gh.usr]="username1"
MY_GIT[gh.ven]="github"
MY_GIT[gh.pri]="false"

MY_GIT[server2.grp]="internal"
MY_GIT[server2.ssh]="git@server2"
MY_GIT[server2.tok]="34567890abcdef1234567890abcdef1234567890"
MY_GIT[server2.uri]="https://server2/api/v1"
MY_GIT[server2.usr]="username2"
MY_GIT[server2.ven]="gitea"

MY_GIT[server3.grp]="internal"
MY_GIT[server3.ssh]="git@server3"
MY_GIT[server3.tok]="4567890abcdef1234567890abcdef1234567890a"
MY_GIT[server3.uri]="https://server3/api/v1"
MY_GIT[server3.usr]="username3"
MY_GIT[server3.ven]="gitea"
```

#### Testing

```sh
git clone https://github.com/J-Siu/mygit.git
cd mygit
cp mygit.conf ~/.mygit.conf
```

Example below can be tested inside `mygit` dir.

#### Debug

Use following to enable debug output:

```sh
export _DEBUG=1
```

#### Info

The `remote` and `group` command provide a quick way to see what is configured.

##### remote

To show all `remotes` configured:

```sh
mygit remote
```

Output:

```sh
gh (external)
server3 (internal)
server2 (internal)
```

##### group

To show all `groups` configured:

```sh
mygit group
```

Output:

```sh
external internal
```

#### Selector

`mygit` allow command applied to groups or remotes through the use of `-g/--group` and `-r/--remote`. This applies to all commands except `remote` and `group` mentioned above.

`-g/--group` and `-r/--remote` must be placed right after `mygit` and before any command.

##### -g/--group

```sh
mygit -g external <command>
mygit -g external -g internal <command>
```

##### -r/--remote

```sh
mygit -r gh <command>
mygit -r gh -r server3 <command>
```

`-g/--group` and `-r/--remote` can be used at the same time.

```sh
mygit -g external -r server2 <command>
```

#### Git Base Commands

`init`, `fetch`, `push` are git base commands.

##### init

`mygit init` will clear all existing remote and add remote base on `-g`/`-r` selector. If no group nor remote are specified, all configured remotes will be added.

`mygit init` by default use current directory name as repository name. Repository name can be specified in the format `mygit init <repository>`. File `.mygit` containing the repository name will be created, which is used by API based commands.

Before `mygit init`:

```sh
$ git remote -v
origin  https://github.com/J-Siu/mygit.git (fetch)
origin  https://github.com/J-Siu/mygit.git (push)
```

`mygit init` without selector:

```sh
$ mygit init
Reinitialized existing Git repository in /tmp/mygit/.git/

$ git remote -v
gh      git@github.com:/username1/mygit.git (fetch)
gh      git@github.com:/username1/mygit.git (push)
server2 git@server2:/username2/mygit.git (fetch)
server2 git@server2:/username2/mygit.git (push)
server3 git@server3:/username3/mygit.git (fetch)
server3 git@server3:/username3/mygit.git (push)
```

`mygit init` with group internal:

```sh
$ mygit --group internal init
Reinitialized existing Git repository in /tmp/mygit/.git/

$ git remote -v
server2 git@server2:/username2/mygit.git (fetch)
server2 git@server2:/username2/mygit.git (push)
server3 git@server3:/username3/mygit.git (fetch)
server3 git@server3:/username3/mygit.git (push)
```

`mygit init` with repository name:

```sh
$ mygit init mygit2
Reinitialized existing Git repository in /tmp/mygit/.git/

$ git remote -v
gh      git@github.com:/username1/mygit2.git (fetch)
gh      git@github.com:/username1/mygit2.git (push)
server2 git@server2:/username2/mygit2.git (fetch)
server2 git@server2:/username2/mygit2.git (push)
server3 git@server3:/username3/mygit2.git (fetch)
server3 git@server3:/username3/mygit2.git (push)
```

##### push

`mygit push` will do `git push` and `git push --tag` base on `-g`/`-r` selector. If no group nor remote are specified, all configured remotes will be pushed in sequence.

```sh
mygit push
```

```sh
mygit -r gh push
```

`mygit push` support options `--master` and `--all`

###### --master

If `--master` is used, `mygit push` will push to upstream(`-u`) master branch.

```sh
mygit push --master
```

###### --all

If `--all` is used, `mygit push` will push all branches(`--all`).

```sh
mygit -r gh push --all
```

##### fetch

`fetch` will do `git fetch` base on `-g`/`-r` selector. If no group nor remote are specified, all configured remotes will be fetched in sequence.

```sh
mygit fetch
```

```sh
mygit -r gh fetch
```

#### API Repo Commands

`mygit repo <command>` are API base command. With exception of `mygit repo ls/list`, all API commands must be ran at root of repository.

##### Remote Information

`mygit repo` without specifying additional command will retrieve repository information from remote server.

##### Visibility Flags

`--pri`, `--private`, `--pub`, `--public` are options for `repo new` and `repo vis` below.

##### new

`mygit repo new` will create repository on remote server.

By default repository will be created with private visibility. This can be override with `MY_GIT[<remote name>.pri]="false"` in configuration file `~/.mygit.conf` on a per remote basis. Or override with `--pri/--pub` in command line.

```sh
mygit repo new
```

```sh
mygit repo new --pub
```

```sh
mygit -g internal repo new
```

##### vis/visibility

`mygit repo vis` will get visibility status from remote.

```sh
mygit repo vis
mygit -g external repo visibility
```

Use `--pri/--pub` to change visibility settings on remote server.

```sh
mygit repo vis --pub
mygit -g external repo vis --pri
```

##### del/delete

> THERE IS NO CONFIRMATION FOR DELETION.

`mygit repo del` will delete repository from remote.

```sh
mygit repo del
mygit -g external repo delete
```

###### Github

Github token need `delete_repo` scope to delete repository through API. Either use a token with that scope in `MY_GIT[<remote name>.tok]` or a separate token and put it in `MY_GIT[<remote name>.del]`.

##### desc/description

`mygit repo desc` will get description of remote repository.

```sh
mygit repo desc
mygit -g external repo desc
```

`mygit repo desc "<description>"` can update description of remote repository. Description needs to be in double quote.

```sh
mygit repo desc "<description>"
mygit -g external repo desc "<description>"
```

##### topic/topics

`mygit repo topic` will get topics of remote repository.

```sh
mygit repo topic
mygit -g external repo topic
```

`mygit repo topic "topics ..."` can update topics of remote repository. Multiple topics needs to be in double quote.

```sh
mygit repo topic "topic1 topic2 topic3"
mygit -g external repo topic "topic1 topic2 topic3"
```

##### ls/list

`mygit repo ls` will list up to 100 repositories on remote server. This command does not depend on repository.

By default archived repositories are filtered out. Use `--archive` to show them also.

```sh
mygit repo ls
mygit repo ls --archive
mygit -g internal repo ls
mygit -r gh repo ls
```

#### API Workflow Commands

`mygit wf|workflow <command>` are API base workflow commands.

All workflow commands must be ran at root of repository.

> Workflow commands only work on GitHub repositories.

##### List Workflow

`mygit wf` will get all workflow information of a repository

```sh
mygit wf
mygit workflow
```

##### Dispatch

`mygit wf disp|dispatch <event>` will trigger a `repository_dispatch` event. Workflows file must configure accordingly.

Workflow file:

```yml
name: Release
on:
  push:
    tags:
      - "*"
  repository_dispatch:
    types: <event>
...
```

```sh
mygit workflow <event>
```

### New Repository Workflow

Assuming `~/.mygit.conf` is setup.

```sh
mygit init
mygit repo new
mygit push --master
```

### Repository

- [mygit](https://github.com/J-Siu/mygit)

### Contributors

- [John Sing Dao Siu](https://github.com/J-Siu)

### Change Log

- 0.1.0
  - Feature complete
- 0.2.0
  - README.md completed
  - add -g/-r checking
  - add init repository name support
  - add repo get info
  - add repo ls --archive flag
  - add usage
  - change push master/all to flag(--)
  - fix comment typo
  - fix repo del github del token logic
  - fix repo ls gogs/gitea support
  - fix repo new visibility logic
  - move desc, topic into repo
- 0.2.1
  - Improve debug log
  - change _mygit_group write to MY_GIT_GROUP directly
  - change _mygit_remote write to MY_GIT_REMOTE directly
  - change repo del allow independent of local remote
  - change shebang to #!/usr/bin/env bash
  - fix ARGP leak for push
  - fix _in_group call missing "" for group
  - fix _in_group call wrapped with $() incorrectly
  - fix _in_group var conflict, change _g -> _x
- 0.2.2
  - fix repo new visibility
- 0.2.3
  - add workflow list and event dispatch for GitHub

### License

The MIT License (MIT)

Copyright (c) 2020 John Siu

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
