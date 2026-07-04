# Lesson 04: Functions & Scope

As your robotics software grows, you cannot keep all your code inside the `main()` function. To keep code clean, reusable, and testable, you must break it down into modular blocks called **functions**.

In this lesson, we will cover function definitions, return types, parameters, prototypes, and how variable scope operates across functions.

---

## What is a Function?

A function is a named block of code that performs a specific task. You can "call" a function from other parts of your program, optionally passing it inputs (parameters) and receiving an output (return value).

Here is the general syntax of a function in C++:

```cpp
return_type function_name(parameter_list) {
    // Code to execute
    return value; // (if return_type is not void)
}
```

Let's write a function that constraints (clamps) a servo angle between physical limits.

```cpp
#include <iostream>

// This function takes a target angle, minimum limit, and maximum limit.
// It returns the clamped safe angle.
int clampAngle(int target, int minLimit, int maxLimit) {
    if (target < minLimit) {
        return minLimit;
    }
    if (target > maxLimit) {
        return maxLimit;
    }
    return target;
}

int main() {
    int requestedAngle = 195;
    int safeAngle = clampAngle(requestedAngle, 0, 180);

    std::cout << "Requested: " << requestedAngle << std::endl;
    std::cout << "Safe: " << safeAngle << std::endl;
    return 0;
}
```

---

## Function Declaration vs. Definition

In C++, the compiler reads your code from top to bottom. If you attempt to call a function before it has been declared, the compiler will fail with an error.

To solve this, C++ separates functions into two parts:
1. **Declaration (Prototype):** Tells the compiler the function's name, return type, and parameters. This is usually placed at the top of the file or in a header file.
2. **Definition:** Contains the actual body of the function. This can be placed later in the file or in a separate `.cpp` source file.

```cpp
#include <iostream>

// 1. Function Declaration (Prototype)
bool isValidAngle(int angle);

int main() {
    // The compiler knows isValidAngle exists and expects an int argument
    if (isValidAngle(120)) {
        std::cout << "Angle is valid!" << std::endl;
    }
    return 0;
}

// 2. Function Definition
bool isValidAngle(int angle) {
    return angle >= 0 && angle <= 180;
}
```

---

## Parameters and Pass-by-Value

When you pass variables into a function in C++, they are passed by **value** by default. This means the compiler makes a **copy** of the variable's value and passes that copy to the function. Any changes made to the parameter inside the function *do not affect* the original variable outside the function.

```cpp
#include <iostream>

void setAngleToZero(int angle) {
    angle = 0; // Modifies the local copy, not the original variable!
    std::cout << "Inside function: " << angle << std::endl;
}

int main() {
    int currentAngle = 90;
    setAngleToZero(currentAngle);
    std::cout << "In main: " << currentAngle << std::endl; // Still 90!
    return 0;
}
```
*(In Part 3, we will learn about references, which allow functions to modify the original variables directly.)*

---

## Variable Scope & Lifetime

* **Lifetime:** The time span during which a variable exists in memory.
* **Scope:** The region of code where a variable's name can be used.

Variables declared inside a function's body (or as parameters) are **local variables**. They are created on the **Stack** when the function is called, and automatically destroyed (deallocated) as soon as the function hits its closing brace `}`.

```cpp
#include <iostream>

void sampleFunction() {
    int tempVal = 42; // Local variable created here
    std::cout << tempVal << std::endl;
} // tempVal is destroyed here!

int main() {
    sampleFunction();
    // std::cout << tempVal << std::endl; // ERROR: tempVal is out of scope!
    return 0;
}
```

---

## Connection to the BraccioV2 Library

In `BraccioV2.cpp`, functions are used to structure the library's features:
* `setOneAbsolute(int joint, int value)` takes two integers by value, performs clamping, and saves the target.
* `getCenter(int joint)` returns the calibrated center point of the specified joint as an integer.
* Utility functions like `constrain()` (built into Arduino) are used extensively for boundary checking.

---

## Practice Exercises

### Exercise 1: Write a Degree-to-Radian Converter
Write a function named `degToRad` that takes a float representation of an angle in degrees and returns its value in radians (formula: $\text{radians} = \text{degrees} \times \frac{\pi}{180}$). Use `3.14159` for $\pi$. Test it inside `main()`.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

float degToRad(float degrees) {
    const float PI_VAL = 3.14159f;
    return degrees * (PI_VAL / 180.0f);
}

int main() {
    float angleDeg = 90.0f;
    float angleRad = degToRad(angleDeg);

    std::cout << angleDeg << " degrees is " << angleRad << " radians." << std::endl;
    return 0;
}
```
</details>

### Exercise 2: Fix the Compiler Error
Identify why this code fails to compile:
```cpp
#include <iostream>

int main() {
    printStatus();
    return 0;
}

void printStatus() {
    std::cout << "System Online" << std::endl;
}
```

<details>
<summary><b>View Solution</b></summary>
The compiler parses code from top to bottom. Inside `main()`, it encounters the call to `printStatus()`, but the function has not yet been declared or defined.

**To fix it, add a function prototype at the top:**
```cpp
#include <iostream>

// Prototype
void printStatus();

int main() {
    printStatus();
    return 0;
}

void printStatus() {
    std::cout << "System Online" << std::endl;
}
```
</details>

---

[Previous: Lesson 03](lesson03_control_flow.md) | [Next: Lesson 05](lesson05_headers.md)
