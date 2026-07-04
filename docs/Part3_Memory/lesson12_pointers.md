# Lesson 12: Understanding Pointers & Addresses

To write efficient systems code in C++, you need to understand how the computer stores data in hardware memory. In robotics, where you interface directly with hardware registers and sensors, this understanding is vital.

In this lesson, we will cover the concept of memory addresses, declaring and using **Pointers**, the dereferencing operators, and the safe use of `nullptr`.

---

## Memory as a Grid of Mailboxes

Think of the computer's RAM (Random Access Memory) as a long street with rows of mailboxes. 
* Every mailbox has a unique numeric **Address** (like `0x7ffd53`).
* Every mailbox can hold some **Data** (like the number `90`).

```text
Memory Address:  [ 0x1000 ]  [ 0x1004 ]  [ 0x1008 ]
Memory Content:  [   90   ]  [  1.5f  ]  [  true  ]
Variable Name:   [ angle  ]  [ speed  ]  [ active ]
```

When you write `int angle = 90;`, the compiler selects a mailbox (for example, at address `0x1000`) and stores the value `90` there.

---

## The Pointer Operators: `&` and `*`

A **Pointer** is a special type of variable that stores a **memory address** as its value, rather than a normal data value.

C++ uses two operators to work with memory:
1. **Address-of Operator (`&`):** Used to retrieve the memory address of a normal variable.
2. **Dereference Operator (`*`):** Used to read or write the data stored at the memory address held by a pointer.

```cpp
#include <iostream>

int main() {
    int angle = 90; // A normal integer variable
    
    // 1. Declare a pointer to an integer (int*) and assign it the address of 'angle'
    int* ptr = &angle; 

    std::cout << "Value of angle: " << angle << std::endl;
    std::cout << "Memory address of angle (&angle): " << &angle << std::endl;
    std::cout << "Value stored in ptr (which is the address): " << ptr << std::endl;

    // 2. Dereference ptr to read the value at that address
    std::cout << "Value pointed to by ptr (*ptr): " << *ptr << std::endl;

    // 3. Modify the value indirectly through the pointer
    *ptr = 120;
    std::cout << "New value of angle: " << angle << std::endl; // Now 120!

    return 0;
}
```

---

## Pointers to Objects & the Arrow Operator (`->`)

You can also create pointers to custom objects. When calling a member function or accessing a member variable through an object pointer, you must dereference the pointer first. 

C++ provides a shorthand operator `->` (the arrow operator) that dereferences the pointer and accesses the member in a single step.

```cpp
#include <iostream>

class Joint {
public:
    int angle = 90;
    void rotate(int val) { angle = val; }
};

int main() {
    Joint elbow;
    Joint* jointPtr = &elbow; // Pointer to elbow object

    // Option A: Dereference first, then use dot operator (clunky syntax)
    (*jointPtr).rotate(45);

    // Option B: Use the arrow operator (preferred, clean syntax)
    jointPtr->rotate(45); 

    std::cout << "Elbow Angle: " << jointPtr->angle << std::endl;

    return 0;
}
```

---

## The Null Pointer (`nullptr`)

If you declare a pointer without initializing it, it will point to some random address in memory. Attempting to read or write to a random memory address is a major bug that causes **undefined behavior** (often crashing your program immediately).

To prevent this, if a pointer does not point to anything yet, you should initialize it to **`nullptr`** (the safe null pointer constant introduced in C++11).

Always check if a pointer is valid before dereferencing it!

```cpp
#include <iostream>

void moveJoint(int* anglePtr, int target) {
    // Safety check: verify the pointer is not null
    if (anglePtr != nullptr) {
        *anglePtr = target; // Safe dereference
    } else {
        std::cout << "Error: Null pointer passed to moveJoint!" << std::endl;
    }
}

int main() {
    int angle = 90;
    int* myPtr = nullptr; // Starts as null

    moveJoint(myPtr, 45); // Safely fails print message

    myPtr = &angle;       // Points to valid variable
    moveJoint(myPtr, 45); // Succeeds!
    std::cout << "Angle: " << angle << std::endl;

    return 0;
}
```

---

## Connection to the BraccioV2 Library

In microcontroller environments, pointers are frequently used to wrap hardware addresses. 

In libraries like BraccioV2, pointers are often used to dynamically share resources. For example, if you wanted to pass a custom pointer of a `Servo` object into another library for logging or kinematics, you would write:
```cpp
void inspectServo(const Servo* servoPtr) {
    if (servoPtr != nullptr) {
        int currentPWM = servoPtr->readMicroseconds();
        // Log status...
    }
}
```

---

## Practice Exercises

### Exercise 1: Swap Two Values using Pointers
Write a function named `swapValues` that takes two integer pointers (`int* a` and `int* b`) and swaps the values stored in the memory they point to. Test this inside `main()`.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

void swapValues(int* a, int* b) {
    if (a != nullptr && b != nullptr) {
        int temp = *a; // Save value pointed to by a
        *a = *b;       // Copy value pointed to by b into memory of a
        *b = temp;     // Copy temp into memory of b
    }
}

int main() {
    int joint1 = 45;
    int joint2 = 90;

    std::cout << "Before swap - Joint 1: " << joint1 << ", Joint 2: " << joint2 << std::endl;

    swapValues(&joint1, &joint2);

    std::cout << "After swap  - Joint 1: " << joint1 << ", Joint 2: " << joint2 << std::endl;

    return 0;
}
```
</details>

### Exercise 2: Find the Memory Bug
Spot the critical error in this snippet and explain how it could crash the program:
```cpp
#include <iostream>

int main() {
    int* ptr = nullptr;
    *ptr = 90;
    std::cout << "Value: " << *ptr << std::endl;
    return 0;
}
```

<details>
<summary><b>View Solution</b></summary>
The variable `ptr` is initialized to `nullptr`. 
On the very next line:
```cpp
*ptr = 90;
```
the program attempts to dereference `ptr` to write the value `90` to memory address `0` (null).

This causes a **null pointer dereference**, which is undefined behavior. On modern operating systems, this triggers a **Segmentation Fault (core dumped)** crash immediately. On microcontrollers, it can cause the chip to crash, hang, or write data into critical boot registers, resulting in unpredictable physical behaviors.

**The Fix:** Always verify that a pointer is not null before dereferencing it, or point it to a valid variable address first.
</details>

---

[Previous: Part 2 - Lesson 11](../Part2_OOP/lesson11_methods.md) | [Next: Lesson 13](lesson13_references.md)
