# MLOps Project - Vehicle Insurance Prediction

Welcome to this MLOps project, designed to demonstrate a robust pipeline for managing vehicle insurance data. This project aims to impress  visitors by showcasing the various tools, techniques, services, and features that go into building and deploying a machine learning pipeline for real-world data management. Follow along to learn about project setup, data processing, model deployment, and CI/CD automation!

---



## 📁 Objective

 - The goal of this project is to develop a machine learning model that predicts whether a customer is likely to be interested in purchasing vehicle insurance. The    prediction is based on customer demographics, vehicle details, and historical policy information.

## 📁 Dataset Description
 - The dataset includes the following features:

 - Gender: Customer gender (Male/Female)
 - Age: Age of the customer
 - Driving_License: 1 if the customer has a driving license, 0 otherwise
 - Region_Code: Encoded region identifier
 - Previously_Insured: 1 if the customer was previously insured, 0 otherwise
 - Vehicle_Age: Age of the vehicle (< 1 Year, 1-2 Year, > 2 Years)
 - Vehicle_Damage: Whether the vehicle was previously damaged (Yes/No)
 - Annual_Premium: The amount paid by the customer annually for the insurance
 - Policy_Sales_Channel: Code for the sales channel of the policy
 - Vintage: Number of days the customer has been associated with the company
 - Response: Target variable (1 = interested, 0 = not interested)



## 📁 Project Setup and Structure

### Step 1: Project Template
- Start by executing the `template.py` file to create the initial project template, which includes the required folder structure and placeholder files.

### Step 2: Package Management
- Write the setup for importing local packages in `setup.py` and `pyproject.toml` files.


### Step 3: Virtual Environment and Dependencies
- Create a virtual environment and install required dependencies from `requirements.txt`:
  ```bash
  conda create -n vehicle_souvik python=3.10 -y
  conda activate vehicle_souvik
  pip install -r requirements.txt
  ```
- Verify the local packages by running:
  ```bash
  pip list
  ```

---

## 📊 MongoDB Setup and Data Management

### Step 4: MongoDB Atlas Configuration
1. Sign up for [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) and create a new project.
2. Set up a free M0 cluster, configure the username and password, and allow access from any IP address (`0.0.0.0/0`).
3. Retrieve the MongoDB connection string for Python and save it (replace `<password>` with your password).

### Step 5: Pushing Data to MongoDB
1. Create a folder named `notebook`, add the dataset, and create a notebook file `mongoDB_demo.ipynb`.
2. Use the notebook to push data to the MongoDB database.
3. Verify the data in MongoDB Atlas under Database > Browse Collections.

---

## 📝 Logging, Exception Handling, and EDA

### Step 6: Set Up Logging and Exception Handling
- Create logging and exception handling modules. Test them on a demo file `demo.py`.

### Step 7: Exploratory Data Analysis (EDA) and Feature Engineering
- Analyze and engineer features in the `EDA` and `Feature Engg` notebook for further processing in the pipeline.

---

## 📥 Data Ingestion

### Step 8: Data Ingestion Pipeline
- Define MongoDB connection functions in `configuration.mongo_db_connections.py`.
- Develop data ingestion components in the `data_access` and `components.data_ingestion.py` files to fetch and transform data.
- Update `constants`, `entity/config_entity.py` and `entity/artifact_entity.py` with relevant ingestion configurations.
- Update `constants` folder with data ingestion related variables.
- Run `demo.py` after setting up MongoDB connection as an environment variable.

### Setting Environment Variables
- Set MongoDB URL:
  ```bash
  # For Bash
  export MONGODB_URL="mongodb+srv://<username>:<password>...."
  # For Powershell
  $env:MONGODB_URL = "mongodb+srv://<username>:<password>...."
  ```
- **Note**: On Windows, you can also set environment variables through the system settings.

### Task Performed
- Extract data from MongoDB
- Store Raw data inside Artifact Folder
- Create an ingested folder inside Artifact Folder and create `Train` and `Test` files, the split configuration is mentioned in `constants` file
- Output create a `artifact/data_ingestion/feature_store/data.csv` , `artifact/data_ingestion/ingested/train.csv` and `artifact/data_ingestion/ingested/test.csv`

---

## 🔍 Data Validation, Transformation & Model Training

### Step 9: Data Validation
- Define schema in `config.schema.yaml` and implement data validation functions in `utils.main_utils.py`.

### Task Performed in Data Validation:
- Update `constants`, `entity/config_entity.py` and `entity/artifact_entity.py` with relevant data Validation configurations.
- Create common method/functions used in Project. Eg: read/write Yaml, save/load numpy array, save object in  `mains_utils.py`
- the `schema.yaml` will be used to validate, whether number of column which is ingested from MongoDB is correct, number of numerical and catagorical columns counts matches aong with datatype.
- output create a `artifact/data_validation/report.yml` saying whether validation has passed or not and the reason for failure.


### Step 10: Data Transformation
- Implement data transformation logic in `components.data_transformation.py` 

### Task Performed in Data Transformation:
- Data Transformation will only start if Data validation status is `true`
- Update `constants`, `entity/config_entity.py` and `entity/artifact_entity.py` with relevant data Transformation configurations.
- Map the `gender` column to `0` for Female or `1` for Male.
- Create a preprocessor pipeline which includes Standard Scaler on numeric features and min_max scaler on catagorical features.
- Create dummy variables for catagorical variables
- Rename column if required
- Drop Columns not used
- SMOTEENN is applied after preprocessor pipelin to handle inbalanced dataset.
- Output creates `artifact/data_transfomation/transformed/test.npy` , `artifact/data_transfomation/transformed/train.npy` , `artifact/data_transfomation/transformed_object/preprocessing.pkl`

### Step 11: Model Training
- Define and implement model training steps in `components.model_trainer.py` and create `estimator.py` in the `entity` folder.

### Task Performed in Model Training:
- Update `constants`, `entity/config_entity.py` and `entity/artifact_entity.py` with relevant Model Training configurations.
- the hyperparameters can be added in `constants` or it can be added in `models.yaml` file
- HP should be done in jupyter notebook and the best parameters should be added here therefore gridsearchCV is not done here.
- Input:  data_transformation_artifact and model__trainer_config
- Read the `train` and `test` npy files, split the features and target variables, initiate random Forrest with parameters specified in constants/models.yaml
- Predict and evaluate on the   `test` data
- This will return the `model` and the `metric_artifact`
- Save model if the accuracy is greater than the expected accuracy saved in constants.
- Output craetes `artifact/model_trainer/trained_model/model.pkl`

---

## 🌐 AWS Setup for Model Evaluation & Deployment

### Step 12: AWS Setup
1. Log in to the AWS console, create an IAM user, and grant `AdministratorAccess`.
2. Set AWS credentials as environment variables.
   ```bash
   # For Bash
   export AWS_ACCESS_KEY_ID="YOUR_AWS_ACCESS_KEY_ID"
   export AWS_SECRET_ACCESS_KEY="YOUR_AWS_SECRET_ACCESS_KEY"
   ```

3. Configure S3 Bucket and add access keys in `constants.__init__.py`. Note a S3 Bucket should be created before hand
4. The purpose of doing this is to store the Processed data, preprocessor.pkl and best model into AWS.



### Step 13: Model Evaluation and Model Pusher to S3
- Create an S3 bucket named `my-model-mlopsproj` in the `us-east-1` region.
- Develop code to push/pull models to/from the S3 bucket in `src.cloud_storage.aws_storage.py` and `entity/s3_estimator.py`.

### Task performed:
-  Update `constants`, `entity/config_entity.py` and `entity/artifact_entity.py` with relevant Model evaluation configurations.
- In constants there is a variable MODEL_EVALUATION_CHANGED_THRESHOLD_SCORE = 0.02, if the new model accuracy is greater than 0.02 then only push the model to AWS.
- Purpose of S3 estimator:
    - Setting up a SageMaker/local Estimator (for training)
    - Defining how and where training data is loaded from S3
    - Specifying where the model artifacts should be saved in S3
    - Optionally: Launching and monitoring training jobs
  - Inputs: model_eval_config, data_ingestion_artifacts(test data), model_trainer_artifacts
  - It first check if any model is present in S3, load the test data, perform data transformation on the test data, load the train data F1 score, load the model from S3 which is trained on train data and predict on the test data and generates test F1 score.
  - If the difference is greater than 0.02, push the new model to AWS S3 else ignore.
  - output: A new model file should be present in the AWS S3
---

## 🚀 Prediction Pipeline

### Step 14: Creation of Prediction Pipeline 
- Create `Prediction Pipeline` and set up `app.py` for FASTAPI integration.



### Step 15: Static and Template Directory
- Add `static` and `template` directories for web UI.

### Task performed:
- Initialize fastAPI application
- Add static and template directories for HTML rendering.
- Take inputs from users convert it to dataframe using `class VehicleData`
- Perform prediction on prediction pipeline using `class VehicleDataClassifier` calling model from AWS S3. Note before Prediction the user test data will go through necessary data transformation and preprocessor.pkl file.
- The app is now ready locally

---

## 🔄 CI/CD Setup with Docker, GitHub Actions, and AWS

### Step 16: Docker and GitHub Actions
1. Create `Dockerfile` and `.dockerignore`.
2. Set up GitHub Actions with AWS authentication by creating secrets in GitHub for:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`
   - `AWS_DEFAULT_REGION`
   - `ECR_REPO`

### Step 17: AWS EC2 and ECR
1. Set up an EC2 instance for deployment.
2. Install Docker on the EC2 machine.
3. Connect EC2 as a self-hosted runner on GitHub.

### Step 18: Final Steps
1. Open the 5080 port on the EC2 instance.
2. Access the deployed app by visiting `http://<public_ip>:5080`.

### Task Performed:
- Setup `dockerFile`
- Setup `.github/workflow/aws.yaml`
- Create a ECR and EC2 in AWS 
- Port mapping docker port 5000 map to EC2 machine port 5000
- In EC2 we need to install docker
- Create a self hosted runner under github actions 
- Setup self hoster runner in EC2, so that it is listening for jobs. It should change from offline to Idle.
- Add the secrets in github secrets
- Activate the port 5000 on EC2 edit inbound security rules
- OPen the public IP address and you should be able to see the html page

---

## 🛠️ Additional Resources
- **Crash Course on setup.py and pyproject.toml**: See `crashcourse.txt` for details.
- **GitHub Secrets**: Manage secrets for secure CI/CD pipelines.

---

## 🎯 Project Workflow Summary

1. **Data Ingestion** ➔ **Data Validation** ➔ **Data Transformation**
2. **Model Training** ➔ **Model Evaluation** ➔ **Model Deployment**
3. **CI/CD Automation** with GitHub Actions, Docker, AWS EC2, and ECR

---


This README provides a structured walkthrough of the MLOps project, showcasing the end-to-end pipeline, cloud integration, CI/CD setup, and robust data handling capabilities.