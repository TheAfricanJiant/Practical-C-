# Lesson 02: Variables, Types, and Constants

In robotics, programs must manage and manipulate physical states. For instance, to rotate a robotic arm, you need to store:
* The current angle of each joint (e.g., 90 degrees).
* The pins where the servos are connected (e.g., pin 11, pin 10).
* The safety limits of the joints (e.g., the shoulder joint must not rotate past 165 degrees).

In C++, this data is managed using **variables**, **types**, and **constants**.

---

## Fundamental Data Types

C++ is a **statically typed** language, meaning that every variable must be declared with a specific data type before it can be used, and this type cannot change. The data type tells the compiler how much memory to allocate and how to interpret the binary bits.

Here are the fundamental C++ types commonly used in robotics:

| Type | Description | Size in Memory (Typical PC) | Arduino Size (8-bit AVR) | Example |
| :--- | :--- | :--- | :--- | :--- |
| `int` | Signed whole numbers | 4 bytes (32 bits) | 2 bytes (16 bits) | `int angle = 90;` |
| `float` | Single-precision floating point | 4 bytes (32 bits) | 4 bytes (32 bits) | `float speed = 1.5;` |
| `double`| Double-precision floating point | 8 bytes (64 bits) | 4 bytes (32 bits - same as float!) | `double radian = 3.14159;` |
| `bool`  | Boolean (true or false) | 1 byte (8 bits) | 1 byte (8 bits) | `bool isMoving = false;` |
| `char`  | Single ASCII character | 1 byte (8 bits) | 1 byte (8 bits) | `char status = 'R';` |

---

## Fixed-Width Integer Types (Embedded Robotics)

In standard C++, the size of an `int` varies between different computer systems. On your desktop, an `int` is 4 bytes (can store values up to ±2 billion). On an 8-bit microcontroller (like the Arduino Uno's ATmega328P), an `int` is only 2 bytes (can only store values up to ±32,767). 

In embedded robotics, memory is highly constrained (the Arduino Uno has only 2KB of RAM!). To prevent bugs and minimize memory footprint, we use **fixed-width integer types** defined in the `<cstdint>` (or `<stdint.h>`) header:

* `int8_t` / `uint8_t`: Signed / Unsigned 8-bit integer (Range: -128 to 127 / 0 to 255). Perfect for servo angles (0 to 180).
* `int16_t` / `uint16_t`: Signed / Unsigned 16-bit integer (Range: -32,768 to 32,767 / 0 to 65,535).
* `int32_t` / `uint32_t`: Signed / Unsigned 32-bit integer (Range: up to ±2 billion / 0 to 4.2 billion). Perfect for millisecond timers.

```cpp
#include <iostream>
#include <cstdint> // Required for fixed-width types

int main() {
    uint8_t baseAngle = 90; // Uses only 1 byte of memory instead of 4 bytes!
    std::cout << "Base Angle: " << (int)baseAngle << " degrees." << std::endl;
    return 0;
}
```
> [!NOTE]
> When printing an 8-bit integer (`uint8_t` or `int8_t`) with `std::cout`, C++ treats it like a character (`char`). Casting it to `(int)` forces the terminal to display it as a number.

---

## Constants: `const` vs. `#define`

Some values in robotics must never change while the program is running—for example, the digital pin where a servo is physically wired. Changing this value in code by accident would cause the program to send signals to the wrong hardware.

There are two ways to define read-only values in C++:

### 1. Preprocessor Macros (`#define`)
Inherited from C, `#define` is a text-replacement directive. It does not occupy memory, but it also has **no type safety**.
```cpp
#define BASE_PIN 11 // The preprocessor replaces every 'BASE_PIN' with '11' before compilation.
```

### 2. Typed Constants (`const`)
The `const` keyword declares a variable as read-only. It is type-safe and obeys scope rules, making it much safer.
```cpp
const uint8_t BASE_PIN = 11; // Compile-time check: trying to change BASE_PIN causes an error.
```

---

## Variable Scope: Global vs. Local

Scope determines where a variable is visible and accessible in your code:

* **Local Variables:** Declared inside a function or block (like `{ }`). They only exist while the function is executing and are destroyed when the function finishes.
* **Global Variables:** Declared outside of any function. They are accessible from anywhere in the file and exist for the entire duration of the program.

```cpp
#include <iostream>

// Global variable - accessible anywhere
const int MAX_ANGLE = 180;

void printLimit() {
    // Can access MAX_ANGLE
    std::cout << "Max safety limit: " << MAX_ANGLE << std::endl;
}

int main() {
    int currentAngle = 95; // Local variable - only visible inside main()
    printLimit();
    std::cout << "Current: " << currentAngle << std::endl;
    return 0;
}
```

---

## Connection to the BraccioV2 Library

In `BraccioV2.h`, constants are used to define the servo pins and joint indices:
```cpp
#define BASE_ROT 0
#define SHOULDER 1
#define _BASE_ROT_PIN 11
#define _SHOULDER_PIN 10
```
While the original library uses `#define`, modern C++ guidelines recommend using `const` or `constexpr` to prevent macro pollution and enforce type safety.

---

## Practice Exercises

### Exercise 1: Memory Budgeting
Suppose you are writing code for a small microcontroller and need to store the following parameters. Choose the most memory-efficient data type for each:
1. A boolean flag representing if the gripper is closed.
2. The servo speed multiplier, which can be a decimal (e.g. `1.5`).
3. The pin number for the shoulder servo (pins are numbered 0 to 13).
4. The system uptime in milliseconds (which can grow very large).

<details>
<summary><b>View Solution</b></summary>

1. `bool` (or `uint8_t`): Takes 1 byte of memory.
2. `float`: Standard decimal type, takes 4 bytes. (Since microcontrollers rarely benefit from 8-byte `double` calculations and often lack double-precision hardware support).
3. `uint8_t`: Takes 1 byte of memory and can store 0 to 255.
4. `uint32_t`: Unsigned 32-bit integer, takes 4 bytes and can count up to 4,294,967,295 milliseconds (approx. 49 days) without overflow.
</details>

### Exercise 2: Code Fixer
Find the bug in this snippet:
```cpp
#include <iostream>

const int LIMIT = 180;

int main() {
    int currentAngle = 90;
    if (currentAngle < LIMIT) {
        LIMIT = currentAngle;
    }
    std::cout << "Limit: " << LIMIT << std::endl;
    return 0;
}
```

<details>
<summary><b>View Solution</b></summary>
The variable `LIMIT` is declared as a `const int`. This means it is read-only.
The line:
```cpp
LIMIT = currentAngle;
```
attempts to assign a new value to a constant, which will cause a compiler error:
`error: assignment of read-only variable 'LIMIT'`.

To fix it, either `LIMIT` should not be `const`, or the comparison/assignment logic should assign to `currentAngle` instead.
</details>

---

[Previous: Lesson 01](lesson01_compiler.md) | [Next: Lesson 03](lesson03_control_flow.md)
