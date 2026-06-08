# Git Cheat Sheet (Organized)

## 1. Check Current Branches
Show all local branches:

```bash
git branch
```

---

# 2. Switching Branches

### Switch to an existing branch

```bash
git checkout <branch-name>
```

or using the newer command:

```bash
git switch <branch-name>
```

---

### Create and switch to a new branch

Using `checkout`:

```bash
git checkout -b <branch-name>
```

Using `switch`:

```bash
git switch -c <branch-name>
```

---

# 3. Stage Changes

### Add a specific file

```bash
git add <file-name>
```

### Add all changes

```bash
git add .
```

---

# 4. Commit Changes

Save staged changes with a message:

```bash
git commit -m "Your commit message"
```

---

# 5. Push Changes

### Push to a specific branch

```bash
git push -u origin <branch-name>
```

### If the branch does not exist remotely yet

```bash
git push --set-upstream origin <branch-name>
```

---

# 6. Remote Repository Management

## Check the current remote URL

```bash
git remote -v
```

---

## Change or set the remote repository URL

```bash
git remote set-url origin git@github.com:ZeroNeroIV/Code-Search-Engine.git
```

Verify the change:

```bash
git remote -v
```

---

# 7. Remove a File from Git Tracking

Stop tracking a file while keeping it locally:

```bash
git rm --cached <file-name>
```

Useful when:
- You accidentally uploaded a file
- You later add it to `.gitignore`

---

# 8. Git Configuration

## Set your email globally

```bash
git config --global user.email "your-email@example.com"
```

---

# 9. Undo the Most Recent Commit

## Keep file changes locally

```bash
git reset --soft HEAD~1
```

---

## Remove the commit and file changes completely

```bash
git reset --hard HEAD~1
```

⚠️ Warning: `--hard` permanently deletes uncommitted changes.

---

# 10. View an Older Commit

Show detailed information about a commit:

```bash
git show <commit-id>
```

---

# 11. Go Back to a Previous Commit

## Case 1 — Temporarily View an Old Commit (Safe)

```bash
git checkout <commit-hash>
```

- Does not change history
- Enters detached HEAD mode
- Useful for inspecting old code

---

## Case 2 — Revert Changes While Keeping History (Recommended)

Creates a new commit that undoes changes:

```bash
git revert <commit-hash>
```

Recommended if the commit was already pushed to GitHub.

---

## Case 3 — Hard Reset to an Older Commit

Completely removes newer commits and changes:

```bash
git reset --hard HEAD~<number-of-commits>
```

Example:

```bash
git reset --hard HEAD~2
```

⚠️ Warning:
- This rewrites history
- Can permanently delete commits
- Be careful if working with others


## See hash for revisions (Pointers or objects)

```
git rev-parse HEAD 
```

## Content for any objects 
```
git cat-file <hash> 
```

* `t`: type for the object. 
* `p`: print the content for the object.

## Recover yourself (log)
```
git reflog show
```
* Will show you all the logs and moves (hashes) you did in your repo (specifically what happened with HEAD), and you can use to recover from any mistake you did. 

> You can use `git switch -C <branch>` to any branch including main points to HEAD, useful when deleting content and recovering.

### What if you delete a file
* `rm <file>.txt`
```
git restore .
```
* Restore from a specific commit.
```
git restore --source <hash_file> <file_name_deleted> 
```


## Merging approaches

### Merge 
```
git switch main
git fetch origin 
git merge origin/main
```
* When the branches have diverged, Git creates a new merge commit that has both branches as parents.
```
Before: 

A---B---C main
     \
      D---E feature

After: 
A---B---C--------M main
     \          /
      D---E---- feature
```

### Rebase 
```
git switch feature_branch
git rebase <target branch(top of it)>
git rebase main

```
* Advanced merge, instead of making a new commit, you take a whole commits of a branch (feature branch), and add them in-front of the main one, so the history of it will be linear. 
* This is very useful if u want to make several features for a main branch without the main history being affected.

```
Before: 

A---B---C main
     \
      D---E feature
After: 
A---B---C---D'---E' feature
```
### Squash 
`git rebase -i`

* An interactive window will appears after.

| Command  | Meaning                             |
| -------- | ----------------------------------- |
| `pick`   | keep commit normally                |
| `squash` | combine with previous commit        |
| `fixup`  | combine and discard message         |
| `reword` | keep commit but edit message        |
| `drop`   | remove commit                       |
| `edit`   | stop during rebase to modify commit |
* squash combines multiple commits into a single commit.
* It’s commonly used to clean up a feature branch before merging it into main.

==> After editing another window will appear for commit message.
```
Suppose your feature branch has many small commits:
A---B---C main
         \
          D---E---F feature
Where:

D = initial feature
E = fix typo
F = debugging changes

You may not want all these commits in the main history.

git checkout main
git merge --squash feature
git commit

Result: 
A---B---C---S main
```


| Operation | What happens                             |
| --------- | ---------------------------------------- |
| Merge     | Combines histories with a merge commit   |
| Rebase    | Replays commits on top of another branch |
| Squash    | Combines multiple commits into one       |



### Squash merging 

* Squash and merge into main directly.
```
git merge --squash feature
git commit
```

Result: 
```
A---B---C---S
```

It’s basically:

“Take all changes from this branch and make them one commit.”

## Git submodule 

A submodule lets one repository include another repository at a specific commit.

```
git submodule add -f https://github.com/user/other-repo.git external/other-repo
git commit -m "Add other-repo as submodule"
```


--- 

--- 
---


# Git Bisect - Complete Guide with Examples

## What is Git Bisect?

Git bisect is a binary-search tool built into Git that helps you find the exact commit that introduced a bug.

Instead of manually checking every commit, Git repeatedly chooses the middle commit between a known good commit and a known bad commit, reducing the number of tests dramatically.

---

## Why Use Git Bisect?

If you have 100 commits between a working version and a broken version, checking commits one-by-one could require 100 tests.

Git bisect uses binary search and can often find the offending commit in about:

```text
log2(100) ≈ 7 tests
```

---

## Basic Workflow

1. Start bisect:

```bash
git bisect start
```

2. Mark current commit as bad:

```bash
git bisect bad
```

3. Mark a known working commit:

```bash
git bisect good <commit-hash>
```

4. Test commits selected by Git.

5. Mark each tested commit as good or bad:

```bash
git bisect good
```

or

```bash
git bisect bad
```

6. Git identifies the first bad commit.

7. End the session:

```bash
git bisect reset
```

---

## Example Repository History

Imagine the following commit history:

```text
fed4567  Latest commit (BAD)
89ab123  More changes (BAD)
cd1d4c4  Added file2 and file3 (BUG INTRODUCED)
377fd8e  Added file5 (GOOD)
a3e9a53  Initial project (GOOD)
```

The bug first appears in commit:

```text
cd1d4c4
```

---

## Example Session

Start bisecting:

```bash
git bisect start
git bisect bad
git bisect good a3e9a53
```

Git checks out:

```text
377fd8e
```

You test the code and determine it works:

```bash
git bisect good
```

Git responds:

```text
cd1d4c4f3a83f40fb20da6ad13000ea6e539c3fe is the first bad commit
```

---

## Expected Git Log

```bash
git log --oneline
```

Output:

```text
fed4567 Latest changes
89ab123 More changes
cd1d4c4 Totall edit, fixing and making it beautiful
377fd8e file5
a3e9a53 Initial commit
```

---

## Understanding the Result

When you marked `377fd8e` as good, Git knew:

- `a3e9a53` is good
- `377fd8e` is good
- A later commit is bad

Therefore, the next commit after `377fd8e`:

```text
cd1d4c4
```

must be the first commit that introduced the bug.

Visualization:

```text
a3e9a53  GOOD
   |
377fd8e  GOOD
   |
cd1d4c4  BAD  <-- First bad commit
   |
89ab123  BAD
   |
fed4567  BAD
```

---

## Useful Commands

Show bisect decisions:

```bash
git bisect log
```

Visualize commits:

```bash
git bisect visualize
```

Return to original branch:

```bash
git bisect reset
```

Inspect a commit:

```bash
git show <commit-hash>
```

Compare two commits:

```bash
git diff <commitA> <commitB>
```

---

## Automating Tests

Git can run tests automatically during bisect.

Example:

```bash
git bisect start
git bisect bad
git bisect good a3e9a53
git bisect run ./test_script.sh
```

Your script should return:

```text
0         => Good commit
Non-zero  => Bad commit
```

Git will automatically continue testing until it finds the first bad commit.

---

## Example Test Script

### Bash

```bash
#!/bin/bash

npm test

if [ $? -eq 0 ]; then
    exit 0
else
    exit 1
fi
```

Run:

```bash
git bisect run ./test.sh
```

---

## Common Mistakes

### 1. Marking the Wrong Commit as Good

If a commit actually contains the bug but is marked as good, the bisect result will be incorrect.

### 2. Testing with Uncommitted Changes

Always ensure your working tree is clean:

```bash
git status
```

### 3. Forgetting to Reset

After bisecting:

```bash
git bisect reset
```

Otherwise you'll remain in a detached HEAD state.

### 4. Assuming the Latest Bad Commit Is the First Bad Commit

The latest bad commit only tells Git where the bug exists now.

Bisect helps locate where it first appeared.

---

## Complexity Analysis

### Linear Search

Checking every commit manually:

```text
100 commits -> up to 100 tests
1000 commits -> up to 1000 tests
```

### Git Bisect (Binary Search)

```text
100 commits  -> ~7 tests
1000 commits -> ~10 tests
10000 commits -> ~14 tests
```

Formula:

```text
Tests ≈ log2(number_of_commits)
```

---

## Summary

Git bisect uses binary search to efficiently locate the exact commit that introduced a bug.

In the example:

```text
GOOD:
a3e9a53
377fd8e

BAD:
cd1d4c4
89ab123
fed4567
```

Git correctly identified:

```text
cd1d4c4f3a83f40fb20da6ad13000ea6e539c3fe
```

as the first bad commit because it was the first commit after the last confirmed good commit (`377fd8e`).

---

## Final Cleanup

After finishing:

```bash
git bisect reset
```

Git returns you to the branch and commit where you started the bisect session.

