# BraccioV2 C++ Masterclass

A polished documentation course for learning modern C++ through the BraccioV2 Arduino library and robotics examples.

## What is included

- A full MkDocs + Material tutorial site with navigation and search
- Step-by-step lessons that explain C++ concepts in a practical robotics context
- A GitHub Pages workflow so the course can be published online
- A Doxygen-ready structure for future API reference for a custom MyBraccio library

## Local preview

```bash
python3 -m pip install -r requirements.txt
mkdocs serve
```

Then open http://127.0.0.1:8000/bracciov2-cpp-masterclass/.

## Publish to GitHub Pages

1. Create a GitHub repository.
2. Push the project to GitHub.
3. In the repository settings, enable GitHub Pages using the GitHub Actions workflow.
4. The workflow in .github/workflows/gh-pages.yml will build and publish the site automatically.
