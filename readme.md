
# 🚀 GitHub Actions CI Workflow – Practical Implementation Report

This report outlines the practical steps I followed to implement a GitHub Actions workflow for a simple Node.js application. It includes automated testing and deployment to AWS EC2 using GitHub Actions.

---

## 1. ✅ Initializing the GitHub Repository

I began by creating a new repository on GitHub and cloning it to my local machine. This provided the foundation for version control and integration with GitHub Actions.

---

## 2. 🛠️ Creating a Simple Node.js Application

I initialized a Node.js project using:

```bash
npm init -y
```

Then, I created a basic Express server in `index.js`:

```javascript
const express = require('express');
const app = express();
const port = process.env.PORT || 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  console.log(`App listening at http://localhost:${port}`);
});
```

I committed and pushed the code to GitHub using:

```bash
git add .
git commit -m "Initial commit with Express server"
git push origin main
```

---

## 3. ✅ Writing and Running Automation Tests

To ensure my application behaves as expected, I wrote automated tests using `jest` and `supertest`.

### Installation

```bash
npm install --save-dev jest supertest
```

### Test File: `app.test.js`

```javascript
const request = require('supertest');
const express = require('express');

const app = express();
app.get('/', (req, res) => {
  res.status(200).send('Hello World!');
});

describe('GET /', () => {
  it('responds with Hello World!', async () => {
    const response = await request(app).get('/');
    expect(response.status).toBe(200);
    expect(response.text).toBe('Hello World!');
  });
});
```

### package.json Script

```json
"scripts": {
  "test": "jest"
}
```

With this setup, I was able to run my tests locally using:

```bash
npm test
```

---

## 4. ⚙️ Configuring GitHub Actions CI Workflow

I created a `.github/workflows/node.js.yml` file to automate testing on push and pull request events.

### CI Workflow

```yaml
name: Node.js CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [14.x, 16.x]

    steps:
      - uses: actions/checkout@v2

      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}

      - run: npm ci
      - run: npm run build --if-present
      - run: npm test
```

This pipeline:
- Checks out the code
- Sets up Node.js
- Installs dependencies
- Optionally builds
- Runs all tests

---

## 5. 🚀 Creating a GitHub Actions Workflow for AWS EC2 Deployment

I also set up a deployment pipeline to an AWS EC2 instance. I added the necessary SSH credentials and environment variables as GitHub Secrets (`EC2_KEY`, `EC2_HOST`, `EC2_USER`).

### Deployment Workflow: `.github/workflows/deploy.yml`

```yaml
name: Deploy to AWS EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Setup SSH key
      run: |
        mkdir -p ~/.ssh
        echo "${{ secrets.EC2_KEY }}" > ~/.ssh/id_rsa
        chmod 600 ~/.ssh/id_rsa

    - name: SSH and deploy
      run: |
        ssh -o StrictHostKeyChecking=no ${{ secrets.EC2_USER }}@${{ secrets.EC2_HOST }} << 'EOF'
          cd /home/${{ secrets.EC2_USER }}/myapp
          git pull origin main
          npm install
          pm2 restart app || pm2 start index.js --name app
        EOF
```

This script:
- Logs into the EC2 instance
- Pulls the latest code
- Installs dependencies
- Restarts the app with `pm2`

---

## 6. 🧪 Experimentation and Learning

I experimented with multiple Node.js versions in the test matrix, tested failure cases, and explored branching strategies to trigger selective workflows. This helped me understand the flexibility and power of GitHub Actions in real CI/CD setups.

---

## ✅ Conclusion

By completing this project, I set up a full CI/CD pipeline using GitHub Actions:
- Automated tests run on every push/pull request.
- The app is automatically deployed to AWS EC2 on push to `main`.
- The project is production-ready with test automation and repeatable deployment.

