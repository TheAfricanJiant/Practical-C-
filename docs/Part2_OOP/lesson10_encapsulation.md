# Lesson 10: Encapsulation & Bounds Checking

One of the key tenets of Object-Oriented Programming is **Encapsulation**. Encapsulation refers to the bundling of data (member variables) and the methods that operate on that data into a single unit (a class), while **restricting direct access** to the object's internal components.

In this lesson, we will explore why public variables are hazardous in robotics and how to protect our objects using private members, getters, and setters.

---

## The Danger of Public Variables

If your class members are all `public`, any code in your project can modify them directly without restriction. 

Consider this vulnerable robot joint definition:

```cpp
class VulnerableJoint {
public:
    int angle; // Direct public access!
};
```
If another developer (or you, by accident) writes:
```cpp
VulnerableJoint joint;
joint.angle = 9999; // EXTREME BUG!
```
This compile successfully. However, sending a `9999` command to a real physical servo designed to rotate only between `0` and `180` degrees could draw huge current, overheat the motor, strip the gears, and snap the mechanical linkages.

---

## Data Hiding with Getters & Setters

To prevent this, we hide data by declaring variables as `private`. We then expose controlled entry points using public functions:
* **Getter (Accessor):** A read-only public function that returns the value of a private variable.
* **Setter (Mutator):** A public function that allows modifying the value, but **enforces validation rules** (such as range validation or clamping) before saving it.

Let's write a secure, encapsulated joint model:

```cpp
#include <iostream>

class SafeJoint {
private:
    // These variables cannot be accessed directly from outside the class!
    int currentAngle;
    const int minLimit = 15;
    const int maxLimit = 165;

public:
    // Constructor initializes the joint to a safe center position
    SafeJoint() : currentAngle(90) {}

    // Getter: Allows other code to read the angle, but not write to it directly
    int getAngle() {
        return currentAngle;
    }

    // Setter: Performs safety checks before updating the internal angle
    void setAngle(int target) {
        if (target < minLimit) {
            std::cout << "Warning: " << target << " is below limit. Clamping to " << minLimit << std::endl;
            currentAngle = minLimit;
        } else if (target > maxLimit) {
            std::cout << "Warning: " << target << " is above limit. Clamping to " << maxLimit << std::endl;
            currentAngle = maxLimit;
        } else {
            currentAngle = target;
        }
    }
};

int main() {
    SafeJoint elbow;

    // elbow.currentAngle = 180; // ERROR: currentAngle is private!

    elbow.setAngle(120);  // Safe and valid
    std::cout << "Elbow Angle: " << elbow.getAngle() << std::endl;

    elbow.setAngle(200);  // Triggers warning and clamps to 165!
    std::cout << "Elbow Angle after bad command: " << elbow.getAngle() << std::endl;

    return 0;
}
```

---

## Connection to the BraccioV2 Library

The BraccioV2 library uses encapsulation to protect the physical limits of the robotic arm.

In the class declaration in `BraccioV2.h`, the arrays that store the joint configurations are kept under the `private:` section:
```cpp
class Braccio {
  // ...
  private:
    int _jointMax[7] = {180, 165, 180, 180, 180, 73};
    int _jointMin[7] = {0, 15, 0, 0, 0, 10};
    int _currentJointPositions[7];
};
```
If a user wants to customize the maximum range of a joint, they cannot write `braccio._jointMax[0] = 120;`. Instead, they must call the public setter method:
```cpp
void Braccio::setJointMax(int joint, int value) {
  _jointMax[joint] = constrain(value, GLOBAL_MIN, GLOBAL_MAX);
}
```
This setter guarantees that even if a user passes a wild value, the library clamps it using the `constrain()` function against the absolute global safe boundaries of the servo technology (`GLOBAL_MIN` = 0, `GLOBAL_MAX` = 180).

---

## Practice Exercises

### Exercise 1: Encapsulate a Robot Gripper
Write a class named `Gripper` with:
* A private member variable `int width` (in millimeters).
* The physical limits of the gripper are: minimum `10` mm (completely closed), maximum `73` mm (completely open).
* A constructor that defaults `width` to `50` mm.
* A public getter `int getWidth()` that returns the value.
* A public setter `void setWidth(int target)` that checks boundaries. If the target is below `10`, clamp it to `10`. If it is above `73`, clamp it to `73`.

Test your class in `main()` by writing valid and invalid values.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

class Gripper {
private:
    int width;
    const int MIN_WIDTH = 10;
    const int MAX_WIDTH = 73;

public:
    // Constructor
    Gripper() : width(50) {}

    // Getter
    int getWidth() const {
        return width;
    }

    // Setter
    void setWidth(int target) {
        if (target < MIN_WIDTH) {
            width = MIN_WIDTH;
        } else if (target > MAX_WIDTH) {
            width = MAX_WIDTH;
        } else {
            width = target;
        }
    }
};

int main() {
    Gripper roboticGripper;

    roboticGripper.setWidth(30); // Valid
    std::cout << "Gripper Width: " << roboticGripper.getWidth() << " mm" << std::endl;

    roboticGripper.setWidth(5); // Below limit
    std::cout << "Gripper Width (clamped): " << roboticGripper.getWidth() << " mm" << std::endl;

    roboticGripper.setWidth(100); // Above limit
    std::cout << "Gripper Width (clamped): " << roboticGripper.getWidth() << " mm" << std::endl;

    return 0;
}
```
</details>

### Exercise 2: Why hide limits?
In our `SafeJoint` class, the constants `minLimit` and `maxLimit` are also marked as `private`. Explain why it is best practice to keep limit constants private rather than public.

<details>
<summary><b>View Solution</b></summary>
Keeping limits private serves two major purposes:
1. **Prevents tampering:** If limits were public, external code could write `joint.maxLimit = 180;` and then call `joint.setAngle(180)`, bypassing the physical safety boundaries designed for the shoulder.
2. **Encapsulates configuration details:** The outside code calling the joint doesn't need to manage or worry about the inner workings of what the limits are. If the physical structure of the arm is upgraded in a future hardware version and the limits change, we only have to update the private variables in our class. The external interface (`setAngle`) remains identical.
</details>

---

[Previous: Lesson 09](lesson09_constructors.md) | [Next: Lesson 11](lesson11_methods.md)
