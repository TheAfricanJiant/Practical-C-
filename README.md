# Practical C++: BraccioV2 Masterclass

A polished documentation course for learning modern C++ through the BraccioV2 Arduino library and robotics examples. Learn from basic to expert mode, practical C++ concepts, build your own custom library, and control the robotic arm in the Arduino IDE.

---

## 🚀 What is Included

- **20 Structured Lessons:** Ranging from compilation pipeline basics to advanced OOP and memory management.
- **Hands-on Projects:** A C++ console simulation of the multi-joint arm and a custom Arduino library (`MyBraccio`).
- **Interactive Exercises:** Challenge problems with full solution sets for every part of the curriculum.
- **Syntax Cheatsheet:** Quick reference sheets for variables, pointers, OOP, and Arduino APIs.
- **Modern Web App/Docs:** Material theme docs with search capability.
- **GitHub Pages Workflow:** Automated deployment of the site.

---

## 💻 Local Preview

To run and preview the course documentation locally on your machine:

1. Install dependencies:
   ```bash
   python3 -m pip install -r requirements.txt
   ```
2. Start the dev server:
   ```bash
   mkdocs serve
   ```
3. Open `http://127.0.0.1:8000/` in your browser.

---

## 🌐 Publish to GitHub Pages

This project is configured with a GitHub Actions workflow to deploy the site to GitHub Pages automatically.

1. Push this repository to your GitHub account: `https://github.com/TheAfricanJiant/Practical-C-`
2. Under **Settings → Pages** in your GitHub repository interface, select **GitHub Actions** as the source build.
3. The workflow in `.github/workflows/gh-pages.yml` will automatically build and publish the site when you push commits to `main`.
