# Lesson 14: Stack vs. Heap & Memory Safety

To write safe C++ code, you must understand where variables live in memory. C++ splits application RAM into two primary areas: the **Stack** and the **Heap**.

In this lesson, we will compare Stack and Heap allocations, learn the syntax of dynamic allocation (`new` and `delete`), and discuss why dynamic heap allocation is highly discouraged in embedded systems and robotics.

---

## Memory Areas: Stack vs. Heap

| Feature | The Stack | The Heap |
| :--- | :--- | :--- |
| **Size** | Small (typically 1–8 MB on PCs, bytes on Arduino) | Large (gigabytes on PCs, limited on Arduino) |
| **Allocation** | Automatic (handled by the compiler) | Manual (controlled by the programmer) |
| **Speed** | Extremely fast (simple pointer movement) | Slower (requires searching for free blocks) |
| **Lifetime** | Tied to scope (destroyed at `}`) | Persistent (exists until explicitly deleted) |

```text
  RAM Layout:
  +---------------------------------------------------+
  | Stack (Grows Down) | ... Free RAM ... | Heap (Up) |
  +---------------------------------------------------+
```

---

## Stack Allocation (Automatic Memory)

All variables declared inside functions are allocated on the Stack. 

```cpp
void moveArm() {
    int localAngle = 90; // Stack allocation
    // localAngle exists here
} // localAngle is automatically deleted!
```
Stack allocation is fast and safe. You cannot forget to free stack memory; C++ handles it automatically.

---

## Heap Allocation (Dynamic Memory)

If you need data to survive beyond the scope of the function that created it, or you need to allocate a variable whose size is only known at runtime, you allocate it on the **Heap**.

In C++, heap allocation is managed using two keywords:
* **`new`:** Allocates memory on the heap and returns a pointer to it.
* **`delete`:** Frees the allocated heap memory, returning it to the system.

```cpp
#include <iostream>

int main() {
    // 1. Allocate an integer on the Heap
    int* heapPtr = new int(90); 

    std::cout << "Heap Value: " << *heapPtr << std::endl;

    // 2. You must manually free this memory when done!
    delete heapPtr; 

    // 3. Prevent dangling pointer by setting it to nullptr
    heapPtr = nullptr; 

    return 0;
}
```

### The Pitfalls of Dynamic Memory:
1. **Memory Leaks:** If you allocate memory with `new` and lose the pointer (or forget to call `delete`), that memory remains occupied. If this happens in a loop, your program will eventually run out of RAM and crash.
2. **Dangling Pointers:** If you call `delete` on a pointer but continue to use it, you will read or write to unallocated memory, causing undefined behavior.

---

## Why Embedded Systems Avoid the Heap

In standard computer programming, heap allocation is commonplace. In embedded robotics (like Arduino), **dynamic memory allocation is highly avoided**.

### 1. Extremely Limited RAM
An Arduino Uno has only 2,048 bytes (2KB) of RAM. A single memory leak can exhaust this memory in milliseconds.

### 2. Heap Fragmentation
The heap is shared. If you allocate and deallocate objects of different sizes repeatedly, your memory becomes "fragmented"—broken into tiny pockets of free space separated by allocated blocks. 

Eventually, you might attempt to allocate a small object, and even though there is enough total free memory, the allocation will fail because there is no **single, contiguous block** large enough. This causes the microcontroller to crash or lock up unpredictably.

```text
Fragmented Memory:
[ Allocated ] [ Free: 2B ] [ Allocated ] [ Free: 2B ] [ Allocated ]
(Total Free Space = 4B, but you cannot allocate a single 3B variable!)
```

---

## Connection to the BraccioV2 Library

To guarantee stability, the BraccioV2 library uses **static memory allocation**. 

All objects (like the `Servo` instances) and arrays (like `_currentJointPositions`) are declared as member variables directly inside the `Braccio` class. When the `Braccio` object is instantiated (typically globally at the top of an Arduino sketch), the memory is allocated statically once at startup. No `new` or `delete` keywords appear anywhere in the source files, guaranteeing zero fragmentation and zero leaks.

---

## Practice Exercises

### Exercise 1: Spot the Leak
Identify the memory leak in the following program. Explain why it occurs and how to fix it.
```cpp
#include <iostream>

void calibrateRobot() {
    int* offset = new int(5);
    if (*offset > 0) {
        std::cout << "Positive calibration offset applied." << std::endl;
        return;
    }
    delete offset;
}

int main() {
    calibrateRobot();
    return 0;
}
```

<details>
<summary><b>View Solution</b></summary>
The memory leak occurs inside the `if` condition:
```cpp
if (*offset > 0) {
    std::cout << "Positive calibration offset applied." << std::endl;
    return; // LEAK!
}
```
If `*offset` is greater than `0`, the function execution hits the `return;` statement. The local pointer variable `offset` (which lives on the Stack) is destroyed, but the memory it pointed to on the Heap is **never freed** because `delete offset;` is skipped.

**The Fix:** Make sure `delete` is called along all execution paths before exiting the function, or avoid heap allocation entirely:
```cpp
void calibrateRobot() {
    int* offset = new int(5);
    if (*offset > 0) {
        std::cout << "Positive calibration offset applied." << std::endl;
        delete offset; // Free memory before returning!
        return;
    }
    delete offset; // Free memory here too
}
```
*Better yet:* Just use a stack variable: `int offset = 5;`. No pointers or dynamic allocations are needed!
</details>

---

[Previous: Lesson 13](lesson13_references.md) | [Next: Lesson 15](lesson15_arrays.md)
