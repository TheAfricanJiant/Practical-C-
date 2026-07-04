# Practice Workbook & Detailed Solutions

This workbook contains practice exercises designed to test your knowledge of C++ and Arduino library design, organized by the four main sections of the course. Each question is followed by its corresponding solution in the **Solutions Section** at the bottom of the page.

---

## ✏️ Part 1: C++ Basics & Compilation (Lessons 1-7)

### Question 1.1 (Easy): Basic Console Output
Write a program `hello_braccio.cpp` that outputs: `Braccio Simulator Online` to the console. Compile it with `-Wall` using `g++`.

### Question 1.2 (Medium): Type Bounds Checking
Write a program that declares a variable `currentAngle` of type `uint8_t` initialized to `180`. Add `100` to the variable. Print the value. Explain the resulting output.

### Question 1.3 (Hard): Modular Math Header
Create a header file `math_utils.h` that declares a function `int getAngleDifference(int startAngle, int endAngle);`. Create `math_utils.cpp` that defines it to return the shortest rotation angle difference between two positions (can be negative). Include it in `main.cpp` and test it with start `45` and end `135`, compiling them together.

---

## ✏️ Part 2: Object-Oriented Programming (Lessons 8-11)

### Question 2.1 (Easy): Class Instantiation
Define a class named `Camera` with a public member `bool recording` and a method `void toggle()` that flips the boolean status. Instantiate it in `main()`.

### Question 2.2 (Medium): Safe Bounds Mutator
Write a class named `Joint` that stores a private `int angle`. Add a constructor that defaults `angle` to `90`. Implement a setter `bool write(int target)` that only updates `angle` if the target is between `0` and `180` inclusive, returning `true`. If invalid, do not update and return `false`.

### Question 2.3 (Hard): Constructor Initialization
Write a class named `Actuator` containing a private `const int pin` and a private `float currentDutyCycle`. Implement a parameterized constructor that initializes `pin` using a **member initializer list** and initializes `currentDutyCycle` to `0.0`. Add getter functions marked with the appropriate read-only `const` qualifiers.

---

## ✏️ Part 3: Pointers, References & Memory (Lessons 12-15)

### Question 3.1 (Easy): Pointer Manipulation
Write a code snippet that declares an integer `voltage = 5`. Declare a pointer `vPtr` pointing to `voltage`. Modify `voltage` to `6` *only* by dereferencing the pointer.

### Question 3.2 (Medium): Pass-by-Const-Reference
Write a structure `JointLimits` that holds `int min` and `int max`. Write a function `void printLimits(const JointLimits& limits)` that prints the limits. Explain why we pass by `const reference` rather than by value or by normal reference.

### Question 3.3 (Hard): Dynamic Array of Joints
Write a program that uses `new` to dynamically allocate a C-style array of `5` integers on the Heap representing joint positions. Initialize all positions to `90` using a loop, print them, and then safely deallocate the array using `delete[]` to prevent memory leaks.

---

## ✏️ Part 4: Building Custom Arduino Libraries (Lessons 16-20)

### Question 4.1 (Easy): Library Metadata
Write a complete, valid `library.properties` file for a custom Arduino library named `ServoInterpolator` written by you, version `2.1.0`, targeting all architectures.

### Question 4.2 (Medium): Non-Blocking Delay Loop
Write an Arduino function `void blinkNonBlocking(int pin, int durationMs)` that blinks an LED on `pin` for the specified `durationMs` *without* freezing other code. You must implement a loop that polls `millis()` and blinks the LED, yielding control or processing other actions in between.

---

## 🔑 Master Solutions Section

---

### Solutions for Part 1

#### Solution 1.1:
**`hello_braccio.cpp`**
```cpp
#include <iostream>

int main() {
    std::cout << "Braccio Simulator Online" << std::endl;
    return 0;
}
```
Compile command:
```bash
g++ -std=c++17 -Wall hello_braccio.cpp -o hello_braccio
```

#### Solution 1.2:
**`overflow_test.cpp`**
```cpp
#include <iostream>
#include <cstdint>

int main() {
    uint8_t currentAngle = 180;
    
    // Adding 100 exceeds the maximum limit of an unsigned 8-bit integer (255)
    currentAngle = currentAngle + 100;
    
    std::cout << "Resulting value: " << (int)currentAngle << std::endl;
    return 0;
}
```
* **Explanation of Output:** The output is `24`. An `uint8_t` has a range of `0` to `255`. When you add `100` to `180`, the value wraps around (overflows): $180 + 100 = 280$. $280 - 256 = 24$.

#### Solution 1.3:
**`math_utils.h`**
```cpp
#ifndef MATH_UTILS_H
#define MATH_UTILS_H

int getAngleDifference(int startAngle, int endAngle);

#endif
```

**`math_utils.cpp`**
```cpp
#include "math_utils.h"

int getAngleDifference(int startAngle, int endAngle) {
    return endAngle - startAngle;
}
```

**`main.cpp`**
```cpp
#include <iostream>
#include "math_utils.h"

int main() {
    int diff = getAngleDifference(45, 135);
    std::cout << "Angle Difference: " << diff << " degrees." << std::endl;
    return 0;
}
```
Compile command:
```bash
g++ -std=c++17 -Wall main.cpp math_utils.cpp -o math_app
```

---

### Solutions for Part 2

#### Solution 2.1:
```cpp
#include <iostream>

class Camera {
public:
    bool recording;

    Camera() : recording(false) {}

    void toggle() {
        recording = !recording;
    }
};

int main() {
    Camera cam;
    std::cout << "Recording status: " << cam.recording << std::endl;
    cam.toggle();
    std::cout << "Recording status after toggle: " << cam.recording << std::endl;
    return 0;
}
```

#### Solution 2.2:
```cpp
#include <iostream>

class Joint {
private:
    int angle;

public:
    Joint() : angle(90) {}

    int getAngle() const { return angle; }

    bool write(int target) {
        if (target >= 0 && target <= 180) {
            angle = target;
            return true;
        }
        return false;
    }
};

int main() {
    Joint elbow;
    if (elbow.write(120)) {
        std::cout << "Angle updated: " << elbow.getAngle() << std::endl;
    }
    if (!elbow.write(250)) {
        std::cout << "Failed to write 250 (out of bounds!). Angle remains: " << elbow.getAngle() << std::endl;
    }
    return 0;
}
```

#### Solution 2.3:
```cpp
#include <iostream>

class Actuator {
private:
    const int pin;
    float currentDutyCycle;

public:
    // Member initializer list is mandatory for const pin
    Actuator(int p) : pin(p), currentDutyCycle(0.0f) {}

    // Getters marked const
    int getPin() const { return pin; }
    float getDutyCycle() const { return currentDutyCycle; }
};

int main() {
    Actuator driveMotor(5);
    std::cout << "Motor on Pin: " << driveMotor.getPin() << " initialized at duty cycle: " << driveMotor.getDutyCycle() << std::endl;
    return 0;
}
```

---

### Solutions for Part 3

#### Solution 3.1:
```cpp
#include <iostream>

int main() {
    int voltage = 5;
    int* vPtr = &voltage;
    
    // Modify value by dereferencing
    *vPtr = 6;
    
    std::cout << "New voltage: " << voltage << std::endl; // Prints 6
    return 0;
}
```

#### Solution 3.2:
```cpp
#include <iostream>

struct JointLimits {
    int min;
    int max;
};

// Passed by Const Reference
void printLimits(const JointLimits& limits) {
    std::cout << "Min: " << limits.min << ", Max: " << limits.max << std::endl;
}

int main() {
    JointLimits shoulder = {15, 165};
    printLimits(shoulder);
    return 0;
}
```
* **Why pass by const reference?** 
  1. Passing by reference (`&`) passes the memory address of `shoulder` directly, avoiding copy constructor overhead and saving CPU speed and RAM.
  2. The `const` qualifier guarantees that the function cannot modify the original struct properties, ensuring safety similar to passing by value.

#### Solution 3.3:
```cpp
#include <iostream>

int main() {
    // 1. Allocate dynamic array on the Heap
    int* positions = new int[5];

    // 2. Initialize elements
    for (int i = 0; i < 5; ++i) {
        positions[i] = 90;
    }

    // 3. Print
    for (int i = 0; i < 5; ++i) {
        std::cout << "Joint " << i << ": " << positions[i] << std::endl;
    }

    // 4. Safely deallocate (use delete[] for arrays!)
    delete[] positions;
    positions = nullptr;

    std::cout << "Array memory successfully deallocated." << std::endl;
    return 0;
}
```

---

### Solutions for Part 4

#### Solution 4.1:
**`library.properties`**
```ini
name=ServoInterpolator
version=2.1.0
author=Your Name <your.email@example.com>
maintainer=Your Name <your.email@example.com>
sentence=A library for interpolating servo positions smoothly.
paragraph=Calculates intermediate movement step positions to avoid physical gear stress.
category=Device Control
architectures=*
```

#### Solution 4.2:
```cpp
#include <Arduino.h>

void blinkNonBlocking(int pin, int durationMs) {
    pinMode(pin, OUTPUT);
    unsigned long startTime = millis();
    unsigned long lastToggleTime = 0;
    bool ledState = false;

    while (millis() - startTime < (unsigned long)durationMs) {
        // Toggle the LED every 500ms
        if (millis() - lastToggleTime >= 500) {
            ledState = !ledState;
            digitalWrite(pin, ledState ? HIGH : LOW);
            lastToggleTime = millis();
        }
        
        // yield() tells the processor to handle background WiFi/USB events (crucial on ESP/ARM boards)
        yield(); 
    }
}
```
