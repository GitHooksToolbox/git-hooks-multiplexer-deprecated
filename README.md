<p align="center">
    <a href="https://github.com/GitToolbox/">
        <img src="https://cdn.wolfsoftware.com/assets/images/github/organisations/gittoolbox/black-and-white-circle-256.png" alt="GitToolbox logo" />
    </a>
    <br />
    <a href="https://github.com/GitToolbox/git-hook-multiplexer/actions/workflows/pipeline.yml">
        <img src="https://img.shields.io/github/workflow/status/GitToolbox/git-hook-multiplexer/pipeline/master?style=for-the-badge" alt="Github Build Status">
    </a>
    <a href="https://github.com/GitToolbox/git-hook-multiplexer/releases/latest">
        <img src="https://img.shields.io/github/v/release/GitToolbox/git-hook-multiplexer?color=blue&label=Latest%20Release&style=for-the-badge" alt="Release">
    </a>
    <a href="https://github.com/GitToolbox/git-hook-multiplexer/releases/latest">
        <img src="https://img.shields.io/github/commits-since/GitToolbox/git-hook-multiplexer/latest.svg?color=blue&style=for-the-badge" alt="Commits since release">
    </a>
    <br />
    <a href=".github/CODE_OF_CONDUCT.md">
        <img src="https://img.shields.io/badge/Code%20of%20Conduct-blue?style=for-the-badge" />
    </a>
    <a href=".github/CONTRIBUTING.md">
        <img src="https://img.shields.io/badge/Contributing-blue?style=for-the-badge" />
    </a>
    <a href=".github/SECURITY.md">
        <img src="https://img.shields.io/badge/Report%20Security%20Concern-blue?style=for-the-badge" />
    </a>
    <a href="https://github.com/GitToolbox/git-hook-multiplexer/issues">
        <img src="https://img.shields.io/badge/Get%20Support-blue?style=for-the-badge" />
    </a>
    <br />
    <a href="https://wolfsoftware.com/">
        <img src="https://img.shields.io/badge/Created%20by%20Wolf%20Software-blue?style=for-the-badge" />
    </a>
</p>

## Overview

Using git hooks has one small issue and that is that the hook can only be a single script. This means if you want to do more than one thing then your script can become very complex and difficult to manage.

With that in mind we have developed a simple multiplexer script. This script will replace the original git hook script and act as wrapper or proxy allow you to run multiple different scripts in sequence.

## How it works

You install the multiplexer as the required git hook, and when the hook fires it will then run all of the defined scripts in sequence for you.

Using a pre-commit hook as an example, set the multiplexer to run as your pre-commit hook (see the section on `installing the multiplexer`), and when you execute a `git commit` command the pre-commit hook will fire (be execute).

The multipler will then look in the following location for the actual real scripts to execute:

```shell
${GIT_ROOT}/hooks/pre-commit
```

> If you install the multiplexer as a post-commit hook it will look for GIT_ROOT/hooks/post-commit instead etc. for any supported hook.

It will then execute all of the scripts (in alphabetical order) in sequence. If a script fails it will notify you but will continue to run. If any of the scripts are non executable it will also notify you that it is skipping that script.

A final status repo is also shown once the complete execution finished.

```shell
Total Scripts: 7, Skipped: 1, Failed: 1, Status: Failed
```

The status is the cumulative status, so if a single script fails to overall impact is that it fails. Skipped scripts are for information only and a skipped script does not cound towards a failure. This allows you to `disable` a script without it having an impact, e.g. is the script is faulty and needs to be fixed.

### Installing the multiplexer

Copy the [script](src/multiplexer) to the .git/hooks/**hook name** at the root of the desired repository (and ensure that it is executable [*chmod +x]*). At this point the hook will run (fire) every time you attempt to commit a change to the repository.

#### Root is where?

If you are not sure where the root of the repository is, then execute the following from within any directory in your repository.

```shell
r=$(git rev-parse --git-dir) && r=$(cd "$r" && pwd)/ && cd "${r%%/.git/*}" && pwd
```

The above will give you the full path to the root of your repository no matter which directory you are in (even inside the .git directory or any of its subdirectories).

If you are sure you are in just a normal directory you could issue the following command instead. It is easier to remember but isn't going to work 100% of the time.

```shell
git rev-parse --show-toplevel
```

> If you see something similar to **fatal: this operation must be run in a work tree** then try the first command instead.

## What hooks are supported?

We took the decision to only support a very limited number of hooks in this initial version, but more hooks will be added in the future.

* pre-commit - The pre-commit script is executed every time you run git commit before Git asks the developer for a commit message or generates a commit object.
* prepare-commit-msg - The prepare-commit-msg hook is called after the pre-commit hook to populate the text editor with a commit message. 
* commit-msg - The commit-msg hook is much like the prepare-commit-msg hook, but it’s called after the user enters a commit message.
* post-commit - The post-commit hook is called immediately after the commit-msg hook.
