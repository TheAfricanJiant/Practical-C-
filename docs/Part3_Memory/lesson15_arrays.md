# Lesson 15: C-style Arrays & Joint State

A robotic arm like the Braccio V2 has six distinct joints. If we wanted to store the target angle for each joint without using arrays, we would have to declare six individual variables:
```cpp
int targetBase = 90;
int targetShoulder = 90;
int targetElbow = 90;
// ...
```
This is messy and makes writing loops impossible. In C++, we use **Arrays** to pack multiple variables of the same type sequentially in memory.

---

## Contiguous Memory Layout

An array is a collection of elements of the same data type stored in contiguous (unbroken) memory locations. 

When you declare `int jointAngles[6] = {90, 115, 90, 90, 90, 50};`, C++ reserves a single block of memory large enough to hold six integers side-by-side:

```text
Index:         [   0   ] [   1   ] [   2   ] [   3   ] [   4   ] [   5   ]
Value:         [  90   ] [  115  ] [  90   ] [  90   ] [  90   ] [  50   ]
Address:       [0x2000 ] [0x2004 ] [0x2008 ] [0x200C ] [0x2010 ] [0x2014 ]
```
Since an `int` takes 4 bytes, each index is offset exactly 4 bytes from the previous address.

---

## Array Syntax & Iteration

Arrays are 0-indexed, meaning the first element is at index `0`, and the last element is at index `size - 1`.

```cpp
#include <iostream>

int main() {
    // Declare and initialize an array of size 6
    int jointAngles[6] = {90, 115, 90, 90, 90, 50};

    // Read and modify elements
    jointAngles[1] = 120; // Modify the shoulder angle at index 1

    // Iterate through the array using a loop
    for (int i = 0; i < 6; ++i) {
        std::cout << "Joint " << i << " Angle: " << jointAngles[i] << " degrees" << std::endl;
    }

    return 0;
}
```

---

## The Danger of Bounds Overflow

In C++, **arrays do not have built-in bounds checking**. If you attempt to access an index outside the array size (for example, `jointAngles[10]`), the compiler will compile the code without error. 

However, at runtime, the program will read or write to whatever random data is located 40 bytes past the start of the array. This is a **Buffer Overflow** and can corrupt other variables, crash the program, or lead to security exploits.

```cpp
int jointAngles[6] = {90, 90, 90, 90, 90, 50};
jointAngles[6] = 180; // BUG! Index 6 is the 7th element (out of bounds).
```

---

## Array Decay to Pointers

In C++, the name of an array acts as a pointer to its first element. When you pass an array into a function, it **decays** into a pointer (`type*`). 

Because the function only receives a pointer, it has no way of knowing how large the array is. Therefore, you must also pass the **array size** as a separate parameter!

```cpp
#include <iostream>

// The parameter 'arr' is actually a pointer (int*) under the hood!
void printAngles(const int arr[], int size) {
    for (int i = 0; i < size; ++i) {
        std::cout << arr[i] << " ";
    }
    std::cout << std::endl;
}

int main() {
    int jointAngles[6] = {90, 115, 90, 90, 90, 50};
    printAngles(jointAngles, 6); // Pass the array and its size
    return 0;
}
```

---

## Connection to the BraccioV2 Library

The BraccioV2 library uses arrays to store and manage the states of the six servos cleanly:
* `_jointMin[7]` and `_jointMax[7]` store physical safety limits.
* `_jointCenter[7]` stores default startup values.
* `_currentJointPositions[7]` and `_targetJointPositions[7]` store current and future angles.

*(Note: Although there are only 6 physical joints on the arm, the library declares arrays of size 7, leaving index 6 unused, or reserves indices for future expansions.)*

In the library implementation, you'll see loops that iterate over these arrays:
```cpp
bool Braccio::setAllAbsolute(int b, int s, int e, int w, int w_r, int g) {
  boolean out = true;
  out = out & setOneAbsolute(BASE_ROT, b);
  out = out & setOneAbsolute(SHOULDER, s);
  // ...
  return out;
}
```
Here, rather than a loop, it accesses the joints using named constants `BASE_ROT = 0`, `SHOULDER = 1`, etc., as indexes into the state arrays.

---

## Practice Exercises

### Exercise 1: Calculate Average Angle
Write a function named `calculateAverageAngle` that takes:
* An array of joint angles `const int angles[]`.
* An integer `int size` representing the number of joints.

The function should return the average angle as a `float`. Test it inside `main()`.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

float calculateAverageAngle(const int angles[], int size) {
    if (size <= 0) return 0.0f;

    int sum = 0;
    for (int i = 0; i < size; ++i) {
        sum += angles[i];
    }
    return (float)sum / size;
}

int main() {
    int currentAngles[6] = {90, 115, 80, 45, 90, 30};
    float avg = calculateAverageAngle(currentAngles, 6);

    std::cout << "Average joint angle: " << avg << " degrees" << std::endl;
    return 0;
}
```
</details>

### Exercise 2: Bounds Safety Checker
Write a program that initializes a joint configuration array of size 6. Write a loop that accepts a user-input joint index. Validate that the index is safe (within bounds of the array) before reading or writing to it.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

bool updateJointAngle(int angles[], int size, int index, int newAngle) {
    // Boundary check
    if (index >= 0 && index < size) {
        angles[index] = newAngle;
        return true; // Success
    }
    return false; // Index out of bounds!
}

int main() {
    int jointAngles[6] = {90, 90, 90, 90, 90, 50};

    // Valid index
    if (updateJointAngle(jointAngles, 6, 2, 100)) {
        std::cout << "Joint 2 updated successfully." << std::endl;
    } else {
        std::cout << "Error: Invalid joint index." << std::endl;
    }

    // Invalid index
    if (updateJointAngle(jointAngles, 6, 8, 120)) {
        std::cout << "Joint 8 updated successfully." << std::endl;
    } else {
        std::cout << "Error: Invalid joint index (Out of Bounds protected!)." << std::endl;
    }

    return 0;
}
```
</details>

---

[Previous: Lesson 14](lesson14_dynamic_memory.md) | [Next: Part 4 - Lesson 16](../Part4_Arduino_Libraries/lesson16_arduino_ecosystem.md)
