# Project 01: Creating a Multi-Joint Robot Simulation

Now that you have completed Parts 1, 2, and 3, you are ready to combine your C++ syntax, object-oriented design, and memory management skills. 

In this project, you will build a **Console-Based Robotic Arm Simulator** in C++. This program runs directly on your computer, simulating joint angles, velocity updates, and safety limit checks.

---

## Project Specification

You will create a simulation consisting of two classes:
1. **`Joint` Class:** Models a single servo motor.
   * Private members: `currentAngle`, `targetAngle`, `minLimit`, `maxLimit`, `speed`.
   * Public members: Constructor, getter methods, setter methods (with bounds check), and an `update()` method that steps the current angle closer to the target.
2. **`RoboticArm` Class:** Models the arm as a whole.
   * Contains an array of 4 `Joint` objects (Base, Shoulder, Elbow, Wrist) using composition.
   * Public members: Constructor, a method to command all joints, an `update()` method, and a method to print the physical positions.

---

## Step-by-Step Implementation

Create a file named `arm_simulator.cpp` and implement the following sections:

### 1. The `Joint` Class
```cpp
#include <iostream>
#include <cmath>

class Joint {
private:
    int currentAngle;
    int targetAngle;
    const int minLimit;
    const int maxLimit;
    const int speed;

public:
    // Parameterized constructor using member initializer lists
    Joint(int minL, int maxL, int defaultPos, int spd) 
        : currentAngle(defaultPos), targetAngle(defaultPos), minLimit(minL), maxLimit(maxL), speed(spd) {}

    // Getters
    int getCurrentAngle() const { return currentAngle; }
    int getTargetAngle() const { return targetAngle; }

    // Setter with safety clamping
    bool setTarget(int target) {
        int clamped = target;
        if (target < minLimit) clamped = minLimit;
        if (target > maxLimit) clamped = maxLimit;
        targetAngle = clamped;
        return (clamped == target);
    }

    // Increments/decrements position towards target based on joint speed
    void update() {
        if (currentAngle != targetAngle) {
            int diff = targetAngle - currentAngle;
            int step = (diff > 0) ? speed : -speed;
            
            // Avoid overshooting
            if (std::abs(step) > std::abs(diff)) {
                step = diff;
            }
            
            currentAngle += step;
        }
    }
};
```

### 2. The `RoboticArm` Class
```cpp
class RoboticArm {
private:
    // Composition: Array of 4 Joint objects
    // Joints: 0 = Base, 1 = Shoulder, 2 = Elbow, 3 = Wrist
    Joint joints[4];

public:
    // Constructor initializes each Joint object with custom limits and speeds
    RoboticArm() : joints{
        Joint(0, 180, 90, 2),  // Base: limits 0-180, center 90, speed 2
        Joint(15, 165, 90, 1), // Shoulder: limits 15-165, center 90, speed 1 (slow!)
        Joint(0, 180, 90, 3),  // Elbow: limits 0-180, center 90, speed 3 (fast!)
        Joint(0, 180, 90, 2)   // Wrist: limits 0-180, center 90, speed 2
    } {}

    // Set targets for all joints
    void commandAll(int b, int s, int e, int w) {
        joints[0].setTarget(b);
        joints[1].setTarget(s);
        joints[2].setTarget(e);
        joints[3].setTarget(w);
    }

    // Updates all joints
    void update() {
        for (int i = 0; i < 4; ++i) {
            joints[i].update();
        }
    }

    // Check if the arm is still in motion
    bool isMoving() const {
        for (int i = 0; i < 4; ++i) {
            if (joints[i].getCurrentAngle() != joints[i].getTargetAngle()) {
                return true;
            }
        }
        return false;
    }

    // Prints status in a clean console table
    void printStatus() const {
        std::cout << "Base: " << joints[0].getCurrentAngle() 
                  << " | Shoulder: " << joints[1].getCurrentAngle() 
                  << " | Elbow: " << joints[2].getCurrentAngle() 
                  << " | Wrist: " << joints[3].getCurrentAngle() << std::endl;
    }
};
```

### 3. The `main()` Driver Loop
```cpp
int main() {
    RoboticArm simulator;
    
    std::cout << "Starting Robot Arm Simulator..." << std::endl;
    simulator.printStatus();
    
    // Command the arm to new positions
    std::cout << "\nCommanding Base -> 45, Shoulder -> 30, Elbow -> 120, Wrist -> 180..." << std::endl;
    simulator.commandAll(45, 30, 120, 180);
    
    // Simulation execution loop
    int steps = 0;
    while (simulator.isMoving() && steps < 100) {
        simulator.update();
        std::cout << "Step " << ++steps << " -> ";
        simulator.printStatus();
    }
    
    std::cout << "\nTarget positions reached in " << steps << " steps!" << std::endl;
    return 0;
}
```

---

## Compiling and Running

Compile the simulator program in your terminal:

```bash
g++ -std=c++17 -Wall arm_simulator.cpp -o arm_simulator
```

Run the compiled executable:

```bash
./arm_simulator
```

Observe how different joints reach their targets at different times. Because the Shoulder has a speed of `1`, it takes the longest to complete its sweep, whereas the Elbow (speed `3`) reaches its target rapidly.

---

## Practice Exercises

### Exercise 1: Collision Prevention logic
Suppose that if the Shoulder angle is less than `30` degrees, the Elbow angle **must not** exceed `110` degrees to prevent the forearm from physically colliding with the base shield of the robot. 
How would you modify the `RoboticArm::commandAll` method to enforce this spatial safety collision rule?

<details>
<summary><b>View Solution</b></summary>

Modify `commandAll()` to check the inputs relative to each other:

```cpp
void commandAll(int b, int s, int e, int w) {
    // If the shoulder is being commanded lower than 30
    if (s < 30) {
        std::cout << "[COLLISION SAFETY] Shoulder is low (<30). Clamping Elbow target to safe limit (110)." << std::endl;
        if (e > 110) e = 110;
    }
    
    joints[0].setTarget(b);
    joints[1].setTarget(s);
    joints[2].setTarget(e);
    joints[3].setTarget(w);
}
```
</details>

---

[Previous: Part 4 - Lesson 20](../Part4_Arduino_Libraries/lesson20_deployment.md) | [Next: Practice Workbook](../Exercises/exercises_solutions.md)
