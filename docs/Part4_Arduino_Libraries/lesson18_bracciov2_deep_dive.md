# Lesson 18: Dissecting the BraccioV2 Library Code

Now that you have mastered the C++ foundations and understand how Arduino libraries are structured, it is time to perform a **code review** of the actual `BraccioV2` codebase that you examined earlier. 

The library contains several sophisticated mechanisms designed to solve hardware challenges, specifically:
1. **Power Management (Soft-Start)**
2. **Coordinated Motion (Non-Blocking Scheduling)**

---

## 1. Power Management: Soft-Start

When multiple servo motors are powered on at the same time, they draw a massive spike of electrical current (known as "inrush current"). On an Arduino, this current spike can pull the system voltage down, causing the microcontroller to reset or brownout.

To prevent this, the Braccio V4 (and greater) Shield includes a power control transistor connected to digital **pin 12**. 

In `BraccioV2.cpp`, the initialization routine configures this pin and runs a soft-start sequence:

```cpp
void Braccio::_initializeServos(bool defaultPos) {
  pinMode(SOFT_START_PIN, OUTPUT);
  digitalWrite(SOFT_START_PIN, LOW); // Turn off power completely first
  
  // Attach servos...
  
  _softStart(); // Ramps up power gradually
}
```

The soft-start sequence uses a software-generated PWM signal to gradually turn on the power transistor over 6 seconds:

```cpp
void Braccio::_softStart() {
  long int tmp = millis();
  
  // Phase 1: 0 to 2 seconds. Pulse pin 12 with a short high time.
  while (millis() - tmp < 2000)
    _softwarePWM(80, 450);   // High for 80us, Low for 450us (Duty Cycle: ~15%)

  // Phase 2: 2 to 6 seconds. Pulse pin 12 with a slightly longer high time.
  while (millis() - tmp < 6000)
    _softwarePWM(75, 430); // High for 75us, Low for 430us (Duty Cycle: ~15%)

  // Phase 3: Fully ON. Enable continuous 5V power.
  digitalWrite(SOFT_START_PIN, HIGH);
}
```
By slowly pulsing the power supply gate, the servos are powered on "softly", preventing the robotic arm from jerking violently or resetting the Arduino board.

---

## 2. Coordinated Motion: `update()` and `safeDelay()`

If you write a standard Arduino sketch and use the blocking `delay(1000)` function to pause between movements, all program execution stops. The microcontroller freezes, and it cannot update servo positions or read sensors.

The BraccioV2 library solves this by using a non-blocking movement scheduler.

### A. The Target/Current Position model
The class maintains two arrays of sizes 7:
* `_currentJointPositions`: Where the joint is *right now*.
* `_targetJointPositions`: Where the joint *wants to be*.

Whenever the user calls `setAllAbsolute(b, s, e, w, w_r, g)` or `setOneAbsolute(joint, value)`, the library **does not move the servos immediately**. It only updates the `_targetJointPositions` array.

### B. The `update()` method
To actually move the servos, the developer must call `update()` repeatedly (usually inside `loop()` or `safeDelay()`). The `update()` method calls `_moveServo(joint)` for every joint.

```cpp
void Braccio::_moveServo(int joint) {
  int currentPos = _currentJointPositions[joint];
  int targetPos = _targetJointPositions[joint];
  
  if (currentPos != targetPos) {
    // Determine direction (+1 degree or -1 degree)
    int dir = (currentPos <= targetPos) ? 1 : -1;
    int delta = _jointDelta[joint]; // Step speed (default 1)
    int newPos = currentPos + (dir * delta);
    
    _setServo(joint, newPos, false); // Move the physical servo to new intermediate position
  }
}
```
If a joint is currently at `90` and its target is `120`, calling `update()` once moves it to `91`. The next call moves it to `92`, and so on, until it reaches `120`. This ensures that all 6 joints move **simultaneously and smoothly**, rather than one joint moving fully before the next starts.

### C. The `safeDelay()` method
Since developers love using delays, the library provides a non-blocking alternative: `safeDelay(int ms)`.

```cpp
void Braccio::safeDelay(int ms, int t){
  long currentTime = millis();
  while(millis() < currentTime + ms){
    update(); // Continually update servo positions during the delay period!
    delay(t); // Yield execution for t milliseconds (default 10ms)
  }
}
```
Instead of freezing the microcontroller, `safeDelay()` loops, constantly calling `update()` every `t` milliseconds to keep the arm moving smoothly while the sketch is waiting.

---

## Practice Exercises

### Exercise 1: Calculate Soft-Start Duty Cycle
In Phase 1 of `_softStart()`, the library calls `_softwarePWM(80, 450)`. The high time is 80 microseconds, and the low time is 450 microseconds. 
1. What is the total period of one PWM pulse?
2. What is the duty cycle percentage (the ratio of high time to total period)?

<details>
<summary><b>View Solution</b></summary>

1. **Total Period:**
   $$\text{Period} = \text{High Time} + \text{Low Time} = 80\,\mu\text{s} + 450\,\mu\text{s} = 530\,\mu\text{s}$$

2. **Duty Cycle:**
   $$\text{Duty Cycle} = \frac{\text{High Time}}{\text{Period}} \times 100\% = \frac{80}{530} \times 100\% \approx 15.09\%$$
</details>

### Exercise 2: Explain Blocking vs Non-Blocking
Suppose you write an Arduino loop that moves the base joint from 90 to 180 and the elbow joint from 90 to 0. 
* If you call `myServoBase.write(180)` followed by a delay, then `myServoElbow.write(0)`, what does the physical movement look like?
* If you use the BraccioV2 `setAllAbsolute(180, 90, 0, 90, 90, 50)` followed by `safeDelay(2000)`, what does the movement look like?

<details>
<summary><b>View Solution</b></summary>

* **Standard blocking approach:** The base joint will rotate to 180 first. Only after the delay completes will the elbow joint begin to rotate to 0. The motion is sequential and looks robotic and disjointed.
* **BraccioV2 `safeDelay` approach:** Both joints will start rotating at the same time. The base joint will step forward by 1 degree, and the elbow joint will step backward by 1 degree on each call to `update()`. The two joints move concurrently, resulting in a smooth, coordinated, and organic robotic movement.
</details>

---

[Previous: Lesson 17](lesson17_library_structure.md) | [Next: Lesson 19](lesson19_coding_mybraccio.md)
