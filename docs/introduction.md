# Introduction

Welcome to the intersection of software and hardware. 

Robotics represents one of the most demanding fields for software engineers. A robot must interact with the physical world in real time, meaning its code must be **fast, predictable, and highly resource-conscious**. At the same time, because robots are complex systems with many moving parts (sensors, actuators, communications, path planning), the codebase must be **modular, extensible, and clean**.

This is why **C++** is the undisputed industry standard for robotics—from hobbyist Arduino boards to NASA's Mars Rovers, autonomous vehicles, and industrial arm systems.

---

## Why C++ for Robotics?

C++ is a unique programming language because it offers two seemingly opposite benefits:

1. **High-Level Abstractions:** You can use Object-Oriented Programming (OOP), templates, and custom types to model complex real-world entities (like a robotic arm, a joint, or a inverse-kinematics solver).
2. **Low-Level Control:** You have direct access to memory addresses, pointers, hardware registers, and system interrupts. There is no heavy runtime environment or garbage collector interrupting your code to clean up memory, giving you deterministic timing.

---

## The Case Study: Braccio V2 Robotic Arm

In this course, we will use the **Braccio V2** library as our main reference. 

A library that controls a robotic arm needs to do several things:
* **Represent Physical Limits:** Prevent a servo from physically rotating past its mechanical limit, which could burn out the motor or damage the arm.
* **Coordinate Multiple Actuators:** Move six servos at the same time to achieve smooth, coordinated motion.
* **Manage Power Demands:** Ramping up motor power slowly (a process called "soft-start") to avoid drawing massive current spikes that could reset the microcontroller.
* **Expose a Simple API:** Allow a user to write `arm.setAllAbsolute(90, 90, 90, 90, 90, 73)` without needing to worry about the electrical signals sent to individual servo pins.

By analyzing and recreating this library, you will learn how professional developers write embedded code.

---

## Prerequisites

To get the most out of this course, you should have:
* Basic programming familiarity (loops, conditional logic, variables in any language).
* A computer capable of running a terminal and compiling C++ code.
* An interest in robotics and physical computing!
* *(Optional)* An Arduino board (such as the Arduino Uno) and a Braccio V2 robotic arm to run your code on physical hardware.

---

[Previous: Roadmap](roadmap.md) | [Next: Getting Started](getting_started.md)
