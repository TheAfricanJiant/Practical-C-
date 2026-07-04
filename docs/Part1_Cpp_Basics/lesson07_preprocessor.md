# Lesson 07: The C++ Preprocessor & Macro Magic

Before your C++ code is processed by the compiler, a tool called the **Preprocessor** scans your files. The preprocessor performs pure text manipulation. It does not understand C++ syntax, types, or scopes; it simply obeys preprocessor directives, which are instructions that start with the `#` symbol.

In this lesson, we will cover macro definitions, macro functions, conditional compilation, and the safety trade-offs of using macros in embedded robotics.

---

## Preprocessor Directives

Directives tell the preprocessor to modify the source code text. The most common directives are:

* `#include`: Pastes the contents of another file at this exact location.
* `#define`: Creates a text substitution macro.
* `#undef`: Deletes a macro definition.
* `#if`, `#ifdef`, `#ifndef`, `#else`, `#elif`, `#endif`: Conditional compilation blocks.

---

## Macro Substitutions

### 1. Object-like Macros
We have seen macros used to define constants:
```cpp
#define GRIPPER_PIN 3
```
Whenever the preprocessor finds the token `GRIPPER_PIN` in your code (except inside strings), it replaces it with `3`.

### 2. Function-like Macros
Macros can also take arguments, looking like functions. They are resolved by replacing the parameter tokens in the macro body:

```cpp
#include <iostream>

// Macro function to find the maximum of two values
#define MAX_VAL(a, b) ((a) > (b) ? (a) : (b))

int main() {
    int val1 = 45;
    int val2 = 90;
    std::cout << "Max: " << MAX_VAL(val1, val2) << std::endl;
    return 0;
}
```

> [!WARNING]
> **The Side-Effect Trap:** Since macros are pure text replacements, passing expressions with side effects can lead to unexpected behaviors. For example:
> `MAX_VAL(++val1, val2)` expands to `((++val1) > (val2) ? (++val1) : (val2))`.
> Here, `val1` will be incremented **twice**!
> Modern C++ prefers inline functions or `std::max()` to prevent this.

---

## Conditional Compilation

Conditional compilation allows you to tell the compiler to compile or skip specific blocks of code depending on whether certain preprocessor macros are defined. This is extremely useful for:
* **Debug Logging:** Only compiling debugging code when a debug flag is active, saving memory on production hardware.
* **Cross-Platform Code:** Writing code that compiles on Windows, Linux, and Arduino using different system headers.

```cpp
#include <iostream>

#define DEBUG_MODE // Comment out this line to disable debug logs

int main() {
    std::cout << "Starting system..." << std::endl;

#ifdef DEBUG_MODE
    std::cout << "[DEBUG] Reading raw servo calibration..." << std::endl;
#endif

    std::cout << "System ready." << std::endl;
    return 0;
}
```
If `DEBUG_MODE` is defined, the output will contain the debug line. If not, the compiler completely ignores the debug line—it will not even appear in the compiled executable binary!

---

## Connection to the BraccioV2 Library

In `BraccioV2.h`, macros are used for configuration:
```cpp
#define SOFT_START_VALUE 0
#define SOFT_START_PIN 12
```
Additionally, Arduino libraries frequently use conditional compilation. For example, to write code that compiles on both standard AVR Arduinos and newer ARM-based boards, developers use:
```cpp
#ifdef ARDUINO
  #include <Arduino.h>
#else
  #include <iostream>
#endif
```

---

## Practice Exercises

### Exercise 1: Macro Side Effects
Predict the output of the following program:
```cpp
#include <iostream>

#define SQUARE(x) (x * x)

int main() {
    int num = 3 + 1;
    std::cout << "Result: " << SQUARE(num) << std::endl;
    return 0;
}
```
Run this through the preprocessor in your head. What is the result? How would you fix the macro definition to avoid this?

<details>
<summary><b>View Solution</b></summary>
The output will be:
`Result: 7`

**Why?**
The preprocessor replaces `SQUARE(num)` with `(3 + 1 * 3 + 1)`.
According to C++ operator precedence rules (multiplication before addition):
`3 + (1 * 3) + 1` = `3 + 3 + 1` = `7`.

**The Fix:**
Always surround macro parameters and the overall macro body in parentheses:
```cpp
#define SQUARE(x) ((x) * (x))
```
Now it expands to `((3 + 1) * (3 + 1))` = `4 * 4` = `16`.
</details>

### Exercise 2: Debug Conditional Compilation
Write a program that uses conditional compilation. If a macro `SIMULATOR_MODE` is defined, print `[SIMULATION] Moving joints to center.` If it is not defined, print `[HARDWARE] Sending signals to servo pins.`

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

#define SIMULATOR_MODE // Comment out to test hardware mode

int main() {
#ifdef SIMULATOR_MODE
    std::cout << "[SIMULATION] Moving joints to center." << std::endl;
#else
    std::cout << "[HARDWARE] Sending signals to servo pins." << std::endl;
#endif
    return 0;
}
```
</details>

---

[Previous: Lesson 06](lesson06_include_guards.md) | [Next: Lesson 08](../Part2_OOP/lesson08_classes.md)
