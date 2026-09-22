# GitHub Actions CI/CD Deployment Project

## 📌 Project Overview

This is a simple **CI/CD automation project using GitHub Actions**.

The project demonstrates how code changes pushed to a GitHub repository can automatically trigger a workflow that builds a Docker image and deploys the application to an AWS EC2 server.

### 🔄 CI/CD Flow

```text
Developer
    ↓
GitHub Repository
    ↓
GitHub Actions
    ↓
Build Docker Image
    ↓
Deploy to AWS EC2
    ↓
Run Docker Container
    ↓
Access Application
```

---

## 🛠️ Technologies Used

* **HTML** – Frontend webpage
* **Docker** – Containerization
* **GitHub** – Source code management
* **GitHub Actions** – CI/CD automation
* **AWS EC2** – Deployment server
* **Linux** – Server environment

---

## 📂 Project Structure

```text
github-action-project1/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── Dockerfile
│
├── index.html
│
└── README.md
```

### Files Explanation

| File         | Purpose                                   |
| ------------ | ----------------------------------------- |
| `index.html` | Contains the website content              |
| `Dockerfile` | Defines how the Docker image is created   |
| `deploy.yml` | Defines the GitHub Actions CI/CD workflow |
| `README.md`  | Project documentation                     |

---

## ⚙️ How It Works

### 1. Developer Changes Code

The develop
