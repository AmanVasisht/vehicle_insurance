# 🚗 Vehicle Data MLOps Project: End-to-End Pipeline

An industrial-grade MLOps solution for vehicle data analysis. This project implements a fully automated pipeline encompassing data ingestion from NoSQL databases, modular machine learning workflow, and continuous deployment via AWS and GitHub Actions.

## 📋 Table of Contents

* [Project Architecture](https://www.google.com/search?q=%23-project-architecture)
* [Key Features](https://www.google.com/search?q=%23-key-features)
* [Prerequisites](https://www.google.com/search?q=%23-prerequisites)
* [Local Environment Setup](https://www.google.com/search?q=%23-local-environment-setup)
* [Configuration](https://www.google.com/search?q=%23-configuration)
* [MongoDB Setup](https://www.google.com/search?q=%23mongodb-setup)
* [AWS Setup](https://www.google.com/search?q=%23aws-setup)


* [ML Pipeline Modules](https://www.google.com/search?q=%23-ml-pipeline-modules)
* [CI/CD & Deployment](https://www.google.com/search?q=%23-cicd--deployment)
* [Usage](https://www.google.com/search?q=%23-usage)

---

## 🏗 Project Architecture

This project follows a strict modular structure for scalability and maintainability.

1. **Data Layer:** MongoDB Atlas (NoSQL).
2. **Orchestration:** Custom Python modular pipeline.
3. **Model Registry:** AWS S3 buckets for artifact storage.
4. **Containerization:** Docker.
5. **Deployment:** AWS EC2 (Ubuntu) via AWS ECR.

---

## 🌟 Key Features

* **Custom Logging & Exception Handling:** Centralized tracking for debugging and audit trails.
* **Automated Data Ingestion:** Connects to MongoDB to fetch and transform data into DataFrames.
* **Data Validation:** Schema validation to ensure data integrity before training.
* **Model Pusher:** Automatically pushes models to AWS S3 only if they pass evaluation thresholds (Acc > 80% and diff > 0.02).
* **Continuous Integration/Deployment (CI/CD):** Fully automated deployment pipeline using GitHub Actions and AWS EC2 as a self-hosted runner.

---

## 🛠 Prerequisites

* Python 3.10+
* Anaconda or Miniconda
* MongoDB Atlas Account
* AWS Account (IAM, S3, ECR, EC2 access)
* Docker Desktop (for local testing)

---

## 💻 Local Environment Setup

1. **Clone the Repository:**
```bash
git clone <your-repo-url>

```


2. **Create and Activate Virtual Environment:**
```bash
conda create -n vehicle python=3.10 -y
conda activate vehicle

```


3. **Install Dependencies:**
The project uses `setup.py` and `pyproject.toml` for local package installation.
```bash
pip install -r requirements.txt

```


4. **Verify Installation:**
Run `pip list` to ensure local packages (src) are installed.

---

## ⚙ Configuration

### MongoDB Setup

This project uses MongoDB Atlas for data storage.

1. **Create Cluster:** Set up a free M0 sandbox cluster on MongoDB Atlas.
2. **Network Access:** Allow access from `0.0.0.0/0` (or specific IPs).
3. **Connection String:** Get the Python driver connection string.
4. **Data Injection:** Use the notebook `notebook/mongoDB_demo.ipynb` to push the dataset to your cluster.

### AWS Setup

The project requires AWS for model storage (S3) and deployment (ECR/EC2).

**1. IAM User:**
Create a user (e.g., `firstproj`) with `AdministratorAccess`. Generate CLI Access Keys.

**2. S3 Bucket:**
Create a bucket (e.g., `my-model-mlopsproj31`) in `us-east-1` for storing model artifacts.

**3. Environment Variables:**
Export your credentials to your environment.

| Bash (Linux/Mac) | PowerShell (Windows) |
| --- | --- |
| `export MONGODB_URL="mongodb+srv://..."` | `$env:MONGODB_URL = "mongodb+srv://..."` |
| `export AWS_ACCESS_KEY_ID="<YOUR_KEY>"` | `$env:AWS_ACCESS_KEY_ID="<YOUR_KEY>"` |
| `export AWS_SECRET_ACCESS_KEY="<YOUR_SECRET>"` | `$env:AWS_SECRET_ACCESS_KEY="<YOUR_SECRET>"` |

---

## 🧠 ML Pipeline Modules

The pipeline is triggered via `demo.py` or the training route in `app.py`.

1. **Data Ingestion:** Reads from MongoDB, splits data into Train/Test/Validate.
2. **Data Validation:** Validates data against `schema.yaml`.
3. **Data Transformation:** Handles categorical encoding, scaling, and feature engineering.
4. **Model Trainer:** Trains the model and saves the artifact.
5. **Model Evaluation:** Compares the new model against the S3 production model.
6. **Model Pusher:** Deploys the improved model to the S3 Registry.

---

## 🚀 CI/CD & Deployment

We use **GitHub Actions** with a **Self-Hosted Runner** on AWS EC2.

### 1. Docker & ECR

* A `Dockerfile` is included to containerize the application.
* An AWS ECR repository (`vehicleproj`) is used to store the images.

### 2. GitHub Secrets

Configure the following in your GitHub Repository Settings -> Secrets:

* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_DEFAULT_REGION` (us-east-1)
* `ECR_REPO` (URI of your ECR repo)

### 3. EC2 Self-Hosted Runner Setup

1. Launch an **Ubuntu 24.04 (T2 Medium)** instance.
2. Connect via SSH and install Docker:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu
newgrp docker

```


3. **Link GitHub Runner:** Go to Repo Settings -> Actions -> Runners -> New Self-Hosted Runner. Run the provided commands on your EC2 instance.
4. **Port Configuration:** Open Port `5080` in the EC2 Security Group inbound rules.

---

## 🎮 Usage

Once deployed, the application is accessible via the EC2 Public IP.

* **Web Interface:** `http://<EC2-Public-IP>:5080`
* **Train Model:** `http://<EC2-Public-IP>:5080/training`
* **Prediction:** Use the UI to submit vehicle data and get price predictions.
