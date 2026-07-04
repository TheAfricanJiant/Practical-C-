# Lesson 16: The Arduino Build System & C++

Many beginners believe that Arduino has its own unique programming language. In reality, **Arduino sketches are written in standard C++**. The Arduino IDE simply acts as a wrapper that simplifies the compilation pipeline and hides the low-level boilerplate code.

In this lesson, we will uncover what happens behind the scenes of the Arduino build system, explore the main lifecycle, and look at the standard `Servo` library.

---

## What the Arduino IDE Hides

When you write a standard C++ program for your computer, you must include headers, declare prototypes, and write a `main()` function:
```cpp
#include <iostream>
int main() {
    std::cout << "Hello World" << std::endl;
    return 0;
}
```

In Arduino, you write a `.ino` sketch with just `setup()` and `loop()`:
```cpp
void setup() {
    pinMode(13, OUTPUT);
}
void loop() {
    digitalWrite(13, HIGH);
    delay(1000);
}
```

When you click the **Verify/Upload** button, the Arduino build system preprocesses your sketch:
1. **Merges Files:** If your sketch folder has multiple `.ino` files, it merges them into a single file.
2. **Injects Headers:** It adds `#include <Arduino.h>` at the very top. This header contains declarations for core Arduino functions (`pinMode`, `digitalWrite`, `delay`).
3. **Generates Prototypes:** It scans your code and generates function prototypes for all functions you defined so you don't get compilation errors.
4. **Appends `main()`:** It appends a standard C++ `main()` function that calls `setup()` once, and then runs `loop()` in an infinite loop.

Here is the conceptual `main()` function hidden inside the Arduino core library:

```cpp
#include <Arduino.h>

int main(void) {
    init(); // Initialize microcontroller registers, timers, and PWM
    setup(); // Runs your setup code once
    
    // The infinite execution loop
    while (true) {
        loop(); // Runs your loop code repeatedly
        if (serialEventRun) serialEventRun(); // Processes incoming serial communication
    }
    return 0;
}
```

---

## The Standard `Servo` Library

To drive physical servo motors, Arduino provides a pre-installed library called `Servo`. Since this is a C++ library, it exposes a `Servo` class.

Here is how you use it in a sketch:

```cpp
#include <Servo.h>

Servo myServo; // Instantiates an object of the Servo class

void setup() {
    // Attach the servo object to physical digital pin 9
    myServo.attach(9); 
}

void loop() {
    // Write an absolute angle (0 to 180 degrees) to the servo
    myServo.write(90);  // Center position
    delay(1000);
    
    myServo.write(45);  // Rotate to 45
    delay(1000);
}
```

### Key Methods of the `Servo` Class:
* `attach(int pin)`: Specifies which microcontroller pin is connected to the servo's signal wire (PWM).
* `write(int angle)`: Sends a Pulse Width Modulation (PWM) signal to rotate the servo to the specified angle (clamped automatically between 0 and 180).
* `read()`: Returns the last written angle.
* `attached()`: Returns `true` if the servo is currently attached to a pin.

---

## Connection to the BraccioV2 Library

The BraccioV2 library is built directly on top of the `Servo` library. 

In `BraccioV2.h`, the class declaration contains six private `Servo` member variables:
```cpp
class Braccio {
  private:
    Servo _base;
    Servo _shoulder;
    Servo _elbow;
    Servo _wrist_rot;
    Servo _wrist;
    Servo _gripper;
};
```
When `Braccio::begin()` is called, it attaches these internal objects to their respective physical pins:
```cpp
void Braccio::_initializeServos(bool defaultPos) {
  _base.attach(_BASE_ROT_PIN);
  _shoulder.attach(_SHOULDER_PIN);
  // ...
}
```
This is an object-oriented design pattern called **Composition**: one class (`Braccio`) is composed of other objects (`Servo`).

---

## Practice Exercises

### Exercise 1: Reconstruct the C++ equivalent
Write a fully complete, standard C++ CLI equivalent of an Arduino sketch. 
* Replace `Serial.println("Hello")` with `std::cout << "Hello\n"`.
* Emulate the Arduino startup and infinite loop.
* Keep the loop running but add a counter so it stops after 5 iterations.

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <iostream>

// Emulate setup
void setup() {
    std::cout << "--- Arduino CLI Simulator ---\n";
    std::cout << "setup() completed.\n";
}

// Emulate loop
void loop() {
    static int counter = 0;
    std::cout << "loop() iteration: " << ++counter << "\n";
    
    if (counter >= 5) {
        std::cout << "Stopping simulation.\n";
        exit(0); // Exit the program
    }
}

// Hidden main wrapper
int main() {
    setup();
    while (true) {
        loop();
    }
    return 0;
}
```
</details>

### Exercise 2: Understanding Compilers
Which compiler does the Arduino IDE use to build sketches for an Arduino Uno? Can you compile an Arduino sketch directly using the standard `g++` command on your computer?

<details>
<summary><b>View Solution</b></summary>
The Arduino IDE uses **`avr-gcc`** (and `avr-g++`) to compile sketches for the AVR-based Arduino Uno.

You **cannot** compile an Arduino sketch using the standard desktop `g++` command directly. 
* Desktop `g++` is a native compiler that compiles code to run on your PC's x86 or ARM CPU.
* Microcontrollers run on different instruction sets (like AVR RISC architecture) and lack operating systems. They require a **cross-compiler** like `avr-g++` to generate the correct binary machine code, along with custom linker maps to target the specific physical RAM and Flash program memory layouts on the microcontroller chip.
</details>

---

[Previous: Part 3 - Lesson 15](../Part3_Memory/lesson15_arrays.md) | [Next: Lesson 17](lesson17_library_structure.md)
