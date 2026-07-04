# Lesson 09: Constructors & Member Initializers

When you create a C++ object, its member variables contain whatever random bits happened to be in memory at that address, unless you initialize them. In robotics, uninitialized values can be catastrophic: if a servo angle defaults to a random garbage value (like `-14325`), writing that to a real physical motor could damage the gears or break the linkages.

To solve this, C++ uses **Constructors** to guarantee that every object is initialized to a safe, predictable state upon creation.

---

## What is a Constructor?

A constructor is a special member function that is automatically called when an object of a class is instantiated. 

Key features of constructors:
* They have the **exact same name** as the class.
* They **do not have a return type** (not even `void`).
* They can be overloaded (you can define multiple constructors with different parameters).

---

## Default vs. Parameterized Constructors

### 1. Default Constructor
A default constructor takes no arguments. It initializes the object with standard default values.

```cpp
#include <iostream>

class Joint {
public:
    int currentAngle;

    // Default Constructor
    Joint() {
        currentAngle = 90; // All joints default to 90 degrees (center)
        std::cout << "Default Joint created at center position." << std::endl;
    }
};

int main() {
    Joint defaultJoint; // Calls Joint() automatically
    std::cout << "Angle: " << defaultJoint.currentAngle << std::endl;
    return 0;
}
```

### 2. Parameterized Constructor
A parameterized constructor accepts arguments, allowing you to configure the object at the moment it is created.

```cpp
#include <iostream>

class Joint {
public:
    int pin;
    int currentAngle;

    // Parameterized Constructor
    Joint(int p, int startAngle) {
        pin = p;
        currentAngle = startAngle;
        std::cout << "Joint configured on pin " << pin << " starting at " << currentAngle << " degrees." << std::endl;
    }
};

int main() {
    // Instantiate objects using the parameterized constructor
    Joint shoulder(10, 45); // Pin 10, angle 45
    Joint elbow(9, 90);     // Pin 9, angle 90
    return 0;
}
```

---

## Member Initializer Lists (Modern C++)

In the constructors above, we assigned values to member variables *inside the body* of the constructor using the assignment operator `=`. 

While this works, C++ provides a much better, more efficient mechanism called the **Member Initializer List**. This initializes the member variables *before* the constructor body executes.

```cpp
#include <iostream>

class Joint {
public:
    int pin;
    int currentAngle;

    // Parameterized Constructor using Member Initializer List
    Joint(int p, int startAngle) : pin(p), currentAngle(startAngle) {
        // Constructor body can be empty, or used for startup print messages
        std::cout << "Joint initialized via initializer list." << std::endl;
    }
};
```
### Why use Member Initializer Lists?
1. **Performance:** For simple integers, the speed difference is tiny. But for complex objects, using assignment `=` forces C++ to first call the default constructor for the member, and then copy the new value over it. An initializer list initializes the member directly, saving CPU cycles.
2. **Const Members:** If your class has a `const` member variable (such as the pin number, which should never change), it **must** be initialized in the member initializer list. You cannot assign a value to a `const` variable inside the constructor body.

---

## Connection to the BraccioV2 Library

In `BraccioV2.h`, the constructor is declared:
```cpp
class Braccio {
  public:
    Braccio();
    // ...
};
```
And in `BraccioV2.cpp`:
```cpp
Braccio::Braccio() {
}
```
Here, the constructor body is empty because the class initializes its arrays directly inside the class definition (e.g. `int _jointCenter[7] = {90, 90, 90, 90, 90, 50};`). This is a modern C++ feature called **In-Class Member Initializers**, which is another clean way to set default states.

---

## Practice Exercises

### Exercise 1: Constructor with Constants
Create a class called `Sensor` with:
* A public `const int id` (which represents a permanent sensor address).
* A public variable `float currentReading`.
* A parameterized constructor that sets the `id` and sets `currentReading` to `0.0`. Since `id` is a constant, you *must* use a member initializer list!

Verify this works by instantiating a sensor with ID `1` in `main()` and printing its ID.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

class Sensor {
public:
    const int id;
    float currentReading;

    // Member initializer list is required for const member 'id'
    Sensor(int sensorID) : id(sensorID), currentReading(0.0f) {}
};

int main() {
    Sensor tempSensor(1);
    std::cout << "Sensor ID: " << tempSensor.id << std::endl;
    std::cout << "Initial reading: " << tempSensor.currentReading << std::endl;
    return 0;
}
```
</details>

### Exercise 2: Constructor Overloading
Write a class named `Actuator` that has two constructors:
1. A default constructor that sets `speed` to `1` and `position` to `0`.
2. A parameterized constructor that allows the user to specify custom values for both `speed` and `position`.

Test both constructors in `main()`.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

class Actuator {
public:
    int speed;
    int position;

    // 1. Default constructor
    Actuator() : speed(1), position(0) {}

    // 2. Parameterized constructor
    Actuator(int s, int p) : speed(s), position(p) {}
};

int main() {
    Actuator basicActuator; // Calls default constructor
    Actuator fastActuator(5, 45); // Calls parameterized constructor

    std::cout << "Basic Actuator - Speed: " << basicActuator.speed << ", Pos: " << basicActuator.position << std::endl;
    std::cout << "Fast Actuator  - Speed: " << fastActuator.speed  << ", Pos: " << fastActuator.position  << std::endl;

    return 0;
}
```
</details>

---

[Previous: Lesson 08](lesson08_classes.md) | [Next: Lesson 10](lesson10_encapsulation.md)
