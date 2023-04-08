---
title: gitconfig
---
```ini
[alias]
    # status
    g = status

    # stage
    s = add

    # unstage
    u = restore --staged

    # commit-create: creates a new commit.
    cc = commit

    # commit-amend: amends the last commit.
    ca = commit --amend

    # commit-empty: creates an empty commit.
    ce = commit --allow-empty

    # branch-delete: deletes a branch.
    bd = !git branch -D

    # branch-rename: renames a branch.
    br = !git branch -m

    # fetch-branch: fetches a branch read from input from the remote and checks it out.
    fb = !git fetch $(git remote) $1 && git checkout -b $1 $(git remote)/$1 && :

    # push-branch: pushes the current branch to its push remote.
    pb = !git push $(git remote) $(git branch --show-current)

    # branch-spinoff: creates a new branch based on a remote branch using the push remote,
    #     it first checks if there is any change that isn't committed. Example: git bs feat master,
    #     will create a branch named feat based on <remote>/master.
    bs = "!if [[ -z $(git status -s) ]]; then \
               if [[ -z "$1" || -z "$2" ]]; then \
                   echo \"usage: git bs <new-branch-name> <remote-branch-to-spinoff-from>\"; \
               else \
                   git fetch $(git remote) $2 && \
                   git checkout -b $1 $(git remote)/$2; \
               fi; \
           else git status; fi" && :

    # stash-create: creates a stash of the index and working tree. The stash name includes the
    #    current date and time stamp, and it takes an optional description read from input.
    #    Additionally, it cleans up all the changes.
    zc = "!git stash push --include-untracked --message \"$(date +'%a %b %d %H:%M') $1\" && \
            git reset --hard HEAD -- && git clean --force -d" && :

    # stash-apply: applies a stash to the working tree. It tries to preserve the stash index.
    #    It takes an optional stash index to apply read from input.
    za = !git stash apply --index $1 && :

    # stash-list: lists stashes.
    zl = !git stash list

    # rebase-upstream: rebases the current branch from the upstream branch.
    ru = !git fetch $(git remote) $(git branch --show-current) && \
            git rebase $(git remote)/$(git branch --show-current) && :

    # rebase-interactive: starts an interactive rebase to change the last number of commits read
    #    from input.
    ri = !git rebase -i HEAD~$1 && :

    # log-branch: shows one line log for the current branch, read from input the number of commits
    #    to display otherwise it defaults to the last commit.
    lb = "!if [[ -z $1 ]]; then \
               git log --pretty=oneline -1; \
           else \
               git log --pretty=oneline -$1; \
           fi" && :
    # log-upstream: shows one line log for the upstream branch, read from input the number of
    #    commits to display otherwise it defaults to the last commit.
    lu = "!if [[ -z $1 ]]; then \
               git fetch $(git remote) && \
               git log --pretty=oneline @{u} -1; \
           else \
               git fetch $(git remote) && \
               git log --pretty=oneline @{u} -$1; \
           fi" && :
[core]
    # set the vi editor to create and edit your commit and tag messages.
    editor = vi
[pull]
    # will update your branch only if it can be "fast-forwarded" without creating new commits.
    ff = only
[push]
    # push the current branch to a branch of the same name, avoids setting up the upstream branch
    # each time, and helps prevent pushing current branch changes to other branches by mistake.
    default = current
[include]
    # place all projects under ~/workspace and have in there a .gitconfig with workspace specific
    # git configurations like user, email, defaultBranch, etc.
    path = ~/workspace/.gitconfig
```