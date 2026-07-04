# Lesson 05: Header Files vs. Source Files

In professional software development, you do not put all your code in a single file. Large codebases are split into multiple files to make them manageable, allow developers to work on different components simultaneously, and reduce compilation times.

C++ manages this separation using two types of files:
* **Header Files (`.h` or `.hpp`):** Contain declarations (interfaces) of functions, classes, and constants. They tell other files *what* is available.
* **Source Files (`.cpp`):** Contain the actual definitions (implementations). They describe *how* things work.

In this lesson, we will build a multi-file project and compile it.

---

## Separating Interface from Implementation

Think of a header file as a contract or blueprint, and the source file as the implementation. 

If you want to use a module (like a servo controller) in another part of your project:
1. You `#include` the header file to let the compiler know the class names and function signatures.
2. The compiler checks that you are calling the functions correctly (correct names, types, and parameter counts).
3. During linking, the compiler connects your calls to the compiled binary code from the source file.

---

## Step-by-Step: Creating a Multi-File Project

Let's build a modular robot joint model consisting of:
* `joint.h` (Declarations)
* `joint.cpp` (Implementations)
* `main.cpp` (The program entry point)

### 1. The Header File: `joint.h`
Create a file named `joint.h` and add the following:

```cpp
#ifndef JOINT_H
#define JOINT_H

// Declare a function that checks if an angle is within safe limits
bool isAngleSafe(int angle);

#endif
```
*(We will explain the `#ifndef` lines in Lesson 06. For now, think of them as safety wrappers.)*

### 2. The Source File: `joint.cpp`
Create a file named `joint.cpp` and add the following:

```cpp
#include "joint.h"

// Define the function declared in joint.h
bool isAngleSafe(int angle) {
    const int MIN_ANGLE = 10;
    const int MAX_ANGLE = 170;
    return (angle >= MIN_ANGLE && angle <= MAX_ANGLE);
}
```
Notice that we include `"joint.h"` (using double quotes instead of angle brackets). 
* **Angle Brackets (`< >`):** Look in the system's standard directories (e.g. `<iostream>`).
* **Double Quotes (`" "`):** Look in the local project directory first.

### 3. The Main File: `main.cpp`
Create a file named `main.cpp` and add the following:

```cpp
#include <iostream>
#include "joint.h" // Includes the declaration of isAngleSafe()

int main() {
    int target = 185;

    if (isAngleSafe(target)) {
        std::cout << "Moving to " << target << " degrees." << std::endl;
    } else {
        std::cout << "Error: Angle " << target << " is UNSAFE!" << std::endl;
    }

    return 0;
}
```

---

## Compiling Multiple Files

To compile a multi-file program, you must pass **all** `.cpp` source files to the compiler. The compiler will compile them individually into object files, and then link them together.

Run the following command in your terminal:

```bash
g++ -std=c++17 -Wall main.cpp joint.cpp -o robot_app
```

Now, run the executable:

```bash
./robot_app
```

You should see:
```text
Error: Angle 185 is UNSAFE!
```

If you forgot to include `joint.cpp` in the compile command:
```bash
g++ -std=c++17 -Wall main.cpp -o robot_app
```
The **compiler** stage succeeds, but the **linker** stage fails with an error:
`undefined reference to 'isAngleSafe(int)'`. This is because `main.cpp` knew about `isAngleSafe` (thanks to `joint.h`), but the actual compiled machine instructions (which live in `joint.cpp`) were never provided!

---

## Connection to the BraccioV2 Library

This is the exact structure of the BraccioV2 library:
* **`BraccioV2.h`:** Contains the `#include` directives, `#define` pin configurations, and the `Braccio` class declaration (all the methods and variables it owns).
* **`BraccioV2.cpp`:** Contains the actual definitions for the `Braccio` class constructor, `begin()`, `setAllAbsolute()`, `update()`, etc.

When you use the BraccioV2 library in an Arduino sketch, you write `#include <BraccioV2.h>` at the top. The Arduino IDE automatically compiles `BraccioV2.cpp` in the background and links it with your sketch.

---

## Practice Exercises

### Exercise 1: Create a Status Header
Create a header file named `status.h` that declares a function `void printBattery(float voltage);`. Create `status.cpp` that implements this function to print the battery level (e.g. `Battery Voltage: 7.4V`). Finally, include it and call it in a `main_status.cpp` file. Compile all files together.

<details>
<summary><b>View Solution</b></summary>

**`status.h`**
```cpp
#ifndef STATUS_H
#define STATUS_H

void printBattery(float voltage);

#endif
```

**`status.cpp`**
```cpp
#include <iostream>
#include "status.h"

void printBattery(float voltage) {
    std::cout << "Battery Voltage: " << voltage << "V" << std::endl;
}
```

**`main_status.cpp`**
```cpp
#include "status.h"

int main() {
    printBattery(7.4f);
    return 0;
}
```

Compile Command:
```bash
g++ -std=c++17 -Wall main_status.cpp status.cpp -o status_app
```
</details>

### Exercise 2: Header Do's and Don'ts
Why should you *never* write actual function definitions (executable code bodies) inside header files? What happens if you include that header in multiple `.cpp` files?

<details>
<summary><b>View Solution</b></summary>
If you define a function (write its body) inside a header file, and that header file is included by multiple `.cpp` source files (for example, `main.cpp` and `joint.cpp`), the compiler copy-pastes that definition into both files.

When compiling, both files will generate object code containing the same function. When the **Linker** attempts to merge them, it will fail with a **"duplicate symbol"** or **"multiple definition"** error. 

Headers should only declare interfaces, leaving the definitions to a single source file.
</details>

---

[Previous: Lesson 04](lesson04_functions.md) | [Next: Lesson 06](lesson06_include_guards.md)
