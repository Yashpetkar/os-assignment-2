
# 🧵 fork() System Call in C – Process Creation Lab

## 📌 Overview

The `fork()` system call is used to create a **new process** in Unix/Linux operating systems. The newly created process is called the **child process**, and the original process is called the **parent process**.

After calling `fork()`, **both parent and child processes execute concurrently**, starting from the next instruction following the `fork()` call.

> ⚠️ Note: These programs **do not compile on Windows OS**. Please run them on **Linux / Unix / macOS** environments.

---

## 🔹 Syntax

```c
pid_t fork(void);
```

---

## 🔁 Return Values of fork()

| Return Value | Meaning                                             |
| ------------ | --------------------------------------------------- |
| `-1`         | Process creation failed                             | 
| `0`          | Returned to **child process**                       |
| `>0`         | Returned to **parent process** (contains child PID) |

---

## 🧠 Key Concepts

* Child process gets a **copy of parent's memory**
* Both processes run **independently**
* Parent and child execute **concurrently**
* Execution order is **not predictable**

---

# 🧪 Examples

---

## Example 1: Basic fork() Demonstration

### Code

```c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>

int main()
{
    pid_t p = fork();

    if(p < 0)
    {
        perror("fork failed");
        return 1;
    }

    printf("Hello World! PID = %d\n", getpid());
    return 0;
}
```

### Output (Sample)

```
Hello World! PID = 31
Hello World! PID = 32
```

### Explanation

Two processes are created:

* Parent process
* Child process

Both print the message.

---

## Example 2: Counting Process Creation

### Code

```c
#include <stdio.h>
#include <unistd.h>

int main()
{
    fork();
    fork();
    fork();

    printf("hello\n");
    return 0;
}
```

### Output

```
hello
hello
hello
hello
hello
hello
hello
hello
```

### Explanation

Number of processes = `2^n`, where `n = number of fork() calls`

Here:
`2^3 = 8 processes` → so "hello" prints **8 times**.

---

## Example 3: Parent vs Child Output

### Code

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>

void forkexample()
{
    pid_t p = fork();

    if(p < 0)
    {
        perror("fork failed");
        exit(1);
    }
    else if(p == 0)
        printf("Hello from Child!\n");
    else
        printf("Hello from Parent!\n");
}

int main()
{
    forkexample();
    return 0;
}
```

### Output (Order may vary)

```
Hello from Parent!
Hello from Child!
```

### Explanation

Both processes run concurrently → output order **cannot be predicted**.

---

## Example 4: Variable Behavior in fork()

### Code

```c
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>

void forkexample()
{
    int x = 1;
    pid_t p = fork();

    if(p == 0)
        printf("Child has x = %d\n", ++x);
    else
        printf("Parent has x = %d\n", --x);
}

int main()
{
    forkexample();
    return 0;
}
```

### Possible Outputs

```
Parent has x = 0
Child has x = 2
```

OR

```
Child has x = 2
Parent has x = 0
```

### Explanation

Each process has **its own copy of variables**, so changes in one do **not affect** the other.

---

# 🔄 fork() vs exec()

| fork()                 | exec()                   |
| ---------------------- | ------------------------ |
| Creates new process    | Replaces current process |
| Parent + Child run     | Old program replaced     |
| Same program continues | New program executes     |

---

# 🧠 GATE & Practice Problems

### 1. How many processes created?

```c
for (i = 0; i < n; i++)
    fork();
```

✔ Correct Answer: **2ⁿ − 1**
✔ Option: **(B)**

---

### 2. Predict Output

```c
fork();
fork() && fork() || fork();
fork();

printf("forked\n");
```

➡️ Challenge Problem for Students 😄

---

# 🧪 Lab Tasks

1. Write a C program using `fork()` to:

   * Print **PID and PPID**
2. Create **multiple child processes**
3. Show **process tree using pstree**
4. Implement **fork + exec** program

---

# 💻 Compilation & Execution

```bash
gcc fork_example.c -o fork_example
./fork_example
```

---

##  C Program
 understand how `fork()`, `execvp()`, and `wait()` work.

---

## C PROGRAM

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid;

    pid = fork();   // Create child process

    if (pid == 0) {
        // Child process
        printf("Child: Running 'ls' command\n");

        execlp("ls", "ls", NULL);   // Execute command
    }
    else {
        // Parent process
        printf("Parent: Waiting for child to finish\n");

        wait(NULL);   // Wait for child

        printf("Parent: Child finished\n");
    }

    return 0;
}
```

---

## Explanation

### Step 1: Header Files

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
```

These provide:

* Input/output functions
* Process creation functions
* Waiting for processes

---

### Step 2: fork()

```c
pid = fork();
```

`fork()` creates a **new process**.

After this:

* Two processes run:

  * Parent
  * Child

Return value:

* `0` → Child process
* Positive value → Parent process

---

### Step 3: Child Process Code

```c
if (pid == 0)
```

This part runs only in child.

```c
execlp("ls", "ls", NULL);
```

* Runs Linux command `ls`
* Child process becomes `ls` command.

---

### Step 4: Parent Process Code

```c
else
```

Parent executes this.

```c
wait(NULL);
```

Parent waits until child finishes.

---

### Step 5: Program Ends

Parent prints completion message.

---

## ✅ Compilation

```bash
gcc process.c -o process
```

## ✅ Run

```bash
./process
```

---

## ✅ Example Output

```
Parent: Waiting for child to finish
Child: Running 'ls' command
process
process.c
Parent: Child finished
```

---

## ✅ Flow Summary

```
Parent starts
      |
    fork()
      |
Parent        Child
   |            |
 wait()      run ls
   |            |
 continues    exit
```

---


