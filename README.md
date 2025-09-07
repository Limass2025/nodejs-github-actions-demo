

# CI/CD Pipeline with GitHub Actions: Step-by-Step Guide

## Introduction

This guide provides a comprehensive walkthrough for setting up a Continuous Integration and Continuous Deployment (CI/CD) pipeline using GitHub Actions. We'll create a Node.js application, implement automated testing, and deploy it using GitHub Actions workflows.

## Prerequisites

1. **Git and GitHub Knowledge**
   - Understanding of version control concepts
   - Familiarity with basic Git operations (clone, commit, push, pull)
   - GitHub account and repository management experience

2. **Programming Fundamentals**
   - Basic programming knowledge (JavaScript preferred)
   - Understanding of web application structure

3. **Node.js and npm**
   - Node.js and npm installed locally
   - Ability to set up Node.js projects and install dependencies

4. **Development Environment**
   - Text editor or IDE (VS Code, Atom, Sublime Text)
   - Command line/terminal access
   - Stable internet connection

5. **CI/CD Concepts (Optional but Helpful)**
   - Basic awareness of Continuous Integration and Deployment principles

## Lesson 1: Understanding Continuous Integration and Continuous Deployment

### Objectives
1. Define CI/CD and understand its benefits
2. Get familiar with the CI/CD pipeline

### Definition and Benefits of CI/CD

**Continuous Integration (CI)** is the practice of merging all developers' working copies to a shared mainline several times a day. This allows teams to detect integration errors early and often.

**Continuous Deployment (CD)** is the process of releasing software changes to production automatically and reliably after passing through the CI pipeline.

**Benefits of CI/CD:**
- **Faster release rate**: Automate the build, test, and deployment process
- **Improved developer productivity**: Reduce manual intervention and context switching
- **Better code quality**: Catch issues early with automated testing
- **Enhanced customer satisfaction**: Deliver features and fixes more quickly

*Screenshot: Diagram showing CI/CD workflow from code commit to production deployment*

### Overview of the CI/CD Pipeline

A typical CI/CD pipeline includes:

**CI Pipeline:**
1. Version control (code commit)
2. Code integration
3. Automated testing
4. Building the application

**CD Pipeline:**
1. Deploying to staging environment
2. Automated testing in staging
3. Deploying to production
4. Post-deployment monitoring

**Common Tools:**
- Version control: Git, GitHub
- CI/CD platforms: GitHub Actions, Jenkins, CircleCI
- Testing frameworks: Jest, Mocha, Cypress
- Deployment tools: Docker, Kubernetes, AWS

*Screenshot: Visual representation of a CI/CD pipeline with stages and tools*

## Lesson 2: Introduction to GitHub Actions

### Objectives
1. Understand what GitHub Actions is
2. Learn key concepts and terminology

### GitHub Actions Overview

GitHub Actions is a CI/CD platform integrated directly into GitHub that automates the build, test, and deployment pipelines of your software. It allows you to create workflows that respond to events in your repository.

### Key Concepts and Terminology

1. **Workflow**
   - Definition: A configurable automated process made up of one or more jobs
   - Example: A workflow to test and deploy a Node.js application upon a Git push
   - Implementation: Defined by a YAML file in the `.github/workflows` directory

2. **Event**
   - Definition: A specific activity that triggers a workflow
   - Examples: `push`, `pull_request`, issue creation, scheduled time
   - Implementation: Specified in the `on` section of the workflow file

3. **Job**
   - Definition: A set of steps in a workflow that execute on the same runner
   - Examples: A job that runs tests, a job that deploys to production
   - Implementation: Jobs can run sequentially or in parallel

4. **Step**
   - Definition: An individual task that runs commands within a job
   - Examples: Install dependencies (`npm install`), run tests (`npm test`)
   - Implementation: Steps can run scripts or use actions

5. **Action**
   - Definition: Standalone commands combined into steps to create a job
   - Examples: `actions/checkout` to check out repository code
   - Implementation: Can be written by you or provided by the community

6. **Runner**
   - Definition: A server that runs your workflows when triggered
   - Examples: GitHub-hosted runner (Ubuntu, Windows, macOS), self-hosted runner
   - Implementation: Specified in the `runs-on` section of a job

*Screenshot: GitHub Actions interface showing workflow, jobs, and steps*

## Practical Implementation

### Step 1: Setting Up the Project

#### 1.1 Initialize a GitHub Repository
1. Go to GitHub and create a new repository (e.g., `nodejs-github-actions-demo`)
2. Clone it to your local machine:
   ```bash
   git clone https://github.com/your-username/nodejs-github-actions-demo.git
   cd nodejs-github-actions-demo
   ```
![alt text](<Screenshot 2025-09-06 114712.png>)

![alt text](<Screenshot 2025-09-06 114909.png>)

#### 1.2 Create a Simple Node.js Application
1. Initialize a Node.js project:
   ```bash
   npm init -y
   ```
2. Install Express.js:
   ```bash
   npm install express
   ```
3. Create a file named `index.js` with the following content:
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
4. Update `package.json` to add a start script:
   ```json
   "scripts": {
     "start": "node index.js"
   }
   ```
![alt text](<Screenshot 2025-09-07 203632.png>)

![alt text](<Screenshot 2025-09-07 204509.png>)

#### 1.3 Add Code to Repository and Push to GitHub
1. Add all files to Git:
   ```bash
   git add .
   ```
2. Commit the changes:
   ```bash
   git commit -m "Initial commit with Node.js app"
   ```
3. Push to GitHub:
   ```bash
   git push origin main
   ```

![alt text](<Screenshot 2025-09-07 204702.png>)

![alt text](<Screenshot 2025-09-07 204758.png>)

![alt text](<Screenshot 2025-09-07 205055.png>)

### Step 2: Writing Your First GitHub Action Workflow

#### 2.1 Create the Workflow Directory and File
1. In your project root, create a directory structure:
   ```bash
   mkdir -p .github/workflows
   ```
2. Inside the `workflows` directory, create a file named `nodejs.yml`

![alt text](<Screenshot 2025-09-07 205415.png>)

#### 2.2 Define the Workflow
Add the following content to `nodejs.yml`:

```yaml
# Name of the workflow
name: Node.js CI

# Specifies when the workflow should be triggered
on:
  # Triggers the workflow on 'push' events to the 'main' branch
  push:
    branches: [ main ]
  # Also triggers the workflow on 'pull_request' events targeting the 'main' branch
  pull_request:
    branches: [ main ]

# Defines the jobs that the workflow will execute
jobs:
  # Job identifier, can be any name (here it's 'build')
  build:
    # Specifies the type of virtual host environment (runner) to use
    runs-on: ubuntu-latest
    
    # Strategy for running the jobs - this section is useful for testing across multiple environments
    strategy:
      # A matrix build strategy to test against multiple versions of Node.js
      matrix:
        node-version: [14.x, 16.x, 18.x]
    
    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so the job can access it
      - uses: actions/checkout@v3
      
      # Sets up the specified version of Node.js
      - name: Use Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      
      # Installs node modules as specified in the project's package-lock.json
      - run: npm ci
      
      # This command will only run if a build script is defined in the package.json
      - run: npm run build --if-present
      
      # Runs tests as defined in the project's package.json
      - run: npm test
```



#### 2.3 Explanation of the Workflow
1. **`name`**: Names the workflow (appears in GitHub Actions tab)
2. **`on`**: Defines when the workflow is triggered (push or pull_request to main branch)
3. **`jobs`**: Defines the jobs to execute (one job named "build")
4. **`runs-on`**: Specifies the runner environment (latest Ubuntu)
5. **`strategy.matrix`**: Allows running the job on multiple Node.js versions
6. **`steps`**: Sequence of tasks:
   - `actions/checkout@v3`: Checks out the repository
   - `actions/setup-node@v3`: Sets up Node.js environment
   - `npm ci`: Installs dependencies
   - `npm run build --if-present`: Runs build script if defined
   - `npm test`: Runs tests
![alt text](<Screenshot 2025-09-07 211232.png>)


### Step 3: Testing and Deployment

#### 3.1 Add Automated Tests
1. Install a testing framework (Jest):
   ```bash
   npm install --save-dev jest supertest
   ```
2. Create a test file `index.test.js`:
   ```javascript
   const request = require('supertest');
   const app = require('./index');

   describe('GET /', () => {
     it('should return Hello World!', async () => {
       const res = await request(app).get('/');
       expect(res.statusCode).toEqual(200);
       expect(res.text).toBe('Hello World!');
     });
   });
   ```
3. Update `package.json` to add a test script:
   ```json
   "scripts": {
     "start": "node index.js",
     "test": "jest"
   }
   ```

![alt text](<Screenshot 2025-09-07 211708.png>)
![alt text](<Screenshot 2025-09-07 211754.png>)

#### 3.2 Create a Deployment Workflow
1. Create a new workflow file `deploy.yml` in `.github/workflows`:
   ```yaml
   name: Deploy to Production

   on:
     push:
       branches: [ main ]

   jobs:
     deploy:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v3
         - name: Use Node.js
           uses: actions/setup-node@v3
           with:
             node-version: '18'
         - run: npm ci
         - name: Deploy to Production
           run: |
             echo "Deploying to production..."
             # Add your deployment commands here
             echo "Deployment complete!"
   ```

![alt text](<Screenshot 2025-09-07 211911.png>)
![alt text](<Screenshot 2025-09-07 211949.png>)

#### 3.3 Push Changes and Monitor Workflows
1. Commit and push all changes:
   ```bash
   git add .
   git commit -m "Add tests and deployment workflow"
   git push origin main
   ```
2. Go to your GitHub repository
3. Click on the "Actions" tab
4. Monitor the running workflows

![alt text](<Screenshot 2025-09-07 212401.png>)
![alt text](<Screenshot 2025-09-07 212604.png>)
## Execution Summary

This project successfully demonstrated:

1. **Understanding CI/CD Concepts**: Covered the fundamentals of Continuous Integration and Continuous Deployment, including their benefits and components.

2. **GitHub Actions Fundamentals**: Learned key concepts like workflows, events, jobs, steps, actions, and runners.

3. **Project Setup**: Created a Node.js application with Express.js and pushed it to a GitHub repository.

4. **CI Workflow Implementation**: Created a workflow that:
   - Triggers on push or pull request to main branch
   - Tests against multiple Node.js versions
   - Installs dependencies and runs tests

5. **Testing Integration**: Added automated tests using Jest and Supertest.

6. **Deployment Workflow**: Created a basic deployment workflow that can be extended for real deployments.

7. **Experimentation**: Explored ways to enhance workflows with different triggers, tests, and deployment strategies.

## Conclusion

GitHub Actions provides a powerful, flexible platform for implementing CI/CD pipelines directly within your GitHub repository. This project covered the essential concepts and practical steps to:

- Automate testing across multiple environments
- Ensure code quality with automated checks
- Prepare for automated deployments
- Extend workflows to meet specific project needs

By mastering GitHub Actions, you can significantly improve your development workflow, reduce manual errors, and deliver higher-quality software faster.
