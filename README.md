# MLOPS Complete ML Pipeline with DVC

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg) ![DVC](https://img.shields.io/badge/DVC-Latest-orange.svg) ![AWS](https://img.shields.io/badge/AWS-S3-yellow.svg) ![License](https://img.shields.io/badge/License-MIT-green.svg)
## Overview

This repository demonstrates how to build a complete **Machine Learning Pipeline** using **DVC (Data Version Control)**.

The project covers:

- Data Versioning
- Pipeline Automation
- Parameter Management
- Experiment Tracking
- Model Versioning
- Remote Storage using AWS S3
- Reproducible ML Workflows

---

# Project Structure

```text
MLOPS_Complete_ML_Pipeline_01/
│
├── .dvc/
├── .vscode/
│
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
│
├── dvclive/
│
├── experiments/
│
├── logs/
│
├── models/
│
├── reports/
│
├── src/
│   ├── data_ingestion.py
│   ├── data_preprocessing.py
│   ├── feature_engineering.py
│   ├── model_building.py
│   └── model_evaluation.py
│
├── .dvcignore
├── .env
├── .gitignore
├── dvc.lock
├── dvc.yaml
├── LICENSE
├── params.yaml
├── projectflow.txt
└── README.md
```

---

# Prerequisites

- Python 3.10+
- Git
- DVC
- AWS CLI
- AWS Account (for remote storage)

---

# Installation

Clone the repository

```bash
git clone https://github.com/Here-Im-2109/MLOPS_Complete_ML_Pipeline_01.git

cd MLOPS_Complete_ML_Pipeline_01
```

Create Virtual Environment

```bash
conda create -p mlops_venv python=3.10.0
```

Activate Environment

### Linux

```bash
conda activate /home/here_im/mlops_venv
```

Install Dependencies

```bash
pip install -r requirements.txt
```

---

# Building the ML Pipeline

## Step 1

Create a GitHub repository and clone it locally.

```bash
git clone https://github.com/Here-Im-2109/MLOPS_Complete_ML_Pipeline_01.git
```

---

## Step 2

Create the `src/` directory and implement each module individually.

```
data_ingestion.py
data_preprocessing.py
feature_engineering.py
model_building.py
model_evaluation.py
```

Run each file separately to ensure it works correctly.

---

## Step 3

Update `.gitignore`

```gitignore
data/
models/
reports/
logs/
dvclive/
```

---

## Step 4

Commit the initial project.

```bash
git add .
git commit -m "Initial project setup"
git push
```

---

# DVC Pipeline (Without Parameters)

## Initialize DVC

```bash
dvc init
```

---

## Create `dvc.yaml`

Define all pipeline stages.

Example:

```yaml
stages:
  data_ingestion:
    cmd: python src/data_ingestion.py
    deps:
    - src/data_ingestion.py
    params:
    - data_ingestion.test_size
    outs:
    - data/raw
  data_preprocessing:
    cmd: python src/data_preprocessing.py
    deps:
    - data/raw
    - src/data_preprocessing.py
    outs:
    - data/interim
  feature_engineering:
    cmd: python src/feature_engineering.py
    deps:
    - data/interim
    - src/feature_engineering.py
    params:
    - feature_engineering.max_features
    outs:
    - data/processed
  model_building:
    cmd: python src/model_building.py
    deps:
    - data/processed
    - src/model_building.py
    params:
    - model_building.n_estimators
    - model_building.random_state
    outs:
    - models/model.pkl
  model_evaluation:
    cmd: python src/model_evaluation.py
    deps:
    - models/model.pkl
    - src/model_evaluation.py
    metrics:
    - reports/metrics.json
params:
- dvclive/params.yaml
metrics:
- dvclive/metrics.json
plots:
- dvclive/plots/metrics:
    x: step
```

---

## Reproduce Pipeline

```bash
dvc repro
```

---

## Visualize Pipeline

```bash
dvc dag
```

---

## Commit Changes

```bash
git add .
git commit -m "Added DVC pipeline"
git push
```

---

# Parameterized Pipeline

Create `params.yaml`

Example

```yaml
data_ingestion:
  test_size: 0.30

feature_engineering:
  max_features: 500

model_building:
  n_estimators: 25
  random_state: 2
```

---

Update `dvc.yaml`

```yaml
params:
  - params.yaml
```

or

```yaml
params:
  - model_building.random_state
  - model_building.max_depth
  - model_building.learning_rate
```

---

Run Pipeline Again

```bash
dvc repro
```

Commit

```bash
git add .
git commit -m "Added parameterized pipeline"
git push
```

---

# Experiment Tracking with DVCLive

Install

```bash
pip install dvclive
```

---

Example

```python
from dvclive import Live

with Live(save_dvc_exp=True) as live:

            live.log_metric('accuracy', accuracy_score(y_test, y_test))
            live.log_metric('precision', precision_score(y_test, y_test))
            live.log_metric('recall', recall_score(y_test, y_test))
            live.log_params(params)
```

---

Run Experiment

```bash
dvc exp run
```

---

Show Experiments

```bash
dvc exp show
```

---

Apply Previous Experiment

```bash
dvc exp apply <experiment-name>
```

---

Delete Experiment

```bash
dvc exp remove <experiment-name>
```

---

Modify `params.yaml`

Run again

```bash
dvc exp run
```

Each execution becomes a new experiment.

Commit

```bash
git add .
git commit -m "Tracked experiments"
git push
```

---

# Configure AWS S3 as DVC Remote

## Step 1

Login to AWS Console

---

## Step 2

Create an IAM User

Save

- Access Key
- Secret Key

---

## Step 3

Create an S3 Bucket

Example

```
mlops-complete-pipeline
```

---

## Step 4

Install DVC S3 Support

```bash
pip install "dvc[s3]"
```

---

## Step 5

Install AWS CLI

```bash
pip install awscli
```

Verify

```bash
aws --version
```

---

## Step 6

Configure AWS

```bash
aws configure
```

Enter

```
AWS Access Key ID

AWS Secret Access Key

Region

Output Format
```

---

## Step 7

Add Remote Storage

```bash
dvc remote add -d dvcstore s3://your-bucket-name
```

Example

```bash
dvc remote add -d dvcstore s3://mlops-complete-pipeline
```

---

## Step 8

Push Data

```bash
dvc commit

dvc push
```

---

Commit

```bash
git add .
git commit -m "Configured DVC Remote"
git push
```

---

# Useful DVC Commands

| Command | Description |
|----------|-------------|
| `dvc init` | Initialize DVC |
| `dvc repro` | Reproduce pipeline |
| `dvc dag` | Show pipeline graph |
| `dvc exp run` | Run experiment |
| `dvc exp show` | View experiments |
| `dvc exp apply` | Apply experiment |
| `dvc exp remove` | Delete experiment |
| `dvc metrics show` | Show metrics |
| `dvc params diff` | Compare parameters |
| `dvc remote list` | List remotes |
| `dvc push` | Upload artifacts |
| `dvc pull` | Download artifacts |
| `dvc status` | Check pipeline status |

---

# Complete Workflow

### Repository Setup

- Create GitHub repository
- Clone repository
- Create project structure
- Add pipeline modules
- Commit to Git

---

### DVC Pipeline

- Initialize DVC
- Create `dvc.yaml`
- Run `dvc repro`
- Check `dvc dag`
- Commit changes

---

### Parameter Management

- Create `params.yaml`
- Link parameters with `dvc.yaml`
- Run `dvc repro`
- Commit changes

---

### Experiment Tracking

- Install DVCLive
- Add logging
- Run `dvc exp run`
- View experiments
- Modify parameters
- Create new experiments
- Commit changes

---

### Remote Storage

- Create IAM User
- Create S3 Bucket
- Configure AWS CLI
- Add DVC Remote
- Push Data
- Commit changes

---

# License

This project is licensed under the MIT License.

---

# Author

**Arpan Chandra**

National Institute of Technology Durgapur

B.Tech in Electrical Engineering

Machine Learning • MLOps • Deep Learning • Computer Vision

---

# References

- https://dvc.org/
- https://dvc.org/doc
- https://dvc.org/doc/start
- https://docs.aws.amazon.com/
- https://aws.amazon.com/s3/
- https://docs.aws.amazon.com/cli/