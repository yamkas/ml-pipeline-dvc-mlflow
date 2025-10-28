# ML Pipeline with DVC and MLflow

This project demonstrates how to build an end-to-end machine learning pipeline using DVC (Data Version Control) for data and model versioning, and MLflow for experiment tracking. The pipeline focuses on training a Random Forest Classifier on the Pima Indians Diabetes Dataset, with clear stages for data preprocessing, model training, and evaluation.

## Project Structure

```
.
├── data/
│   ├── processed/      # Processed/transformed data
│   └── raw/           # Raw data files (versioned with DVC)
├── models/            # Trained model artifacts
├── src/              # Source code
│   ├── preprocess.py # Data preprocessing script
│   ├── train.py      # Model training script
│   └── evaluate.py   # Model evaluation script
├── dvc.yaml          # DVC pipeline configuration
├── params.yaml       # Model parameters and configurations
└── requirements.txt  # Project dependencies
```

## Key Features

### Data Version Control (DVC)
- Tracks and versions datasets, models, and pipeline stages
- Structures pipeline into reproducible stages (preprocessing, training, evaluation)
- Automatically re-executes stages when dependencies change
- Supports remote storage (e.g., DagsHub, S3) for large datasets and models

### Experiment Tracking with MLflow
- Tracks experiment metrics, parameters, and artifacts
- Logs model hyperparameters (e.g., n_estimators, max_depth)
- Records performance metrics like accuracy
- Facilitates comparison between different runs and models

## Pipeline Stages

### 1. Preprocessing
- Script: `src/preprocess.py`
- Input: Raw dataset (`data/raw/data.csv`)
- Output: Processed data (`data/processed/data.csv`)
- Functions: Column renaming and basic preprocessing

### 2. Training
- Script: `src/train.py`
- Input: Processed data
- Output: Trained model (`models/model.pkl`)
- Features: Random Forest Classifier with MLflow logging

### 3. Evaluation
- Script: `src/evaluate.py`
- Input: Trained model and test data
- Output: Performance metrics
- Metrics logged to MLflow for tracking

## Setup and Usage

### Installation

1. Clone the repository:
```bash
git clone https://github.com/yamkas/ml-pipeline-dvc-mlflow.git
cd ml-pipeline-dvc-mlflow
```

2. Create and activate a virtual environment:
```bash
# Create venv
conda create -p venv python==3.10 -y

# Activate venv
conda activate venv/
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

### DVC Pipeline Configuration

Add pipeline stages using DVC:

1. Preprocessing Stage:

```bash
dvc stage add -n preprocess \
    -p preprocess.input,preprocess.output \
    -d src/preprocess.py -d data/raw/data.csv \
    -o data/processed/data.csv \
    python src/preprocess.py
```

2. Training Stage:
```bash
dvc stage add -n train \
    -p train.data,train.model,train.random_state,train.n_estimators,train.max_depth \
    -d src/train.py -d data/raw/data.csv \
    -o models/model.pkl \
    python src/train.py
```

3. Evaluation Stage:
```bash
dvc stage add -n evaluate \
    -d src/evaluate.py -d models/model.pkl -d data/raw/data.csv \
    python src/evaluate.py
```

### Running the Pipeline

1. Run the complete pipeline:
```bash
dvc repro
```

2. View pipeline status:
```bash
dvc status
```

3. Start MLflow tracking server:
```bash
mlflow ui
```
Then visit http://localhost:5000 to view experiments.

## Project Goals

1. **Reproducibility**: Ensure consistent results across different environments using DVC
2. **Experimentation**: Track and compare different model versions using MLflow
3. **Collaboration**: Enable seamless teamwork with version control for both code and data

## Technology Stack

- **Python**: Core programming language
- **DVC**: Data and pipeline version control
- **MLflow**: Experiment tracking and model management
- **Scikit-learn**: Machine learning framework (Random Forest Classifier)