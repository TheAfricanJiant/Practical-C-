# C++ & Arduino Syntax Cheatsheet

A quick reference guide for C++ syntax, Object-Oriented Programming, Memory Management, and Arduino APIs.

---

## 💻 C++ Core Syntax

### 1. Variables & Types
```cpp
#include <cstdint> // Required for fixed-width types

int speed = 90;           // Signed integer (usually 4 bytes)
float voltage = 5.0f;     // Single-precision decimal (4 bytes)
bool active = true;       // Boolean value (1 byte)
char label = 'A';         // Single ASCII character (1 byte)

// Fixed-width types (Recommended for Embedded Systems)
uint8_t angle = 120;      // Unsigned 8-bit int (0 to 255) - 1 byte
int16_t reading = -450;   // Signed 16-bit int (-32768 to 32767) - 2 bytes
uint32_t timer = 45000;   // Unsigned 32-bit int (0 to 4.2 billion) - 4 bytes
```

### 2. Constants
```cpp
const int SAFETY_LIMIT = 165; // Type-safe constant variable
#define LED_PIN 13            // Preprocessor text substitution (no type safety)
```

### 3. Conditionals & Decision Making
```cpp
// if-else
if (angle > 180) {
    angle = 180;
} else if (angle < 0) {
    angle = 0;
} else {
    // Within limits
}

// switch-case
switch (jointID) {
    case 0:  /* Base code */ break;
    case 1:  /* Shoulder code */ break;
    default: /* Fallback code */ break;
}
```

### 4. Loops
```cpp
// for loop (known counts)
for (int i = 0; i < 6; ++i) {
    jointPositions[i] = 90;
}

// while loop (runs until condition is false)
while (currentPosition != targetPosition) {
    currentPosition += step;
}
```

### 5. Functions & Prototypes
```cpp
// 1. Prototype (Declaration) - Place at top of file or in .h
int clampValue(int value, int minL, int maxL);

// 2. Definition - Place at bottom of file or in .cpp
int clampValue(int value, int minL, int maxL) {
    if (value < minL) return minL;
    if (value > maxL) return maxL;
    return value;
}
```

---

## 🛠️ Object-Oriented Programming (OOP)

### 1. Class Structure & Access Specifiers
```cpp
class ServoJoint {
private:
    int angle; // Accessible only inside this class

public:
    // Constructor using Member Initializer List
    ServoJoint(int initialAngle) : angle(initialAngle) {}

    // Getter (Const-Qualified)
    int getAngle() const { return angle; }

    // Setter (With safety clamping)
    void setAngle(int target) {
        if (target >= 0 && target <= 180) {
            angle = target;
        }
    }
};
```

---

## 🧠 Memory, Pointers & References

### 1. Pointer Basics
```cpp
int angle = 90;
int* ptr = &angle; // Store the memory address of angle in ptr

*ptr = 120;        // Dereference ptr to write 120 into angle
int val = *ptr;    // Dereference ptr to read angle value
```

### 2. References (Aliases)
```cpp
int angle = 90;
int& ref = angle;  // ref is an alias/nickname for angle

ref = 120;         // Modifies the original variable angle directly!
```

### 3. Function Parameter Passing
```cpp
void passByValue(int x);          // Copies variable (read-only, modifies local copy)
void passByReference(int& x);      // Modifies original variable directly (efficient)
void passByConstRef(const int& x); // Read-only, does not copy (highly efficient for large objects)
```

### 4. Arrays & Decay
```cpp
int joints[6] = {90, 90, 90, 90, 90, 50};

// Array index access
int baseAngle = joints[0];

// Array decay (passing array to function as pointer)
void printArray(const int* arr, int size);
```

---

## 🔌 Arduino Core APIs

### 1. Setup & Loop Lifecycle
```cpp
void setup() {
    // Runs once at power-on
}

void loop() {
    // Runs infinitely after setup
}
```

### 2. Digital I/O
```cpp
pinMode(13, OUTPUT);      // Configure pin 13 as output
digitalWrite(13, HIGH);   // Send 5V (digital HIGH) to pin 13
int state = digitalRead(2); // Read digital level (HIGH/LOW) on pin 2
```

### 3. Timing & Serial
```cpp
delay(1000);              // Freeze microcontroller for 1000 milliseconds
unsigned long t = millis(); // Returns milliseconds since power-on

Serial.begin(9600);       // Initialize serial monitoring at 9600 baud
Serial.println("Ready");  // Print message to serial console
```
