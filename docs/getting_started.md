# Getting Started

## Learning Objectives

- Install the documentation tools
- Preview the site locally
- Understand the repository layout
- Prepare the project for GitHub publishing

## Prerequisites

- Python 3
- pip
- A text editor such as VS Code

## Step 1: Create the project structure

The course project should look like this:

```text
BraccioV2-Cpp-Masterclass/
├── docs/
├── images/
├── .github/workflows/
├── mkdocs.yml
├── requirements.txt
└── README.md
```

## Step 2: Install dependencies

Run the following command in the project root:

```bash
python3 -m pip install -r requirements.txt
```

## Step 3: Start the local preview

```bash
mkdocs serve
```

Then open the local URL shown in the terminal.

## Step 4: Edit content

Most of your writing will happen in the docs folder. When you save a Markdown file, the preview updates automatically.

## Step 5: Publish to GitHub

After pushing the repository to GitHub, enable GitHub Pages and use the included workflow so the documentation is published as a clean website.

## Summary

This setup gives you a professional documentation site that can be viewed locally and published online with minimal effort.

---

[Previous: Introduction](introduction.md) | [Next: Lesson 01](Part1_Cpp_Basics/lesson01_compiler.md)
