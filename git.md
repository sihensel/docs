# Git

A collection of Git commands and features that can be used as a quick reference.

## Git Commands

Command | Action
--- | ---
`git clone https://github.com/user/repo.git` | clones the repo using https
`git clone git@github.com:user/repo.git` | clones the repo using ssh
`git add -u` | adds all _modified_ files to the local git repo
`git commit -m "Commit Message"` | Commit the changes
`git push -u` | push the local branch and set it as upstream
`git pull --rebase origin master` | rebases the local branch to the remote master
`git rebase -i HEAD~3` | start interactive rebase for the last 3 commits
`git rebase -i <commit_hash>` | start interactive rebase starting __after__ the stated commit
`git rebase --onto main <branch>` | rebase a branch onto main
`git log` | show the Git log and who made which commits
`git diff` | show all unstaged changes
`git update-index --assume-unchanged <file>` | tell git to ignore any changes to a file

## Branches

Command | Action
--- | ---
`git switch -c branchName` | Create a new branch and switch to it
`git push -u origin branchName` | Push the new branch to remote and set it as upstream
`git fetch origin branchName` | Fetch a branch to local without merging/rebasing it
`git branch -d branchName` | Delete a local branch (use `-D` to force delete)
`git push origin --delete branchName` | Delete a branch on remote
`git branch -r` | list all remote branches.
`git branch -a` | shows the local list of all known branches (run `git fetch` before)
`git remote update origin --prune` | update the local list of remote branches

### Forks

Command | Action
--- | ----
`git remote -v` | shows all remote connections with their respective URL
`git remote add <name> <URL>` | adds a remote URL with \<name> (e.g. `upstream`) to the branch
`git fetch upstream` | fetch upstream remote changes into your fork
`git rebase upstream/main` | rebase you branch to upstream/main

### Amending commits

Sometimes it is necessary to edit a commit, either beacuse the message is incomplete or the author is incorrect.</br>
`git commit --amend` lets you edit the last commit message (this also works while rebasing).</br>
`git commit --amend --author="Author Name <email@address.com>" --no-edit` changes the author of the last commit message.</br>
It is also possible to `squash` or `fixup` commits with a rebase.

### Fixup old commit

It is possible to fixup a specific commit using its commit ID/Hash.
```sh
git add <FILE>                              # Stage the fix
git commit --fixup=<COMMIT_ID>              # Commit a fix for a specific commit
git rebase -i --autosquash <COMMIT_ID>~1    # rebase the fixup
```

### Undo a rebase

Check `git reflog` and search for the commit before the rebase and reset the branch to it. Suppose the old commit was `HEAD@{2}`:
```sh
git reset --hard HEAD@{2}
```

## .gitconfig

Use `git ignored` to list all files marked with `--assume-unchanged`.

```ini
[user]
    email = user@example.com
    name = Peter Griffin
[commit]
    verbose = true
[core]
    editor = nvim
[fetch]
    prune = true
[log]
    decorate = short
[status]
    showStash = true
[http]
    postBuffer = 157286400
[alias]
    ignored = !git ls-files -v | grep "^[[:lower:]]"
```

## Set up SSH with Git

### Generate a keypair

Generate the the ssh keypair (if you don't already have one).
```sh
ssh-keygen -t rsa -b 4096
```

Copy your public key (`~/.ssh/id_rsa.pub`) to Github.

### Working with repos

Change your directory to a local repo and run:
```sh
git remote set-url origin git@github.com:username/your-repository.git
```

Or clone a new repo with:
```sh
git clone git@github.com:username/your-repository.git
```

## Cleanup Git Repo

If your repo contains a lot of deleted binary files (e.g. images, compiled objects, etc.), your `.git` directory can become quite big, since Git still keeps track of them. These objects can be cleaned from git with the help of [BFG](https://rtyley.github.io/bfg-repo-cleaner/). This short guide follows [this post](https://www.tikalk.com/posts/2017/04/19/delete-binaries-from-git-repository/).

To find the biggest files in your repo, use the [git-tools](https://github.com/ivantikal/git-tools) scripts.
This step is optional, but can provide good insights.

```sh
git clone https://github.com/ivantikal/git-tools.git
```

To get the 50 biggest files, run

```sh
./git-tools/clean-binaries/get_biggest_files_in_history.sh -r ./repo_with_binaries/ -n 50
```

This script will write the output to a `.txt` file.
Afterwards, we can use `BFG` to clean our repo. To run this script, install the `jre-openjdk-headless` package (on Arch).</br>
Download the file (either via the website or with `curl`)

```sh
curl -LO https://repo1.maven.org/maven2/com/madgag/bfg/1.14.0/bfg-1.14.0.jar
```

Clone your repo, using the `--mirror` prefix, which loads all refs from Github.

```
git clone --mirror git@github.com:user/repo.github
```

This will create a directory with the string `.git` at the end. This is our target for `BFG`.
To delete all refs for files bigger that 50 MB, run

```sh
java -jar bfg-1.14.0.jar --strip-blobs-bigger-than 50M your_repo.git
```

Now that Git realizes that it keeps track of unnecessary files, run

```sh
cd your_repo.git
git reflog expire --expire=now --all && git gc --prune=now --aggressive
```

As a last step, perform a `git push` to update the remote repo.
Make sure to delete all old local copies of the repo and a do fresh clone, to avoid reintroducing old binaries into the git history.
