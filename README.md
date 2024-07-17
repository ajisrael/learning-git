# Learning Git

## Commands

### Basics

Covers about 80% of what you need.

#### Add

```bash
git add <path-to-file | pattern>
```

Adds zero or more files to index (staging area)

#### Commit

```bash
git commit -m '<message>'
```

Commits changes present in index

#### Status

```bash
git status
```

Describes the state of the repo. Inclueds tracked, staged, and untracked changes

### Config

Configuration is just a file. (Located at `.git/config` for local config)

#### Add a key value to git config

```bash
git config --add --global <key> "<value>"
```

Example:

```bash
git config --add --global user.name "Alex Israels"
```

#### View any value of git config

```bash
git config --get <key>
```

Example:

```bash
git config --get user.name
```

#### List config values

```bash
git config --list
```

#### Filter config values based on expression

```bash
git config --get-regexp <expression>
```

#### Remove (Unset) a config key value pair

##### Unset matching key

```bash
git config --unset <key>
```

Note: You cannot unset keys with multiple values with this command

##### Unset all matching keys

```bash
git config --unset-all <key>
```

#### Remove Namespace

```bash
git config --remove-section <key>
```

### Commits and SHAs

#### Log

```bash
git log --graph --decorate
```

Shows commit history with SHAs

Note: `--decorate` is only required when running `git log` and piping to a file to maintain the pretty syntax

```bash
git log --oneline
```

Really simplifies the logs to show you commits on a branch

```bash
git log --oneline <branch-name>
```

You can also append a branch name if you want to see the logs of a different branch than the one you're currently on.

#### Cat-File

Way to inspect files within git's data store

```bash
git cat-file -p <some-sha>
```

Echo out contents of sha

You can follow the trail from the commit, to the tree, to the blob, to get the file contents

### Repository

#### Initialize repository

```bash
git init
```

Creates a new `.git` folder for your git repo inside the current directory

#### Create new repo on command line

The following is from github itself not the FEM course

```bash
echo "# REPO_NAME" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:ajisrael/REPO_NAME.git
git push -u origin main
```

### Branching

#### Create a new branch

```bash
git branch <branch-name>
```

or 

```bash
git chechout -b <branch-name> 
```

#### Switch to another branch

```bash
git switch <branch-name>
git checkout <branch-name>
```

`checkout` is supposedly more versitile, and more commonly used.

#### Delete a branch

```bash
git branch -d <branch-name>
```

You can change `-d` to `-D` to --delete --force.

#### Merge branches

Attempting to combine two histories that may or may not have diverged. There is a common commit piont between the two, referred to as the best common ancestor (or "merge base" as defined in the docs).

Git merges the sets of commits on to the merge base and creates a new commit at the tip of the branch that is being merged on with all the changes combined into one commit.

There is a `target` branch, which is the one that you are on, and a `source` branch that you want to merge into your current branch.

```bash
git merge <source-branch-name>
```

Run the following command to see the changes done by the `merge` command in more detail

```bash
git log --oneline --decorate --graph --parents
```

Example Output:
```
*   7df8e11 d10ee81 67100a8 (HEAD -> trunk-merge-foo) Merge branch 'foo' into trunk-merge-foo
|\  
| * 67100a8 f8b02df (foo) C
| * f8b02df 07dc767 B
* | d10ee81 08bee70 (trunk) E
* | 08bee70 07dc767 D
|/  
* 07dc767 A
```

The merge commit has two parents (d10ee81 67100a8 in the example) one from the head of each branch from the merge

## Terms

### Commit

A point in time reprsenting the project in its entirety

The sha that represents a commit, 40 hexadecimal characters 0-f, calculated from contents of change, author, time, and more

### Index

Interchangable with staging area. This is the area in which you hold the changes you intend to commit.

### Squash

To take multiple commits and "squash" them into a single commit

### Work Tree

The git repo, there are some ways to track multiple branches (trees) of a repo by cloning a bare repo

### Remote

A copy of the repo on another machine that you can pull and push to.

### Keys

Keys are in the format: `<section>.<key>`
`--global` is for all future uses of git and repos
`user.name` and `user.email` are keys used to create commit tied to author

### Tree

A directory that contains a set of files or directories

### Blob

A file

### Parent

The commit or commits that bore the current commit

## Tidbits

### See all files created in `.git` from `git init`

```bash
find .git
```

## Notes

Git stores the entire state of the repo in the repository not just the difference between states.
It is an asyclic graph that happens to be linear.

Git allows you to store multiples of the same key with different values. This allows for global and local configs without conflict. Resolution depends on first occurance in a file at specific levels (local > global)

When you create a new branch you are creating it at the moment of your current branch. AKA it is part of the current commit, you can see this in `.git/refs/<branch-name>` as the contents of the file of your branch name are the commit from which the branch was created.

