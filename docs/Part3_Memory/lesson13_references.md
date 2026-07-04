# Lesson 13: References & Pass-by-Reference

In Lesson 04, we saw that passing variables into functions copies them (pass-by-value). If the variable is small (like an `int`), copying is cheap. But if we are passing large data structures (like a class object representing our robot or a coordinate matrix), copying consumes valuable memory and CPU cycles.

C++ introduces **References** as a cleaner, safer, and more efficient way to access and pass variables without copying.

---

## What is a Reference?

A reference is an **alias** (a nickname) for an already existing variable. Once initialized, the reference acts as another name for the original variable. Any operations performed on the reference are actually performed on the original variable.

A reference is declared using the `&` symbol during variable declaration:

```cpp
#include <iostream>

int main() {
    int angle = 90;
    int& ref = angle; // 'ref' is now a reference (alias) to 'angle'

    std::cout << "Original angle: " << angle << std::endl;
    std::cout << "Reference value: " << ref << std::endl;

    ref = 120; // Modify the reference
    std::cout << "Original angle after modify: " << angle << std::endl; // Now 120!

    return 0;
}
```

---

## Pointers vs. References

Although they both allow you to access variables indirectly, pointers and references have critical differences:

| Feature | Pointer (`int*`) | Reference (`int&`) |
| :--- | :--- | :--- |
| **Syntax** | Requires `*` and `&` to address/dereference | Uses normal variable syntax |
| **Initialization** | Can be declared without initializing | **Must** be initialized immediately |
| **Nullability** | Can be `nullptr` | Cannot be null (must refer to a real variable) |
| **Reassignability**| Can change to point to a different address | Cannot be changed to refer to another variable |

Because references cannot be null and do not require dereferencing syntax, they are generally **safer and cleaner** than pointers.

---

## Pass-by-Value vs. Pass-by-Reference

Let's look at how references solve the limitation of copying variables inside functions.

### 1. Modifying Original Variables
By passing a parameter by reference, the function receives a direct alias to the original variable, enabling the function to modify it.

```cpp
#include <iostream>

// Takes an int by reference (note the '&')
void increaseAngle(int& currentAngle) {
    currentAngle += 10; // Modifies the original variable in main()!
}

int main() {
    int shoulderAngle = 45;
    increaseAngle(shoulderAngle);
    std::cout << "Shoulder Angle: " << shoulderAngle << std::endl; // Prints 55!
    return 0;
}
```

### 2. Efficiency & Const References
When passing large objects, we pass them by reference to avoid copying. But what if we do not want the function to modify the original object?

We wrap it in a **Const Reference** (`const type&`). This gives us the performance benefit of reference passing (zero copying) with the safety of pass-by-value (read-only protection).

```cpp
#include <iostream>
#include <string>

struct RobotStatus {
    std::string name;
    int batteryLevel;
    float currentVoltage;
};

// Passed by Const Reference: No copy is made, but function cannot modify 'status'!
void displayStatus(const RobotStatus& status) {
    std::cout << "Robot: " << status.name << " | Battery: " << status.batteryLevel << "%" << std::endl;
    // status.batteryLevel = 0; // ERROR: assignment of member in read-only object!
}

int main() {
    RobotStatus braccio = {"Braccio V2", 95, 5.0f};
    displayStatus(braccio);
    return 0;
}
```

---

## Connection to the BraccioV2 Library

In C++ and Arduino library design, passing by reference is standard:
* If you pass a helper class or object (like a software serial logger or an display driver) to a robot class, you pass it by reference:
  ```cpp
  void beginLogger(SoftwareSerial& serialPort);
  ```
This ensures the library interacts directly with the single active hardware serial object, rather than creating a useless copy that wouldn't compile or function correctly.

---

## Practice Exercises

### Exercise 1: Safe Multi-Return Function
Functions in C++ can only return a single value. However, you can use reference parameters to get multiple outputs from a single function!

Write a function named `getJointLimits` that takes:
* An input `int jointID`.
* An output parameter `int& minOut` passed by reference.
* An output parameter `int& maxOut` passed by reference.

Inside the function, set the output references depending on `jointID` (e.g. if `jointID == 1`, set limits to `15` and `165`; otherwise `0` and `180`). Test it in `main()` to retrieve and print limits.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

void getJointLimits(int jointID, int& minOut, int& maxOut) {
    if (jointID == 1) { // Let's say 1 is the Shoulder
        minOut = 15;
        maxOut = 165;
    } else {
        minOut = 0;
        maxOut = 180;
    }
}

int main() {
    int minLimit = 0;
    int maxLimit = 0;

    // Retrieve limits for joint 1 (Shoulder)
    getJointLimits(1, minLimit, maxLimit);

    std::cout << "Shoulder Min Limit: " << minLimit << std::endl;
    std::cout << "Shoulder Max Limit: " << maxLimit << std::endl;

    return 0;
}
```
</details>

### Exercise 2: spot the Reference Bug
Why will the following code fail to compile?
```cpp
#include <iostream>

int main() {
    int& ref;
    int angle = 90;
    ref = angle;
    return 0;
}
```

<details>
<summary><b>View Solution</b></summary>
The declaration:
```cpp
int& ref;
```
fails to compile with the error:
`error: 'ref' declared as reference but not initialized`.

Unlike pointers, **references must be initialized immediately at the moment they are declared.** They cannot be declared "empty" and bound to a variable later.

**Correct declaration:**
```cpp
int angle = 90;
int& ref = angle;
```
</details>

---

[Previous: Lesson 12](lesson12_pointers.md) | [Next: Lesson 14](lesson14_dynamic_memory.md)
