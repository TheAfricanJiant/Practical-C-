# Lesson 01: The C++ Toolchain & Compilation

Before you can control a robotic arm, you need to understand how C++ source code is transformed into instructions that a machine can execute. In this lesson, we will explore the C++ compilation pipeline and compile our first robotic program using the terminal.

---

## The Compilation Pipeline

Unlike interpreted languages (like Python or JavaScript) that are read and executed line-by-line at runtime, C++ is a **compiled language**. This means your human-readable code is translated entirely into machine code (binary instructions) *before* it runs. 

This translation is performed by a toolchain consisting of four distinct stages:

```mermaid
graph LR
    Src[Source Code .cpp] --> Prep[1. Preprocessor]
    Prep --> Comp[2. Compiler]
    Comp --> Asm[3. Assembler]
    Asm --> Link[4. Linker]
    Link --> Exec[Executable / Binary]
```

### 1. The Preprocessor
The preprocessor scans the source code for lines starting with `#` (such as `#include` or `#define`). It performs textual substitution, copy-pasting the contents of headers directly into the file and replacing macro names with their defined values.

### 2. The Compiler
The compiler takes the expanded preprocessor output and translates it into **Assembly language**—a low-level, human-readable representation of processor instructions specific to your CPU architecture (e.g., x86, ARM, AVR).

### 3. The Assembler
The assembler converts the assembly instructions into **Object Code** (machine code). These are saved in binary files usually ending in `.o` or `.obj`. At this stage, the code cannot be run yet because external functions (like printing to the screen or moving a servo) are not resolved.

### 4. The Linker
The linker combines one or more object files, along with pre-compiled library binaries (like the C++ Standard Library), to produce a single final **Executable** file (or a `.hex` file for Arduino microcontrollers).

---

## Why This Matters for Robotics

When you write code for a microcontroller (like the Arduino Uno's ATmega328P chip), your development computer acts as a **cross-compiler**. It compiles C++ code written for an AVR processor on an x86/ARM laptop. 

Understanding this pipeline helps you diagnose build errors:
* If you miss a semicolon, the **Compiler** will fail with a syntax error.
* If you call a function that was declared but never defined, the **Linker** will fail with an "undefined reference" error.

---

## Step-by-Step: Writing and Compiling in the Terminal

Let's write a simple C++ program that simulates checking the connection status of our robotic arm.

### Step 1: Write the Code
Create a file named `status_check.cpp` in your text editor and type the following code:

```cpp
#include <iostream>

int main() {
    std::cout << "Initializing Braccio Robotic Arm..." << std::endl;
    std::cout << "All joints connected and calibrated!" << std::endl;
    return 0;
}
```

* `#include <iostream>`: Instructs the preprocessor to include the standard Input/Output Stream library, allowing us to print text to the console.
* `int main()`: The entry point of every C++ application. The operating system begins execution here.
* `std::cout`: Stands for "character output". It represents the standard console output stream.
* `<<`: The stream insertion operator, used to send data to `cout`.
* `std::endl`: Inserts a newline character and flushes the output buffer, ensuring the text appears immediately.
* `return 0;`: A status code returned to the operating system. `0` indicates the program completed successfully.

### Step 2: Compile the Program
Open a terminal in the folder containing `status_check.cpp` and run the following command:

```bash
g++ -std=c++17 -Wall status_check.cpp -o status_check
```

Let's break down the compiler flags:
* `g++`: The GNU C++ Compiler executable.
* `-std=c++17`: Instructs the compiler to use the C++17 language standard.
* `-Wall`: Enables "all warnings". This prompts the compiler to warn you about potential issues (like unused variables or bad conversions).
* `status_check.cpp`: The source file we want to compile.
* `-o status_check`: Specifies the name of the output executable file. If omitted, the compiler defaults to `a.out`.

### Step 3: Run the Executable
In your terminal, run the compiled binary:

```bash
./status_check
```

You should see the output:
```text
Initializing Braccio Robotic Arm...
All joints connected and calibrated!
```

---

## Common Compilation Errors

### 1. Missing Header Include
If you comment out or forget `#include <iostream>`, the compiler will complain:
```text
error: 'cout' is not a member of 'std'
```
*Fix:* Always include the necessary headers for standard library functions.

### 2. Missing Semicolon
If you forget a semicolon at the end of a line, the compiler will say:
```text
error: expected ';' before 'return'
```
*Fix:* Semicolons are statement terminators in C++; verify that every instruction ends with one.

---

## Practice Exercises

### Exercise 1: Build a Diagnostics Reporter
Create a program named `diagnostics.cpp` that prints three separate lines:
1. `--- Braccio V2 Diagnostics ---`
2. `Voltage: 5.0V`
3. `Status: Safe`

Compile and run it.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

int main() {
    std::cout << "--- Braccio V2 Diagnostics ---" << std::endl;
    std::cout << "Voltage: 5.0V" << std::endl;
    std::cout << "Status: Safe" << std::endl;
    return 0;
}
```
Compile command:
```bash
g++ -std=c++17 -Wall diagnostics.cpp -o diagnostics
```
</details>

### Exercise 2: Identify the Stage of Failure
If you compile a program and receive the error:
`collect2: error: ld returned 1 exit status` (where `ld` is the GNU Linker)
What stage of the compilation pipeline failed, and what type of error should you search for in your code?

<details>
<summary><b>View Solution</b></summary>
The **Linker** stage failed. You should look for:
1. A declared function or class method that is missing its actual definition/implementation.
2. A misspelled function name in your call.
3. Forgetting to compile all necessary source files together (e.g., compiling `main.cpp` without compiling `BraccioV2.cpp`).
</details>

---

[Previous: Getting Started](../getting_started.md) | [Next: Lesson 02](lesson02_variables.md)
