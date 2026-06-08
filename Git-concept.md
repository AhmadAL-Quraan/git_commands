# Concepts

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