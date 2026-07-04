# Lesson 20: Deploying, Testing, and Troubleshooting

Congratulations! You have written your custom C++ library `MyBraccio`. The final step of our masterclass is learning how to **deploy** this library into the Arduino IDE, write a test sketch to drive the robotic arm, and troubleshoot common library import and build errors.

---

## Step 1: Locating Your Arduino Libraries Folder

To make the library discoverable by the Arduino IDE, you must place the library root folder (`MyBraccio/`) inside your system's Arduino libraries directory:

* **Linux:** `~/Arduino/libraries/`
* **Windows:** `C:\Users\<YourUsername>\Documents\Arduino\libraries\`
* **macOS:** `/Users/<YourUsername>/Documents/Arduino/libraries/`

Simply copy your `MyBraccio/` folder containing the `src/` directory, `library.properties`, `keywords.txt`, and `examples/` directory into that folder.

---

## Step 2: Creating the Example Sketch

Inside your library folder, navigate to `examples/` (create it if it doesn't exist) and create a subfolder named `BasicSweep`. Inside that folder, create a sketch file named `BasicSweep.ino`.

Write the following verification code inside `BasicSweep.ino`:

```cpp
#include <MyBraccio.h>

// Instantiate our custom robot controller
MyBraccio arm;

void setup() {
    // Start serial communications for debugging
    Serial.begin(9600);
    Serial.println("Initializing MyBraccio robotic arm...");
    
    // Attach servos and drive to default center positions
    arm.begin();
    
    Serial.println("Initialization complete! Starting sweep loop...");
}

void loop() {
    Serial.println("Sweeping Arm to Position A (Low Left)...");
    // Parameters: Base, Shoulder, Elbow, Wrist, WristRot, Gripper
    // Rotate base to 45, drop shoulder to 45, extend elbow to 90, gripper closed (10)
    arm.setAllAbsolute(45, 45, 90, 90, 90, 10);
    arm.safeDelay(3000); // Wait 3 seconds while updating joints
    
    Serial.println("Sweeping Arm to Position B (High Right)...");
    // Rotate base to 135, raise shoulder to 135, elbow to 90, gripper open (73)
    arm.setAllAbsolute(135, 135, 90, 90, 90, 73);
    arm.safeDelay(3000); // Wait 3 seconds while updating joints
}
```

---

## Step 3: Importing and Building in the Arduino IDE

1. **Launch the Arduino IDE.**
2. If the IDE was already open, restart it so the editor scans the libraries folder and detects the new files.
3. Open the example sketch: Go to **File → Examples → MyBraccio → BasicSweep**.
4. Select your board (e.g. Arduino Uno) under **Tools → Board**.
5. Click the **Verify** (checkmark) button to compile the code. The build output console should show a successful compilation.
6. Click the **Upload** (arrow) button to upload the program to your microcontroller.

---

## Troubleshooting Common Errors

### 1. Header not found error
* **Error:** `fatal error: MyBraccio.h: No such file or directory`
* **Causes:** 
  1. The `MyBraccio` folder was placed in the wrong directory.
  2. The name of the header file inside the `src/` folder is misspelled (e.g., `mybraccio.h` in lowercase on a case-sensitive Linux OS, whereas the sketch includes `MyBraccio.h`).
* **Fix:** Verify the path is correct and case matches exactly. Restart the IDE.

### 2. Undefined reference linker error
* **Error:** `error: undefined reference to 'MyBraccio::begin()'`
* **Cause:** The IDE found the header declaration, but did not compile or link the source `MyBraccio.cpp` file. This usually happens if you put `MyBraccio.cpp` in the root folder instead of the `src/` directory under standard Arduino 1.5 format.
* **Fix:** Ensure both `MyBraccio.h` and `MyBraccio.cpp` reside directly inside the `MyBraccio/src/` folder.

---

## Practice Exercises

### Exercise 1: Create a `.ZIP` Library Package
Sometimes you want to distribute your library as a single file. How do you prepare and import a `.zip` library package? List the steps.

<details>
<summary><b>View Solution</b></summary>

1. **Create the zip file:** Compress the `MyBraccio` folder directly. The resulting file `MyBraccio.zip` should have `library.properties` and the `src` folder immediately inside its root directory (not nested inside a double-folder).
2. **Import via Arduino IDE:**
   * Open the Arduino IDE.
   * Go to the top menu: **Sketch → Include Library → Add .ZIP Library...**
   * Select your `MyBraccio.zip` file and click Open.
3. **Verify:** The library will be extracted automatically into your libraries folder, and the IDE will make it immediately ready for use.
</details>

### Exercise 2: Code a Calibration Sketch
Write an Arduino sketch using the `MyBraccio` library that performs a startup sequence:
* In `setup()`, it boots the arm.
* In `loop()`, it sweeps ONLY the base joint between `0` and `180` degrees in steps of `30` degrees. It must print the current base angle to the Serial Monitor at each step. (Hint: Use `setOneAbsolute` and `safeDelay`).

<details>
<summary><b>View Solution</b></summary>

```cpp
#include <MyBraccio.h>

MyBraccio arm;

void setup() {
    Serial.begin(9600);
    arm.begin();
    Serial.println("Base Joint Calibration Routine Ready.");
}

void loop() {
    // Sweep base from 0 to 180 in increments of 30
    for (int angle = 0; angle <= 180; angle += 30) {
        Serial.print("Commanding Base to: ");
        Serial.print(angle);
        Serial.println(" degrees.");
        
        arm.setOneAbsolute(JOINT_BASE, angle);
        arm.safeDelay(1000); // Let the servo rotate for 1 second
    }
    
    Serial.println("Sweep completed. Pausing for 5 seconds...");
    arm.safeDelay(5000);
}
```
</details>

---

[Previous: Lesson 19](lesson19_coding_mybraccio.md) | [Next: Project 01](../Projects/project01_robot.md)
