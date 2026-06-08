# Concepts

## Branches and Head 

* Branch and HEAD and commits are all pointers.
* Branch is a pointer, points to a commit.
* Git history is a **directed acyclic graph (DAG)** implemented using commit objects that store **parent pointers**, just like a linked list that allows multiple nodes to point to the same parent.
```C
#include <stdio.h>
#include <stdlib.h>

🔧 Commit structure
typedef struct Commit {
    int id;
    struct Commit* parent;
} Commit;

🧱 Helper to create commits
Commit* new_commit(int id, Commit* parent) {
    Commit* c = malloc(sizeof(Commit));
    c->id = id;
    c->parent = parent;
    return c;
}

🌿 Building the graph (branching!)
int main() {
    Commit* c1 = new_commit(1, NULL);
    Commit* c2 = new_commit(2, c1);
    Commit* c3 = new_commit(3, c2);

    // Branch happens here
    Commit* c4 = new_commit(4, c3); // branch A
    Commit* c5 = new_commit(5, c3); // branch B (HEAD)

    // HEAD points here
    Commit* HEAD = c5;

    // Traverse like git log
    Commit* cur = HEAD;
    while (cur) {
        printf("Commit %d\n", cur->id);
        cur = cur->parent;
    }

    return 0;
}
```


* When using `git log`, it shows what HEAD itself points to (branch), and it goes to commits until it reaches NULL pointers (NO commit left). 
	* HEAD -> main branch -> commit 1 -> commit 2 -> No commit
	* We just see what this branch have .


## Git objects 

Git stores four major object types:

- Blob: Content of the files.
- Tree: Directories 
- Commit: 
- Tag

All objects are identified using SHA hashes.

## Commit Graph Example

```text
A --- B --- C main
 \
  D --- E feature
```

Each commit points to parent commits.

> Commits are snapshots to content (after staging).


- Branches are simply movable labels pointing to commits.
- So: HEAD -> Branch -> Commit 
- HEAD can point to commit as well.
- i.e HEAD, Branches just a pointers, that points to commits.

```
git add(files staged) ---> git commit ---> files are hashed & saved in .git/objects -> git stores the content along side the hash file (inside it but comporessed)
```

Ex: 
```
echo "hello" > hello.txt 
git add hello.txt 
```
To see the file hash(blob object) got to `.git/objects` -> the first 2 bytes of hash file is stored as a directory, and then the rest will be inside it as a file.
```
git cat-file -p <hash> -> a2dvdf...
```

->result: `hello`

```
Object:
{
  type: blob
  content: "hello"
}
```
> The hash is for the object itself not the file name.
```
file contents
   ↓
Git creates blob object
   ↓
Git hashes blob object
   ↓
hash becomes object ID
```
---

## Understanding HEAD and Branches

HEAD points to the current branch or commit.

Example:

```text
main -> C
HEAD -> main
```

After another commit:

```text
main -> D
HEAD -> main
```

> See the branches head points to in `.git/refs/head/`
---

## Difference between revision and reference 

* Reference: Any pointer that points to specific git object, i.e HEAD, main , ... -> you can find this phrase in commands like : `git reflog`

* Revision: Some way to identify a specific version/object/commit -> i.e `git rev-parse HEAD`

All of these valid revisions:
```
main
HEAD
abc123
HEAD~1
main^
origin/main
v1.0
```
