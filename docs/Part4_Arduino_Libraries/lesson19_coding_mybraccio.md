# Lesson 19: Coding Your Custom `MyBraccio` Library

Now, you are ready to put everything you have learned together. You will build your own custom version of the Braccio robotic arm control library from scratch, which we will call **`MyBraccio`**. 

This library will model the 6-joint structure using composition (internal `Servo` objects), encapsulate joint angles using private state arrays, enforce limit boundaries, and implement the non-blocking coordinated motion engine.

---

## Step 1: Writing the Header File (`MyBraccio.h`)

Create a header file named `MyBraccio.h`. This file contains the declarations of our class members, constants, and external dependencies.

```cpp
#ifndef MYBRACCIO_H_
#define MYBRACCIO_H_

#include <Arduino.h>
#include <Servo.h>

// Joint Index Definitions
#define JOINT_BASE 0
#define JOINT_SHOULDER 1
#define JOINT_ELBOW 2
#define JOINT_WRIST 3
#define JOINT_WRIST_ROT 4
#define JOINT_GRIPPER 5

// Physical Arduino Pin Assignments
#define PIN_BASE 11
#define PIN_SHOULDER 10
#define PIN_ELBOW 9
#define PIN_WRIST 6
#define PIN_WRIST_ROT 5
#define PIN_GRIPPER 3

class MyBraccio {
public:
    MyBraccio();
    
    // Attaches servos to pins and sets initial center positions
    void begin();
    
    // Setter methods to set target positions (returns true if valid, false if clamped)
    bool setAllAbsolute(int b, int s, int e, int w, int w_r, int g);
    bool setOneAbsolute(int joint, int value);
    
    // Processes 1-degree movement increments towards target positions
    void update();
    
    // Non-blocking delay that keeps updating servo positions
    void safeDelay(int ms);

private:
    // Servo objects (Composition)
    Servo _servos[6];
    
    // Encapsulated configuration arrays
    int _jointMin[6];
    int _jointMax[6];
    int _currentPositions[6];
    int _targetPositions[6];
    
    // Helper method to route values to the physical Servo objects
    void _writeServo(int joint, int angle);
};

#endif
```

---

## Step 2: Writing the Source File (`MyBraccio.cpp`)

Create a source file named `MyBraccio.cpp`. This file contains the actual definitions and implementation logic for the declarations in `MyBraccio.h`.

```cpp
#include "MyBraccio.h"

// Constructor: Initializes limits and default center states
MyBraccio::MyBraccio() {
    // Set safety limits for each joint
    // Base, Shoulder, Elbow, Wrist, WristRot, Gripper
    _jointMin[JOINT_BASE] = 0;   _jointMax[JOINT_BASE] = 180;
    _jointMin[JOINT_SHOULDER] = 15; _jointMax[JOINT_SHOULDER] = 165;
    _jointMin[JOINT_ELBOW] = 0;   _jointMax[JOINT_ELBOW] = 180;
    _jointMin[JOINT_WRIST] = 0;   _jointMax[JOINT_WRIST] = 180;
    _jointMin[JOINT_WRIST_ROT] = 0;   _jointMax[JOINT_WRIST_ROT] = 180;
    _jointMin[JOINT_GRIPPER] = 10;  _jointMax[JOINT_GRIPPER] = 73; // Open/Close limits
    
    // Initialize current and target states to 90 (center), gripper to 50
    for (int i = 0; i < 5; ++i) {
        _currentPositions[i] = 90;
        _targetPositions[i] = 90;
    }
    _currentPositions[JOINT_GRIPPER] = 50;
    _targetPositions[JOINT_GRIPPER] = 50;
}

// Initializes hardware pins and moves servos to default positions
void MyBraccio::begin() {
    _servos[JOINT_BASE].attach(PIN_BASE);
    _servos[JOINT_SHOULDER].attach(PIN_SHOULDER);
    _servos[JOINT_ELBOW].attach(PIN_ELBOW);
    _servos[JOINT_WRIST].attach(PIN_WRIST);
    _servos[JOINT_WRIST_ROT].attach(PIN_WRIST_ROT);
    _servos[JOINT_GRIPPER].attach(PIN_GRIPPER);
    
    // Immediately write initial values to prevent starting jerks
    for (int i = 0; i < 6; ++i) {
        _writeServo(i, _currentPositions[i]);
    }
}

// Direct helper to write raw values to the standard Servo objects
void MyBraccio::_writeServo(int joint, int angle) {
    if (joint >= 0 && joint < 6) {
        _servos[joint].write(angle);
    }
}

// Sets target position for a single joint with bounds checking
bool MyBraccio::setOneAbsolute(int joint, int value) {
    if (joint < 0 || joint >= 6) return false;
    
    int clamped = constrain(value, _jointMin[joint], _jointMax[joint]);
    _targetPositions[joint] = clamped;
    
    return (clamped == value); // Returns true if no clamping occurred
}

// Sets target positions for all 6 joints
bool MyBraccio::setAllAbsolute(int b, int s, int e, int w, int w_r, int g) {
    bool ok = true;
    ok &= setOneAbsolute(JOINT_BASE, b);
    ok &= setOneAbsolute(JOINT_SHOULDER, s);
    ok &= setOneAbsolute(JOINT_ELBOW, e);
    ok &= setOneAbsolute(JOINT_WRIST, w);
    ok &= setOneAbsolute(JOINT_WRIST_ROT, w_r);
    ok &= setOneAbsolute(JOINT_GRIPPER, g);
    return ok;
}

// Increments/decrements positions towards targets (Non-Blocking Engine)
void MyBraccio::update() {
    for (int i = 0; i < 6; ++i) {
        int current = _currentPositions[i];
        int target = _targetPositions[i];
        
        if (current != target) {
            int step = (current < target) ? 1 : -1;
            _currentPositions[i] = current + step;
            _writeServo(i, _currentPositions[i]);
        }
    }
}

// Non-blocking safe delay loops while calling update()
void MyBraccio::safeDelay(int ms) {
    unsigned long startTime = millis();
    while (millis() - startTime < (unsigned long)ms) {
        update();
        delay(15); // Wait 15ms between step updates for smooth rotation speed
    }
}
```

---

## Why this design is professional

This code incorporates multiple C++ and software engineering best practices:
1. **Separation of Concerns:** Hardware connections are encapsulated in `begin()`, state management inside the member arrays, and bounds checks in `setOneAbsolute()`.
2. **Safety Clamping:** Using the `constrain()` utility ensures that the physical hardware is never commanded to go past safety limit thresholds, even if the user enters bad numbers.
3. **Implicit Step Interpolation:** By modifying positions in increments of `1` inside `update()`, the robot moves all joints concurrently, creating coordinated, smooth multi-axis sweeps.

---

## Practice Exercises

### Exercise 1: Add a Joint Speed Multiplier
Modify the `update()` method logic conceptually. Suppose we want to support speed steps (deltas) of different values per joint rather than a fixed step of `1` (for example, base rotates fast at 2 degrees per step, but the gripper closes slowly at 1 degree per step). How would you modify the class properties and `update()` loop?

<details>
<summary><b>View Solution</b></summary>

1. **Add a private speed array in `MyBraccio.h`:**
   ```cpp
   int _jointDelta[6];
   ```
2. **Initialize speed values inside the constructor in `MyBraccio.cpp`:**
   ```cpp
   for (int i = 0; i < 6; ++i) {
       _jointDelta[i] = 1; // Default to 1 degree steps
   }
   _jointDelta[JOINT_BASE] = 2; // Make base move faster
   ```
3. **Update the stepping logic inside `update()`:**
   ```cpp
   void MyBraccio::update() {
       for (int i = 0; i < 6; ++i) {
           int current = _currentPositions[i];
           int target = _targetPositions[i];
           
           if (current != target) {
               int diff = target - current;
               int step = (diff > 0) ? _jointDelta[i] : -_jointDelta[i];
               
               // Prevent overshooting if the step is larger than the remaining distance
               if (abs(step) > abs(diff)) {
                   step = diff;
               }
               
               _currentPositions[i] = current + step;
               _writeServo(i, _currentPositions[i]);
           }
       }
   }
   ```
</details>

### Exercise 2: Add a Reset Method
Declare and implement a public method `void reset()` that sets the target of all joints to their default center positions, then calls `safeDelay(2000)` to let the arm return to rest safely.

<details>
<summary><b>View Solution</b></summary>

**In `MyBraccio.h` (public section):**
```cpp
void reset();
```

**In `MyBraccio.cpp`:**
```cpp
void MyBraccio::reset() {
    // Set all joint targets to default centers
    setAllAbsolute(90, 90, 90, 90, 90, 50);
    // Wait for the arm to move there
    safeDelay(2000);
}
```
</details>

---

[Previous: Lesson 18](lesson18_bracciov2_deep_dive.md) | [Next: Lesson 20](lesson20_deployment.md)
