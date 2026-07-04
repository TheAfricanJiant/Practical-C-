# Lesson 17: Anatomy of an Arduino Library

To share C++ code between multiple Arduino projects, or to publish code for others to use, you must package it into a structured format called an **Arduino Library**. The modern Arduino IDE (version 1.5+) follows a strict directory layout to recognize, compile, and index libraries.

In this lesson, we will inspect the standard folder structure, metadata formatting, and syntax highlighting configuration of a professional Arduino library.

---

## The Standard Directory Layout

An Arduino library is contained in a single root folder. The files must be organized as follows:

```text
MyBraccio/
├── src/
│   ├── MyBraccio.h
│   └── MyBraccio.cpp
├── examples/
│   └── SweepJoint/
│       └── SweepJoint.ino
├── library.properties
└── keywords.txt
```

Let's detail the role of each directory and file:

### 1. The `src/` Folder
Contains all the C++ source files and headers. Putting your files inside `src/` is the standard since Arduino IDE 1.5. This keeps the root directory clean and allows the compiler to search for nested directories.

### 2. The `examples/` Folder
Contains example sketches demonstrating how to use the library. 
* Every example must reside in its own subdirectory matching the name of the sketch (e.g. `examples/SweepJoint/SweepJoint.ino`).
* Once installed, these examples appear in the Arduino IDE menu under **File → Examples → MyBraccio**.

---

## The Metadata: `library.properties`

The `library.properties` file is a plain-text configuration file containing metadata about your library. The Arduino IDE uses this file to populate the Library Manager interface and search results.

Here is the standard content of a `library.properties` file:

```ini
name=MyBraccio
version=1.0.0
author=Your Name <your.email@example.com>
maintainer=Your Name <your.email@example.com>
sentence=A custom object-oriented library for the Braccio V2 robotic arm.
paragraph=This library allows you to control 6 joints, configure limits, and execute smooth movements.
category=Device Control
architectures=*
depends=Servo
```

### Key properties explained:
* `name`: The name of the library (must be unique).
* `version`: Version number (e.g., `1.0.0`), using Semantic Versioning (`MAJOR.MINOR.PATCH`).
* `category`: The category in Library Manager (e.g., `Device Control`, `Sensors`, `Display`).
* `architectures`: Set to `*` to allow compilation on all boards, or specify specific architectures (like `avr`, `samd`).
* `depends`: A comma-separated list of other libraries that this library requires (e.g., `Servo`). The Arduino IDE will automatically prompt the user to install these dependencies.

---

## Syntax Highlighting: `keywords.txt`

To make your class names and member functions stand out in the Arduino IDE editor, you can provide a `keywords.txt` file. This file tells the IDE's editor which words to color-code.

The format uses strict **tab characters** (do not use spaces!) to separate the keyword from its classification:

```text
# Class Names (Colored orange/red - KEYWORD1)
MyBraccio	KEYWORD1

# Function and Method Names (Colored brown/blue - KEYWORD2)
begin	KEYWORD2
setAllAbsolute	KEYWORD2
setJointMax	KEYWORD2
update	KEYWORD2

# Constants (Colored blue/purple - LITERAL1)
BASE_ROT	LITERAL1
SHOULDER	LITERAL1
```

> [!WARNING]
> The separator between the keyword and its type (e.g. `MyBraccio` and `KEYWORD1`) **must be a single tab character (`\t`)**. If you use spaces, the Arduino IDE will ignore the line, and your keywords will not be highlighted.

---

## Practice Exercises

### Exercise 1: Write a `library.properties` File
Write a `library.properties` file for a hypothetical library named `UltraRange` (version `1.2.0`) designed to interface with ultrasonic distance sensors. Make it depend on no other libraries and specify that it works on all architectures.

<details>
<summary><b>View Solution</b></summary>

```ini
name=UltraRange
version=1.2.0
author=Jane Doe <jane.doe@example.com>
maintainer=Jane Doe <jane.doe@example.com>
sentence=A high-performance library for ultrasonic rangefinders.
paragraph=Provides non-blocking distance readings, filtering algorithms, and temperature compensation.
category=Sensors
architectures=*
```
</details>

### Exercise 2: Identify the Syntax Highlighting Bug
Why is the keyword `setAngle` not highlighted in the Arduino IDE in the following `keywords.txt` snippet?
```text
# Keywords file
MyJoint    KEYWORD1
setAngle    KEYWORD2
```

<details>
<summary><b>View Solution</b></summary>
The separator between `MyJoint` and `KEYWORD1`, as well as `setAngle` and `KEYWORD2`, consists of spaces rather than a tab character. 

The Arduino IDE parser expects a literal **tab character (`\t`)** as the delimiter. 

**The Fix:** Replace the spaces with a single tab character:
```text
MyJoint	KEYWORD1
setAngle	KEYWORD2
```
</details>

---

[Previous: Lesson 16](lesson16_arduino_ecosystem.md) | [Next: Lesson 18](lesson18_bracciov2_deep_dive.md)
