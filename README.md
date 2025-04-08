
# 🚀 Deploying a Machine Learning Model on AWS SageMaker (50_Startups Project)

This project demonstrates an end-to-end ML pipeline using **AWS SageMaker**. We use the classic `50_Startups.csv` dataset to train a **Linear Regression model** that predicts the profit of startups based on various investments.

The project includes:
- Data preprocessing
- Model training with `sklearn`
- SageMaker integration
- Real-time endpoint deployment
- Lambda + API Gateway integration
- Postman/API testing
- Architecture diagram
- Video walkthrough

---

## 🧠 Problem Statement

Predict startup profit based on:
- R&D Spend
- Administration
- Marketing Spend
- State

We use Linear Regression to estimate the output and deploy the model using a scalable AWS ML infrastructure.

---

## 🗂️ Project Structure

```
📁 project-root/
├── 50_Startups.csv               # Dataset
├── startup_prediction.py         # Training script for SageMaker
├── AWS_Train.ipynb               # SageMaker notebook to run the training job
└── README.md                     # This file
```

---

## 🔧 Step-by-Step Workflow

### 1. Upload Dataset to S3

- Create an S3 bucket (`sagemaker-startup-bucket`)
- Upload `50_Startups.csv` to a folder like `data/`

---

### 2. Launch a SageMaker Notebook Instance

- Choose instance type: `ml.t2.medium`
- Assign an IAM role with **S3 full access**
- Clone or upload the repo to the Jupyter environment

---

### 3. Train Model via `AWS_Train.ipynb`

- The notebook:
  - Loads the dataset
  - Runs `startup_prediction.py` using `SKLearn` Estimator
  - Launches the training job
  - Saves the trained model to `S3`

### Example Cell:

```python
from sagemaker.sklearn.estimator import SKLearn

estimator = SKLearn(
    entry_point="startup_prediction.py",
    role=role,
    instance_type="ml.m4.xlarge",
    framework_version="0.23-1",
    py_version="py3",
    output_path=s3_output
)

estimator.fit({"train": s3_input})
```

---

### 4. Deploy the Model

```python
predictor = estimator.deploy(
    initial_instance_count=1,
    instance_type="ml.m4.xlarge"
)
```

This creates a real-time **SageMaker endpoint**.

---

### 5. Setup AWS Lambda Function

Use `sagemaker_lambda.py`:

- Create a Lambda function
- Attach the IAM role with SageMaker runtime permissions
- Replace `{SAGEMAKER ENDPOINT}` with the actual endpoint name

### Sample Logic:

```python
response = runtime.invoke_endpoint(
    EndpointName=ENDPOINT_NAME,
    Body=json.dumps(payload)
)
result = json.loads(response['Body'].read().decode())
```

---

### 6. Integrate with API Gateway

- Create a **REST API** in API Gateway
- Connect it to the Lambda function
- Enable **POST method**
- Deploy and get a **public API URL**

---

### 7. Test Using Postman

Use this payload to test your endpoint:

```json
{
  "data": [165349.2, 136897.8, 471784.1]
}
```

Expected output: Predicted profit as a single float.

---

---

## 💡 Key Takeaways

- AWS SageMaker abstracts away infra so you can focus on ML.
- Using Lambda + API Gateway creates a **serverless** prediction API.
- Real-time ML deployment can be done end-to-end in under 1 hour.

---

## 📌 Future Enhancements

- Use XGBoost or Deep Learning models
- Add frontend dashboard for prediction
- Fine-tune model and retrain with more data

---

## 🙌 Acknowledgments

Built with ❤️ by a hands-on ML engineer passionate about Gen AI, cloud automation, and building production-grade pipelines.


