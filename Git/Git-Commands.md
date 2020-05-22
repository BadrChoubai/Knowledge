## Git Command-Line Interface

### Basics

- `git help <command>` : get help for a git command
- `git init` : creates a new git repo, with data stored in the `.git` directory
- `git status` : tells you what's going on
- `git add <filename>` : add files to staging area
- `git commit` : create a new commit
- `git log` : show flattened log of history
  - `git log --all --graph --decorate` : visualizes history as a DAG.  
- `git diff <filename>` : show differences since the last commit
- `git diff <revision> <filename>` : shows differences in a file between commits
- `git checkout <revision>` : updates HEAD and current branch

### Branching and Merging

- `git branch` : shows branches
- `git branch <name>` : creates a branch
- `git checkout -b <name>` : creates a branch and switches to it
- `git merge <revision>` : merges into current branch
  - `git mergetool` : use a fancy tool to resolve merge conflicts
- `git rebase` : rebase set of patches onto a new base.

### Remotes

- `git remote` : list remotes
- `git remote add <name> <url>` : add a remote
- `git push <remote> <local branch>:<remote branch>` : send objects to remote and update remote reference
  - `git fetch` : retrieve objects/references from a remote
  - `git pull` same as `git fetch; git merge`

### Undo

- `git commit --amend` : edit a commit's contents/message
- `git reset HEAD <file>` : unstage a file
- `git checkout -- <file>` : discard changes
