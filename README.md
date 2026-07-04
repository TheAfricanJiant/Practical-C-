# 🤖 Practical C++ — BraccioV2 Masterclass

> **Learn modern C++ from scratch and build your own Arduino robot arm library.**  
> Every lesson links directly to a readable Markdown file — no tools required to follow along on GitHub.

[![Documentation](https://img.shields.io/badge/docs-GitHub%20Pages-blue?logo=github)](https://theafricanjiant.github.io/Practical-C-/)

## 🙌 Community Edition & Attribution

This project is being developed as a community-driven learning effort by **Tab Precious**, a core member of the **Hardware Innovation Valley Community (HWIVC)** in Buea, Cameroon.  

- **Author:** Tab Precious  
- **LinkedIn:** [Tabu Precious](https://cm.linkedin.com/in/tambu-precious-29bb67217)  
- **Community:** [Hardware Innovation Valley Community (HWIVC)](https://hwivc.org/)  
- **Community role:** Core team member contributing to hands-on robotics and embedded systems learning in Buea

This work also gives proper reference and credit to the original **BraccioV2** library authored by **Lukas Severinghaus**, and to the earlier **Braccio** library by **Andrea Martino** and **Angelo Ferrante**. The content here is shared as part of a wider community endeavour to support practical hardware education and innovation in Cameroon.
[![License](https://img.shields.io/github/license/TheAfricanJiant/Practical-C-)](LICENSE)
[![Lessons](https://img.shields.io/badge/lessons-20-brightgreen)](#-curriculum)

---

## 📖 What Is This?

This is a **structured, 20-lesson C++ curriculum** that teaches the language through the lens of robotics — specifically by building up to and then recreating the **BraccioV2 robotic arm Arduino library** from scratch.

By the end, you will have:
- A solid grip on modern C++ (types, OOP, memory, the preprocessor)
- A custom Arduino library (`MyBraccio`) you wrote yourself
- A working console simulation of a 6-joint robotic arm
- The skills to read and understand any real-world C++ embedded library

**You can read every lesson right here on GitHub** — just click the links below. Each file is a self-contained Markdown document with code examples, explanations, and exercises with full solutions.

---

## 🦾 The Hardware

The Braccio V2 is a 6-joint servo-driven robotic arm for Arduino.  
Throughout the course we use its library source code as a living, real-world C++ case study.

![Braccio Robotic Arm](docs/images/imagesB.jpeg)

---

## 📚 Curriculum

### Preface

| # | Title | Description |
|---|-------|-------------|
| — | [Introduction](docs/introduction.md) | Why C++ is the standard for robotics |
| — | [Course Roadmap](docs/roadmap.md) | Milestones and learning path overview |
| — | [Getting Started](docs/getting_started.md) | Environment setup and local preview |

---

### 📘 Part 1 — C++ Basics & Compilation  *(Lessons 1–7)*

Understand how code becomes machine instructions, and master the core syntax every embedded developer needs.

| # | Title | Key Concepts |
|---|-------|--------------|
| 01 | [The C++ Toolchain & Compilation](docs/Part1_Cpp_Basics/lesson01_compiler.md) | Preprocessor → Compiler → Assembler → Linker, `g++` flags |
| 02 | [Variables, Types & Constants](docs/Part1_Cpp_Basics/lesson02_variables.md) | Fixed-width types (`uint8_t`), `const`, variable scope |
| 03 | [Control Flow & Decision Making](docs/Part1_Cpp_Basics/lesson03_control_flow.md) | `if/else`, `switch`, `for`, `while`, logical operators |
| 04 | [Functions & Scope](docs/Part1_Cpp_Basics/lesson04_functions.md) | Return types, parameters, prototypes, pass-by-value |
| 05 | [Header Files vs Source Files](docs/Part1_Cpp_Basics/lesson05_headers.md) | `.h` vs `.cpp`, separate compilation, `g++` multi-file builds |
| 06 | [Include Guards & `#pragma once`](docs/Part1_Cpp_Basics/lesson06_include_guards.md) | Double-inclusion problem, `#ifndef`/`#define`/`#endif` |
| 07 | [The Preprocessor & Macros](docs/Part1_Cpp_Basics/lesson07_preprocessor.md) | `#define`, function-like macros, conditional compilation |

---

### 📙 Part 2 — Object-Oriented Programming  *(Lessons 8–11)*

Model physical robot joints as C++ classes. Learn how the BraccioV2 library is architected around OOP principles.

| # | Title | Key Concepts |
|---|-------|--------------|
| 08 | [Classes & Objects](docs/Part2_OOP/lesson08_classes.md) | `class`, `public`/`private`, member variables & methods |
| 09 | [Constructors & Member Initializers](docs/Part2_OOP/lesson09_constructors.md) | Default & parameterized constructors, member initializer lists |
| 10 | [Encapsulation & Bounds Checking](docs/Part2_OOP/lesson10_encapsulation.md) | Getters/setters, data hiding, safety clamping |
| 11 | [Member Functions & Const Methods](docs/Part2_OOP/lesson11_methods.md) | The `this` pointer, `const`-qualified methods, method chaining |

---

### 📗 Part 3 — Pointers, References & Memory  *(Lessons 12–15)*

Demystify how C++ actually manages memory — a critical skill for writing safe embedded firmware.

| # | Title | Key Concepts |
|---|-------|--------------|
| 12 | [Understanding Pointers & Addresses](docs/Part3_Memory/lesson12_pointers.md) | `*`, `&`, `->`, `nullptr`, dereferencing |
| 13 | [References & Pass-by-Reference](docs/Part3_Memory/lesson13_references.md) | Reference aliases, `const&`, avoiding copies |
| 14 | [Stack vs Heap & Memory Safety](docs/Part3_Memory/lesson14_dynamic_memory.md) | `new`/`delete`, memory leaks, heap fragmentation on Arduino |
| 15 | [C-style Arrays & Joint State](docs/Part3_Memory/lesson15_arrays.md) | Contiguous memory, array decay, bounds overflow |

---

### 📕 Part 4 — Building a Custom Arduino Library  *(Lessons 16–20)*

Put everything together. Read real library source code, then write your own `MyBraccio` library from scratch and deploy it to hardware.

| # | Title | Key Concepts |
|---|-------|--------------|
| 16 | [The Arduino Build System & C++](docs/Part4_Arduino_Libraries/lesson16_arduino_ecosystem.md) | `.ino` translation, hidden `main()`, `Servo` library |
| 17 | [Anatomy of an Arduino Library](docs/Part4_Arduino_Libraries/lesson17_library_structure.md) | `src/`, `examples/`, `library.properties`, `keywords.txt` |
| 18 | [Dissecting the BraccioV2 Library](docs/Part4_Arduino_Libraries/lesson18_bracciov2_deep_dive.md) | Soft-start PWM, non-blocking scheduler, `safeDelay()` |
| 19 | [Coding Your `MyBraccio` Library](docs/Part4_Arduino_Libraries/lesson19_coding_mybraccio.md) | Full `MyBraccio.h` + `MyBraccio.cpp` implementation |
| 20 | [Deploying, Testing & Troubleshooting](docs/Part4_Arduino_Libraries/lesson20_deployment.md) | Arduino IDE import, `.ZIP` packaging, build errors |

---

### 🔬 Projects & Exercises

| Resource | Description |
|----------|-------------|
| [Project 01: Multi-Joint Robot Simulator](docs/Projects/project01_robot.md) | Build a full C++ console simulator of the 6-joint arm using OOP |
| [Practice Workbook & Solutions](docs/Exercises/exercises_solutions.md) | Exercises for all 4 parts with complete, explained solutions |
| [C++ & Arduino Syntax Cheatsheet](docs/Appendix/cheatsheet.md) | Quick-reference for types, OOP, pointers, and Arduino APIs |

---

## 🚀 Two Ways to Follow This Course

### Option A — Read on GitHub *(no setup needed)*
Click any lesson link in the table above. GitHub renders Markdown natively — code blocks are syntax-highlighted, links between lessons work, and exercises have collapsible solution blocks. Just click **▶ View Solution** to expand them.

### Option B — Run the Full Documentation Site Locally
For the best experience (search, dark mode, navigation sidebar):

```bash
# 1. Clone the repo
git clone https://github.com/TheAfricanJiant/Practical-C-.git
cd Practical-C-

# 2. Install dependencies
python3 -m pip install -r requirements.txt

# 3. Start the local server
mkdocs serve
```

Then open **http://127.0.0.1:8000/** in your browser.

---

## 🌐 Live Site

The full course is also deployed to GitHub Pages:  
**→ https://theafricanjiant.github.io/Practical-C-/**

The site is automatically rebuilt and republished every time a commit is pushed to `main` via the workflow in [`.github/workflows/gh-pages.yml`](.github/workflows/gh-pages.yml).

---

## 📁 Repository Structure

```
Practical-C-/
│
├── docs/                          # All lesson content (Markdown)
│   ├── Part1_Cpp_Basics/          # Lessons 01–07
│   ├── Part2_OOP/                 # Lessons 08–11
│   ├── Part3_Memory/              # Lessons 12–15
│   ├── Part4_Arduino_Libraries/   # Lessons 16–20
│   ├── Projects/                  # Capstone projects
│   ├── Exercises/                 # Practice workbook + solutions
│   ├── Appendix/                  # Cheatsheets
│   └── images/                    # Robotic arm photos
│
├── BraccioV2.h                    # Original library header (case study)
├── BraccioV2.cpp                  # Original library source (case study)
│
├── .github/workflows/gh-pages.yml # Auto-deploy to GitHub Pages
├── mkdocs.yml                     # Documentation site config
├── requirements.txt               # Python dependencies
└── README.md                      # ← You are here
```

---

## 🤝 Contributing

Found a typo, a broken link, or want to add a lesson?  
Open an [Issue](https://github.com/TheAfricanJiant/Practical-C-/issues) or submit a Pull Request — contributions are welcome!

---

*Built with [MkDocs](https://www.mkdocs.org/) + [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)*
