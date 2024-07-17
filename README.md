# Learning Git

## Commands

### Create new repo on command line

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
