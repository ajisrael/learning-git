# Learning Git

## Commands

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

### Config

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

### Commits and SHAs

#### Log

```bash
git log --graph --decorate
```

Shows commit history with SHAs

Note: `--decorate` is only required when running `git log` and piping to a file to maintain the pretty syntax

#### Cat-File

Way to inspect files within git's data store

```bash
git cat-file -p <some-sha>
```

Echo out contents of sha

You can follow the trail from the commit, to the tree, to the blob, to get the file contents

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
