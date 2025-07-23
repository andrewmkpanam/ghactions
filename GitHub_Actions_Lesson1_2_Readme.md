# CI/CD with GitHub Actions: Lesson 1 & 2 Summary

This document outlines the implementation of Continuous Integration (CI) using **GitHub Actions**, as taught in Module 3 - Lessons 1 and 2. It includes the configuration of build and test steps, environment variables, secrets, conditional execution, job outputs, and build matrices.

---

## 📘 Lesson 1: Building and Testing Code

### 🎯 Objectives

- Set up build steps in GitHub Actions.
- Run tests as part of the CI process.

---

### ✅ Step-by-Step Implementation

#### 1. Defining the Build Job

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      # Steps defined below
```

#### 2. Adding Build Steps

```yaml
steps:
  - uses: actions/checkout@v2
    # Checks out the repository so we can access code

  - name: Install dependencies
    run: npm install
    # Installs packages from package.json

  - name: Build
    run: npm run build
    # Executes the build script
```

#### 3. Running Tests in the Workflow

```yaml
  - name: Run tests
    run: npm test
    # Runs the test script in package.json
```

---

### 🧠 Learner Notes

- The `build` job contains code checkout, dependency installation, code building, and test execution.
- `runs-on: ubuntu-latest` ensures jobs run on the latest Ubuntu runner.
- Uses standard Node.js commands: `npm install`, `npm run build`, `npm test`.

---

## 🛠️ Additional YAML Concepts

### 1. Environment Variables

```yaml
env:
  CUSTOM_VAR: value
  # Available globally to all jobs

jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - name: Use environment variable
        run: echo $CUSTOM_VAR
```

---

### 2. Working with Secrets

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Use secret
        run: |
          echo "Access Token: ${{ secrets.ACCESS_TOKEN }}"
```

---

### 3. Conditional Execution

```yaml
jobs:
  conditional-job:
    runs-on: ubuntu-latest
    if: github.event_name == 'push' && github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v2
```

---

### 4. Outputs and Inputs Between Steps

```yaml
jobs:
  example:
    runs-on: ubuntu-latest
    steps:
      - id: step-one
        run: echo "::set-output name=value::$(echo hello)"

      - id: step-two
        run: echo "Received value from previous step: ${{ steps.step-one.outputs.value }}"
```

---

## 📗 Lesson 2: Configuring Build Matrices

### 🎯 Objectives

- Implement parallel and matrix builds.
- Manage dependency versions across different environments.

---

### ⚙️ Example Matrix Strategy Configuration

```yaml
name: Matrix CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [14, 16, 18]
        os: [ubuntu-latest, macos-latest]
    name: Node ${{ matrix.node-version }} - OS ${{ matrix.os }}
    steps:
      - uses: actions/checkout@v2

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - name: Install dependencies
        run: npm install

      - name: Run build
        run: npm run build

      - name: Run tests
        run: npm test
```

---

## ✅ Summary

| Feature                  | Status   |
|--------------------------|----------|
| Code checkout            | ✅        |
| Dependency installation  | ✅        |
| Build execution          | ✅        |
| Test execution           | ✅        |
| Environment variables    | ✅        |
| Secrets handling         | ✅        |
| Conditional execution    | ✅        |
| Step outputs             | ✅        |
| Matrix builds            | ✅        |

This workflow setup provides a complete CI solution with test automation, configuration management, and parallel testing across multiple environments.

---
