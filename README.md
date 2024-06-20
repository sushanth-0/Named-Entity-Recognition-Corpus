<div align="center">
  <h1>Named Entity Recognition (NER) using BERT</h1>

  <p>
    An end-to-end NLP pipeline to perform named entity recognition (NER) using a pre-trained HuggingFace Transformer, BERT, and deploy to Google Cloud Platform (GCP) with Docker and CircleCI.
  </p>
</div>

<details>
  <summary>Table of Contents</summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#tech-stack">Tech Stack</a></li>
      </ul>
    </li>
    <li><a href="#how-to-run">How to Run</a></li>
    <li><a href="#training-pipeline">Training Pipeline</a></li>
    <li><a href="#prediction-pipeline">Prediction Pipeline</a></li>
    <li><a href="#deployment">Deployment</a></li>
    <li><a href="#workflow">Workflow</a></li>
    <li><a href="#gcp-cicd-deployment-with-circleci">GCP CI/CD Deployment with CircleCI</a></li>
  </ol>
</details>

## About The Project

This project is designed to accurately perform named entity recognition (NER) using a pre-trained HuggingFace Transformer, BERT. The pipeline involves data ingestion, transformation, model training, evaluation, and deployment.

### Tech Stack

- **Programming Language**: Python
- **Machine Learning Library**: HuggingFace Transformers (BERT)
- **Web Framework**: FastAPI
- **Containerization**: Docker
- **Cloud Services**: 
  - **GCP**: Google Cloud Platform for deployment
  - **GCP Bucket**: Data and model storage
  - **GCP Artifact Registry**: Docker image repository
  - **GCP Virtual Machine**: Hosting the application
- **CI/CD**: CircleCI
- **Version Control**: GitHub

## How to Run

Instructions to set up your local environment for running the project.

```bash
git clone https://github.com/yourusername/yourproject.git

cd yourproject
# Set up a virtual environment
python3 -m venv venv

source venv/bin/activate
# Install dependencies
pip install -r requirements.txt
```

## Usage

Instructions on how to use the project.

```bash
# Run the application
python app.py
```

## Training Pipeline

1. **Data Ingestion**: 
   - Ingested a .zip file from GCP bucket and unzipped it to a .csv file containing text and label columns.

2. **Data Transformation**:
   - Read `ner.csv` to a pandas DataFrame, split into train, test, and validation sets.
   - Extracted unique labels from the labels column and saved them as pickle files.

3. **Model Trainer**:
   - Trained a pre-trained BERT transformer model on the prepared dataset.

4. **Model Evaluation**:
   - Evaluated the model's performance on a test dataset and calculated accuracy metrics.

5. **Model Pusher**:
   - The model with the highest accuracy was pushed to the GCP bucket.

## Prediction Pipeline

- For user-provided text data, the tokenizer was applied for vector embedding.
- Predicted the named entity using the trained BERT model from the GCP bucket and classified them.

## Deployment

1. **Containerize the Application**:
   - Used Docker to containerize the application for easy deployment and scalability.
   - Stored the Docker image in the GCP Artifact Registry.

2. **Set up GCP Virtual Machine**:
   - Hosted the deployed application on a GCP Virtual Machine.
   - Pulled the Docker image from the GCP Artifact Registry and ran the application on the VM.

3. **Automate Deployment with CircleCI**:
   - Used CircleCI to automate the deployment workflow.
   - On each code push, retrained the model, built the Docker image, pushed it to the GCP Artifact Registry, pulled the image to the GCP VM, and ran the application.

4. **Web Application**:
   - Built a basic web app using FastAPI to expose the model's prediction functionality.


## GCP CI/CD Deployment with CircleCI

### Steps to Deploy

1. **Login to GCP Console**

2. **Create IAM User for Deployment**
   - **With specific access:**
     - **GCP Artifact Registry**: To manage Docker images.
     - **GCP Compute Engine**: To manage virtual machines.

3. **Create Artifact Registry Repository**
   - Navigate to the Artifact Registry in GCP.
   - Create a new Docker repository.
   - Save the URI of your repository for later use.

4. **Create GCP Virtual Machine (Ubuntu)**
   - Go to the Compute Engine section.
   - Create a new VM instance with the required specifications (e.g., Ubuntu).

5. **Install Docker on the GCP VM**
   - SSH into your VM instance.
   - Run the following commands to install Docker:

     ```bash
     sudo apt-get update -y
     sudo apt-get upgrade -y
     curl -fsSL https://get.docker.com -o get-docker.sh
     sudo sh get-docker.sh
     sudo usermod -aG docker $USER
     newgrp docker
     ```

6. **Set Up CircleCI**
   - Log in to CircleCI and create a new project.
   - Select the GitHub repository you want to set up with CircleCI.

7. **Set Environment Variables in CircleCI**
   - In the CircleCI project settings, go to the "Environment Variables" section.
   - Add the following environment variables:
     - `GCLOUD_SERVICE_KEY`: The JSON key for your service account.
     - `GOOGLE_COMPUTE_ZONE`: Your compute zone (e.g., `us-east1`).
     - `GOOGLE_PROJECT_ID`: Your Google Cloud project ID.

8. **Update CircleCI Configuration**:
   - Modify lines 42, 50, 54, 72, and 76 in your CircleCI configuration file as needed.

9. **Create a VM instance and setup scripts**:
   - Follow the setup scripts to configure your VM instance for deployment.
