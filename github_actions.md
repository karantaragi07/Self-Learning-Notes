# ⚙️ GitHub Actions Notes

---

## 🧩 Overview

GitHub Actions is a built-in **CI/CD and automation tool** provided by GitHub. It helps developers automate workflows such as building, testing, and deploying code directly from the repository. These workflows are written in **YAML** and stored inside the `.github/workflows/` directory. It eliminates the need for external CI/CD tools by tightly integrating with GitHub’s version control system, making it easy to trigger automation whenever code changes occur.

---

## 🔹 Key Concepts

- **Workflow** → The complete automation process triggered by specific events like push or pull requests.  
- **Event** → Any activity that triggers a workflow (e.g., `push`, `pull_request`, `schedule`, `workflow_dispatch`).  
- **Job** → A group of steps executed together on a runner (e.g., build, test, deploy).  
- **Step** → A single task within a job that can run a command or use an action.  
- **Action** → A reusable piece of code that performs specific tasks such as checkout, build, or deploy.  
- **Runner** → The virtual machine (GitHub-hosted or self-hosted) that executes the workflow jobs.

---

## 🔹 Basic Workflow Process

When developers push code to GitHub or open a pull request, **GitHub Actions** automatically triggers the defined workflow. The workflow executes sequentially — starting from checking out the code, setting up dependencies, building the project, running test cases, and finally deploying it if all checks pass. This ensures a fully automated and consistent CI/CD process across teams.

---

## 🔹 GitHub Actions and Feature Development Flow

When a project is already deployed and a new feature (for example, a navbar) needs to be added:
1. A new **feature branch** is created from the main branch to isolate changes.  
2. All feature-related work is done inside that branch.  
3. Once completed, the **build is tested** locally or in CI to ensure no errors.  
4. All **test cases** are executed to confirm no existing functionality breaks.  
5. After validation, a **pull request (PR)** is created for review and merge.  
6. Once merged, **GitHub Actions** automatically triggers the build and test pipeline.  
7. If successful, a **Docker image** is built and pushed to **Docker Hub**, making it ready for deployment.

This entire process — from code push to Docker image creation — can be fully automated using GitHub Actions.

---

## 🔹 GitHub Actions for Docker Projects

GitHub Actions can seamlessly integrate with Docker to automate containerization and deployment. After a successful build and test:
- A **Docker image** is created using the Dockerfile.  
- The image is **tagged** (e.g., `latest` or `v1.0.0`).  
- Using stored **Docker Hub credentials** (from GitHub Secrets), the image is **pushed automatically** to Docker Hub.  
- This ensures the latest version of the application is containerized and ready for deployment across different environments.

This Docker integration makes the CI/CD pipeline fully automated, consistent, and production-ready.

---

## 🔹 Secrets and Credentials Management

Sensitive data like Docker Hub credentials, API keys, or cloud tokens should never be hardcoded. Instead, they are stored in **GitHub Secrets**:
- Navigate to `Settings → Secrets → Actions`  
- Add credentials like `DOCKER_USERNAME` and `DOCKER_PASSWORD`  
- Access them inside workflows as `${{ secrets.SECRET_NAME }}`  

This ensures security and prevents credential leaks in public repositories.

---

## 🔹 Typical CI/CD Flow Using GitHub Actions

1. **Developer pushes code** to GitHub.  
2. **Workflow triggers automatically** based on defined events.  
3. Workflow performs the following tasks:  
   - **Builds the project** using tools like Maven, Gradle, or npm.  
   - **Runs tests** to verify application correctness.  
   - **Builds Docker image** for the latest version.  
   - **Pushes image to Docker Hub** or container registry.  
   - **Deploys to production/staging** environment if configured.  
4. **Notifications** (Slack/Email) can be sent after success or failure.

---

## 🔹 Common Workflow Triggers

- `push` → When code is pushed to a specific branch.  
- `pull_request` → When a PR is created or updated.  
- `schedule` → Runs on a defined cron schedule (e.g., daily builds).  
- `workflow_dispatch` → Manual trigger from GitHub UI.  
- `release` → When a new release is published.  

---

## 🔹 Benefits of GitHub Actions

- Fully integrated within GitHub — no separate setup required.  
- Easy to configure and modify using YAML files.  
- Supports multiple environments and **parallel jobs**.  
- Access to thousands of **prebuilt actions** in the marketplace.  
- Enables full **CI/CD automation** from code to deployment.  
- Improves consistency, reduces manual effort, and increases speed.  

---

## 🔹 Best Practices

- Keep workflows **modular and easy to maintain**.  
- Store all credentials in **GitHub Secrets**.  
- Use **caching** to improve build times.  
- Separate workflows for **build/test** and **deploy** stages.  
- Always **run tests** before deployment.  
- Tag Docker images with **version or commit SHA**.  
- Restrict workflow triggers to necessary branches like `main` or `develop`.  
- Use **status badges** in README to monitor workflow results.

---

## 🔹 Common Interview Topics

- Difference between **GitHub Actions** and **Jenkins**.  
- How to automate Docker image build and push using Actions.  
- Understanding of **workflow**, **job**, **step**, and **runner**.  
- How to use **GitHub Secrets** securely.  
- Role of GitHub Actions in **CI/CD pipelines**.  
- How to manually trigger workflows (`workflow_dispatch`).  
- Use of **self-hosted runners** for custom environments.  
- Troubleshooting failed builds or test cases in workflows.  

---

## 🔹 Summary

GitHub Actions provides a simple yet powerful way to automate the entire software delivery process — from **code integration**, **testing**, **Docker image creation**, to **deployment**. It is a core part of modern DevOps workflows, ensuring consistency, reliability, and speed across all stages of the development lifecycle.

---

 **Tip:** Always modularize your workflows and test them in separate branches before merging to production.
