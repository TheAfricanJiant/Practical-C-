# Lesson 06: Include Guards & Pragma Once

When compiling a C++ project, the preprocessor resolves `#include` directives by literally copying and pasting the header's contents into the source file. If a header file is included more than once in a single compilation unit, the compiler sees multiple declarations of the same classes or structs, resulting in compile-time redefinition errors.

To solve this, we use **Include Guards** or `#pragma once`.

---

## The Double Inclusion Problem

Imagine we have a project that models a robot:
1. `config.h` declares a structure for servo limits:
   ```cpp
   struct JointConfig {
       int minAngle;
       int maxAngle;
   };
   ```
2. `servo.h` includes `config.h` to configure its motors.
3. `main.cpp` includes both `config.h` (to read bounds) and `servo.h` (to interact with the motors).

When the preprocessor compiles `main.cpp`, it expands the files:
* It reads `config.h` -> defines `JointConfig`.
* It reads `servo.h` -> which reads `config.h` -> defines `JointConfig` *again*.

The compiler sees:
```cpp
struct JointConfig { ... };
struct JointConfig { ... }; // ERROR: redefinition of 'struct JointConfig'
```
Even though you didn't write it twice, the preprocessor expanded it twice. This breaks compilation.

---

## Solution 1: Traditional Include Guards

Traditional include guards use preprocessor directives (`#ifndef`, `#define`, and `#endif`) to wrap the contents of a header file. 

```cpp
#ifndef CONFIG_H_
#define CONFIG_H_

struct JointConfig {
    int minAngle;
    int maxAngle;
};

#endif // CONFIG_H_
```

Here is how this prevents double inclusion:
1. The first time `config.h` is read, the preprocessor evaluates `#ifndef CONFIG_H_` ("if `CONFIG_H_` is not defined").
2. Since `CONFIG_H_` has not been defined, the preprocessor proceeds past the condition.
3. The next line is `#define CONFIG_H_`, which defines the preprocessor macro.
4. The struct definition is parsed.
5. The second time `config.h` is read (via `servo.h`), the preprocessor checks `#ifndef CONFIG_H_`.
6. Since `CONFIG_H_` is now defined, the preprocessor skips everything down to the `#endif`. The struct is not redefined!

> [!TIP]
> The naming convention for include guard macros is typically the header name in uppercase, replacing dots with underscores and adding a trailing underscore (e.g. `FILENAME_H_` or `PROJECT_FILENAME_H`).

---

## Solution 2: `#pragma once`

Most modern C++ compilers support a simpler, non-standard but universally accepted alternative: `#pragma once`.

```cpp
#pragma once

struct JointConfig {
    int minAngle;
    int maxAngle;
};
```

When the compiler encounters `#pragma once`, it marks this file. If it encounters the same file again during the compilation of a source file, it ignores it immediately.

### Pros of `#pragma once`:
* **Concise:** Only requires one line at the top.
* **No Name Collisions:** You do not have to worry about accidentally using the same include guard macro name in two different header files.
* **Faster Compile Times:** The compiler does not even open the file a second time to parse preprocessor checks.

### Cons of `#pragma once`:
* **Non-Standard:** It is not part of the official C++ Standard (though supported by all major compilers like GCC, Clang, and MSVC).
* **Path Issues:** In very complex builds with symbolic links or network drives, the compiler might fail to recognize that two paths refer to the same physical file.

---

## Connection to the BraccioV2 Library

In `BraccioV2.h`, traditional include guards are used:
```cpp
#ifndef BRACCIOV2_H_
#define BRACCIOV2_H_

// All class declarations, constants, and defines go here...

#endif
```
This ensures that if you include `<BraccioV2.h>` in your Arduino sketch, and also include another library that happens to include `<BraccioV2.h>`, your project will compile without errors.

---

## Practice Exercises

### Exercise 1: Implement Include Guards
Add proper, traditional include guards to this vulnerable header file:
```cpp
// motor_limits.h
const int HARD_LIMIT_MAX = 180;
const int HARD_LIMIT_MIN = 0;
```

<details>
<summary><b>View Solution</b></summary>

```cpp
#ifndef MOTOR_LIMITS_H_
#define MOTOR_LIMITS_H_

const int HARD_LIMIT_MAX = 180;
const int HARD_LIMIT_MIN = 0;

#endif // MOTOR_LIMITS_H_
```
</details>

### Exercise 2: Identify the Guard Failure
Look at this header file. Why will it fail to prevent redefinition errors when included multiple times?
```cpp
#ifndef LED_PINS_H
#define LED_PIN_H

const int STATUS_LED = 13;

#endif
```

<details>
<summary><b>View Solution</b></summary>
The name used in the `#ifndef` check is `LED_PINS_H` (with an **S**), but the name defined in the next line is `LED_PIN_H` (without an **S**).

When the preprocessor reads this file a second time:
1. It checks `#ifndef LED_PINS_H`.
2. Since `LED_PINS_H` was never defined (only `LED_PIN_H` was), the condition remains true.
3. The preprocessor parses the content again, causing a redefinition compiler error.

Include guard macro names must match exactly across `#ifndef` and `#define`.
</details>

---

[Previous: Lesson 05](lesson05_headers.md) | [Next: Lesson 07](lesson07_preprocessor.md)
