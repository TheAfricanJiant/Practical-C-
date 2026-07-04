# Lesson 03: Control Flow & Decision Making

Robotics is all about decision making. A robotic arm must decide:
* "Is the target angle within the physical limits of the shoulder joint?"
* "Has the servo reached its destination, or must we keep updating its position?"
* "Which joint needs to move right now?"

In C++, these decisions are controlled by **conditional statements** (`if`, `switch-case`) and **loops** (`for`, `while`).

---

## Conditionals: Making Decisions

### 1. The `if` / `else if` / `else` Statement
This construct evaluates a boolean expression. If it is `true`, a block of code runs. If it is `false`, the program moves to the next condition.

```cpp
#include <iostream>

int main() {
    int requestedAngle = 175;
    const int MAX_SAFE_ANGLE = 165;

    if (requestedAngle > MAX_SAFE_ANGLE) {
        std::cout << "Warning: Requested angle " << requestedAngle 
                  << " exceeds safety limit of " << MAX_SAFE_ANGLE << "!" << std::endl;
        std::cout << "Clamping to max limit." << std::endl;
        requestedAngle = MAX_SAFE_ANGLE;
    } else if (requestedAngle < 0) {
        std::cout << "Warning: Angle cannot be negative! Clamping to 0." << std::endl;
        requestedAngle = 0;
    } else {
        std::cout << "Angle is within safe boundaries." << std::endl;
    }

    std::cout << "Final angle: " << requestedAngle << std::endl;
    return 0;
}
```

### 2. Logical Operators
When evaluating complex conditions, we use logical operators to combine boolean statements:
* `&&` (Logical AND): Returns true if *both* operands are true.
* `||` (Logical OR): Returns true if *at least one* operand is true.
* `!` (Logical NOT): Reverses the logical state (turns true to false and vice-versa).

```cpp
// Check if the angle is valid for the elbow (between 0 and 180 inclusive)
if (angle >= 0 && angle <= 180) {
    // Move joint
}
```

### 3. The `switch-case` Statement
When you need to compare a single integer or character variable against multiple fixed values, `switch-case` is cleaner and faster than a long chain of `if-else` blocks.

```cpp
#include <iostream>

int main() {
    int jointID = 2; // Let's assume 0 = Base, 1 = Shoulder, 2 = Elbow

    switch (jointID) {
        case 0:
            std::cout << "Controlling Base Rotation." << std::endl;
            break;
        case 1:
            std::cout << "Controlling Shoulder Joint." << std::endl;
            break;
        case 2:
            std::cout << "Controlling Elbow Joint." << std::endl;
            break;
        default:
            std::cout << "Unknown Joint ID!" << std::endl;
            break;
    }
    return 0;
}
```
> [!WARNING]
> Remember the `break;` statement at the end of each case. Without it, the program will execute all subsequent cases (this is called "fall-through"), which is usually a bug.

---

## Loops: Repeating Actions

### 1. The `for` Loop
A `for` loop is used when you know exactly how many times you need to repeat an action. In robotics, this is frequently used to loop through arrays containing joint values.

```cpp
#include <iostream>

int main() {
    // Loop from index 0 to 5 (6 joints total)
    for (int joint = 0; joint < 6; ++joint) {
        std::cout << "Updating joint index: " << joint << std::endl;
    }
    return 0;
}
```
A `for` loop has three statements:
1. `int joint = 0`: Initialization (runs once before the loop starts).
2. `joint < 6`: Condition (evaluated before each iteration; loop stops when false).
3. `++joint`: Increment (runs at the end of each iteration).

### 2. The `while` Loop
A `while` loop runs as long as a condition remains true. It is useful when you do not know the exact number of iterations beforehand.

```cpp
#include <iostream>

int main() {
    int currentAngle = 45;
    int targetAngle = 50;
    int stepSize = 1;

    // Keep moving until we reach the target
    while (currentAngle != targetAngle) {
        currentAngle += stepSize;
        std::cout << "Moving... Current angle: " << currentAngle << std::endl;
    }

    std::cout << "Target reached!" << std::endl;
    return 0;
}
```

---

## Connection to the BraccioV2 Library

In `BraccioV2.cpp`, control flow is everywhere:
* In `_setServo()`, a `switch-case` statement checks which joint constant (`BASE_ROT`, `SHOULDER`, etc.) was passed and routes the write instruction to the correct `Servo` object.
* In `update()`, the library performs actions sequentially for each joint:
  ```cpp
  _moveServo(BASE_ROT);
  _moveServo(SHOULDER);
  // ...
  ```
* In `_moveServo()`, an `if` condition checks if the joint has reached its target:
  ```cpp
  if (currentPos != targetPos) {
      // Calculate delta direction and write new angle
  }
  ```

---

## Practice Exercises

### Exercise 1: Range Validator
Write a program that takes an integer `angle` and checks if it is inside the safe operating envelope of the shoulder joint (minimum limit: `15`, maximum limit: `165`). If it is within limits, print "Angle Safe". If not, print "Out of Bounds".

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

int main() {
    int angle = 100; // Try changing this value to test
    const int MIN_LIMIT = 15;
    const int MAX_LIMIT = 165;

    if (angle >= MIN_LIMIT && angle <= MAX_LIMIT) {
        std::cout << "Angle Safe" << std::endl;
    } else {
        std::cout << "Out of Bounds" << std::endl;
    }

    return 0;
}
```
</details>

### Exercise 2: Slow Sweep Simulator
Write a loop that simulates a servo sweeping from `0` degrees to `90` degrees in steps of `5` degrees. Print the angle on each step.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

int main() {
    for (int angle = 0; angle <= 90; angle += 5) {
        std::cout << "Sweep angle: " << angle << " degrees." << std::endl;
    }
    return 0;
}
```
</details>

---

[Previous: Lesson 02](lesson02_variables.md) | [Next: Lesson 04](lesson04_functions.md)
