# Lesson 11: Const Methods & Member Functions

Class behaviors are implemented using member functions, which are often called **methods**. In C++, methods have a special relationship with the object that invokes them: they have access to all the member variables (both public and private) and are passed a hidden pointer to the calling object.

In this lesson, we will cover how methods operate under the hood, how to use the implicit `this` pointer, and how to write **const member functions** to enforce compile-time safety.

---

## The Implicit `this` Pointer

When you call a member function on an object, how does the function know *which* object's variables to modify?

```cpp
baseServo.rotateTo(90);
elbowServo.rotateTo(45);
```
Under the hood, whenever a member function is called, the compiler automatically passes a hidden argument: a pointer to the invoking object. This pointer is called **`this`**.

Inside any member function, `this` is a pointer to the current object. You can use it to resolve naming conflicts when your function parameters have the exact same name as your class member variables.

```cpp
#include <iostream>

class Joint {
private:
    int angle; // Member variable

public:
    // Constructor
    Joint() : angle(90) {}

    // Parameter is named 'angle'. 
    // We use 'this->angle' to refer to the member variable, 
    // and 'angle' to refer to the parameter.
    void updateAngle(int angle) {
        this->angle = angle; 
    }

    void printAddress() {
        // 'this' stores the physical memory address of the object
        std::cout << "Object address in memory: " << this << std::endl;
    }
};

int main() {
    Joint base;
    Joint elbow;

    base.printAddress();
    elbow.printAddress(); // Will print two different memory addresses!

    return 0;
}
```

---

## Const Member Functions (Const Correctness)

In C++, you should mark a member function with the `const` keyword if it does not modify any of the object's member variables. These are called **const member functions**.

```cpp
class Joint {
private:
    int angle;

public:
    Joint() : angle(90) {}

    // Adding 'const' at the end guarantees this method is read-only.
    // The compiler will reject any attempt to modify 'angle' inside this function.
    int getAngle() const {
        // angle = 120; // ERROR: assignment of member in read-only object!
        return angle;
    }
};
```

### Why is `const` critical in C++?
1. **Expressive Design:** It documents your intent, telling other developers that calling this method is safe and has no side effects.
2. **Compiler Optimization:** It allows the compiler to make optimizations knowing the state won't change.
3. **Const References:** If you pass a large object to a function by `const reference` (which is standard practice in C++ to save memory, as we will learn in Part 3), that function **can only call const member functions** on the object. If a getter is not marked `const`, the compiler will throw an error.

---

## Connection to the BraccioV2 Library

In `BraccioV2.h`, getters and helper methods use `const` qualifiers to guarantee read-only behavior. 
For example:
```cpp
class Braccio {
  public:
    // ...
    int getCenter(int joint) const; // If we wanted to write a const getter
};
```
Maintaining "const correctness" is a standard practice in C++ library development. It makes libraries stable, predictable, and compliant with standard C++ design guidelines.

---

## Practice Exercises

### Exercise 1: Const-Correct Robot Arm
Analyze the following class. Identify which methods **must** be marked `const` to maintain strict C++ const correctness, and write the corrected class declaration.

```cpp
class RobotArm {
private:
    int batteryLevel = 100;
    bool moving = false;

public:
    int getBattery() {
        return batteryLevel;
    }

    bool isMoving() {
        return moving;
    }

    void setMoving(bool state) {
        moving = state;
    }
};
```

<details>
<summary><b>View Solution</b></summary>

The methods `getBattery()` and `isMoving()` only read member variables; they do not modify any state. Therefore, they should be marked `const`. 

`setMoving()` modifies the member variable `moving`, so it cannot be `const`.

**Corrected Class:**
```cpp
class RobotArm {
private:
    int batteryLevel = 100;
    bool moving = false;

public:
    // Mark as const
    int getBattery() const {
        return batteryLevel;
    }

    // Mark as const
    bool isMoving() const {
        return moving;
    }

    // Must NOT be const
    void setMoving(bool state) {
        moving = state;
    }
};
```
</details>

### Exercise 2: Using `this` for Method Chaining
The `this` pointer can be dereferenced (`*this`) and returned from a method by reference to enable "method chaining". Method chaining allows you to write code like `joint.setAngle(45).printStatus();` in a single line.

Write a class named `ChainedJoint` with:
* A private member variable `int angle`.
* A setter `ChainedJoint& setAngle(int angle)` that updates the private variable and returns `*this` (a reference to the object itself).
* A method `ChainedJoint& printStatus()` that prints the angle and returns `*this`.

Test this chaining inside `main()`.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

class ChainedJoint {
private:
    int angle;

public:
    ChainedJoint() : angle(90) {}

    // Setter returns a reference to the class itself
    ChainedJoint& setAngle(int angle) {
        this->angle = angle;
        return *this; // Return this object
    }

    ChainedJoint& printStatus() {
        std::cout << "Current Angle: " << this->angle << " degrees" << std::endl;
        return *this; // Return this object
    }
};

int main() {
    ChainedJoint joint;

    // Chaining methods together!
    joint.setAngle(45).printStatus().setAngle(120).printStatus();

    return 0;
}
```
</details>

---

[Previous: Lesson 10](lesson10_encapsulation.md) | [Next: Lesson 12](../Part3_Memory/lesson12_pointers.md)
