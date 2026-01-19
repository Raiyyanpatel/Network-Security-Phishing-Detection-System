# Network Security - Phishing Detection System

An end-to-end machine learning project for detecting phishing websites using advanced data pipelines, MLOps practices, and a FastAPI-based deployment. The system uses MongoDB for data storage and includes comprehensive training and prediction pipelines.

## Overview

This project implements a complete MLOps pipeline for network security threat detection, specifically focused on phishing website identification. It features automated data ingestion from MongoDB, data validation, transformation, model training, and a REST API for real-time predictions.

## Features

- **End-to-End ML Pipeline**: Complete training pipeline from data ingestion to model deployment
- **MongoDB Integration**: Seamless data storage and retrieval from MongoDB Atlas
- **Data Validation**: Schema-based validation using YAML configuration
- **Model Training**: Automated model training with artifact versioning
- **FastAPI REST API**: Production-ready API for training and predictions
- **Docker Support**: Containerized deployment for easy scaling
- **MLOps Integration**: MLflow tracking and experiment management
- **Real-time Predictions**: Upload CSV files for batch predictions with HTML visualization
- **Cloud Ready**: AWS ECR/EC2 deployment with CI/CD

## Project Structure

```
networksecurity/
├── app.py                          # FastAPI application
├── main.py                         # Training pipeline entry point
├── push_data.py                    # MongoDB data ingestion utility
├── test_mongodb.py                 # MongoDB connection testing
├── Dockerfile                      # Container configuration
├── requirements.txt                # Python dependencies
├── setup.py                        # Package setup configuration
├── README.md                       # This file
│
├── networksecurity/               # Main package
│   ├── components/                # Pipeline components
│   │   ├── data_ingestion.py     # Data collection from MongoDB
│   │   ├── data_validation.py    # Data quality checks
│   │   ├── data_transformation.py # Feature engineering
│   │   └── model_trainer.py      # Model training logic
│   │
│   ├── pipeline/                  # Training and prediction pipelines
│   ├── entity/                    # Configuration and artifact entities
│   ├── constant/                  # Project constants
│   ├── exception/                 # Custom exception handling
│   ├── logging/                   # Logging configuration
│   ├── utils/                     # Utility functions
│   └── cloud/                     # Cloud storage utilities (S3)
│
├── Artifacts/                     # Training artifacts (timestamped)
│   └── [timestamp]/
│       ├── data_ingestion/
│       ├── data_validation/
│       └── data_transformation/
│
├── data_schema/                   # Data validation schemas
│   └── schema.yaml
│
├── final_model/                   # Production models
│   ├── model.pkl                 # Trained model
│   └── preprocessor.pkl          # Data preprocessor
│
├── Network_Data/                  # Training data
│   └── phisingData.csv
│
├── prediction_output/             # Prediction results
│   └── output.csv
│
├── templates/                     # HTML templates
│   └── table.html
│
└── logs/                          # Application logs
```

## Requirements

- Python 3.10+
- MongoDB Atlas account (or local MongoDB instance)
- AWS CLI (for cloud deployment)

### Python Dependencies

- FastAPI & Uvicorn (API framework)
- PyMongo (MongoDB driver)
- Pandas & NumPy (Data processing)
- Scikit-learn (Machine learning)
- MLflow (Experiment tracking)
- DagHub (MLOps platform)
- Python-dotenv (Environment management)
- Certifi (SSL certificates)

## Installation

1. Clone or download this repository

2. Navigate to the project directory:
```bash
cd networksecurity
```

3. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

4. Install dependencies:
```bash
pip install -r requirements.txt
```

5. Install the package in development mode:
```bash
pip install -e .
```

## Configuration

### Environment Variables

Create a `.env` file in the project root with the following variables:

```env
MONGO_DB_URL=mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority
MONGODB_URL_KEY=mongodb+srv://username:password@cluster.mongodb.net/?retryWrites=true&w=majority
```

### GitHub Secrets (For CI/CD)

Configure the following secrets in your GitHub repository:

```
AWS_ACCESS_KEY_ID=<your-access-key>
AWS_SECRET_ACCESS_KEY=<your-secret-key>
AWS_REGION=us-east-1
AWS_ECR_LOGIN_URI=788614365622.dkr.ecr.us-east-1.amazonaws.com/networkssecurity
ECR_REPOSITORY_NAME=networkssecurity
```

### Data Schema

Configure data validation rules in `data_schema/schema.yaml` to define expected columns, data types, and constraints.

## Usage

### 1. Push Data to MongoDB (Optional)

If you have local CSV data to upload to MongoDB:

```bash
python push_data.py
```

### 2. Train the Model

Run the training pipeline:

```bash
python main.py
```

This will:
- Ingest data from MongoDB
- Validate data against schema
- Transform and engineer features
- Train the model
- Save artifacts with timestamps

### 3. Start the FastAPI Application

```bash
python app.py
```

Or using uvicorn directly:
```bash
uvicorn app:app --host 0.0.0.0 --port 8000
```

The API will be available at `http://localhost:8000`

### 4. API Endpoints

#### Interactive Documentation
- **Swagger UI**: `http://localhost:8000/docs`
- **ReDoc**: `http://localhost:8000/redoc`

#### Train Model
```bash
GET /train
```
Triggers the training pipeline and trains a new model.

#### Make Predictions
```bash
POST /predict
```
Upload a CSV file to get predictions. Returns an HTML table with results.

Example using cURL:
```bash
curl -X POST "http://localhost:8000/predict" \
  -H "accept: application/json" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@test.csv"
```

## Docker Deployment

### Local Docker

Build the Docker image:
```bash
docker build -t networksecurity:latest .
```

Run the container:
```bash
docker run -p 8000:8000 --env-file .env networksecurity:latest
```

### AWS EC2 Deployment

#### Docker Setup in EC2

Connect to your EC2 instance and run:

```bash
# Optional - Update system
sudo apt-get update -y
sudo apt-get upgrade

# Required - Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu
newgrp docker
```

#### Deploy Application

1. Pull the image from ECR:
```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 788614365622.dkr.ecr.us-east-1.amazonaws.com
docker pull 788614365622.dkr.ecr.us-east-1.amazonaws.com/networkssecurity:latest
```

2. Run the container:
```bash
docker run -d -p 8000:8000 --env-file .env 788614365622.dkr.ecr.us-east-1.amazonaws.com/networkssecurity:latest
```

## Project Workflow

### Training Pipeline

1. **Data Ingestion**: 
   - Connects to MongoDB
   - Exports collection as DataFrame
   - Splits into train/test sets
   - Saves to feature store

2. **Data Validation**: 
   - Validates against schema
   - Checks for data drift
   - Generates validation reports

3. **Data Transformation**: 
   - Handles missing values
   - Encodes categorical variables
   - Scales numerical features
   - Creates preprocessor object

4. **Model Training**: 
   - Trains ML model
   - Evaluates performance
   - Saves model artifacts
   - Logs metrics to MLflow

### Prediction Pipeline

1. Upload CSV file through API
2. Load preprocessor and model
3. Transform input data
4. Generate predictions
5. Return results as HTML table

## Model Artifacts

All training runs are versioned with timestamps in the `Artifacts/` directory:

```
Artifacts/
└── MM_DD_YYYY_HH_MM_SS/
    ├── data_ingestion/
    │   ├── feature_store/
    │   ├── train.csv
    │   └── test.csv
    ├── data_validation/
    │   └── validation_report.json
    └── data_transformation/
        ├── transformed_train.npy
        └── transformed_test.npy
```

## Logging

Logs are stored in the `logs/` directory with detailed information about:
- Data ingestion process
- Validation results
- Transformation steps
- Model training progress
- API requests and responses

## Exception Handling

The project uses custom exception handling (`NetworkSecurityException`) for:
- Consistent error messages
- Detailed stack traces
- Proper error propagation
- Easy debugging

## Testing

Test MongoDB connection:
```bash
python test_mongodb.py
```

## Cloud Integration

The project includes AWS integration for:
- **ECR**: Docker image registry
- **EC2**: Application hosting
- **S3**: Model and data storage (optional)

Configure AWS credentials and use the cloud utilities in `networksecurity/cloud/`.

## MLOps Features

- **Experiment Tracking**: MLflow integration for tracking experiments
- **Model Versioning**: Timestamped artifacts for reproducibility
- **Pipeline Automation**: Modular components for easy maintenance
- **API Documentation**: Auto-generated Swagger/OpenAPI docs
- **Containerization**: Docker support for consistent deployments
- **CI/CD Ready**: GitHub Actions compatible

## Future Enhancements

- Model registry integration
- A/B testing framework
- Real-time monitoring dashboard
- Automated model retraining
- Multi-model ensemble support
- Enhanced feature engineering
- Advanced anomaly detection
- Kubernetes orchestration

## Troubleshooting

### MongoDB Connection Issues
- Verify `.env` file contains correct MongoDB URL
- Check network connectivity
- Ensure IP whitelist in MongoDB Atlas

### Module Import Errors
- Run `pip install -e .` to install the package
- Verify virtual environment is activated

### Model Loading Errors
- Ensure model files exist in `final_model/` directory
- Check file permissions
- Verify model was trained successfully

### Docker Issues
- Ensure Docker daemon is running
- Check port 8000 is not already in use
- Verify environment variables are passed correctly

## Contributing

This project follows standard Python development practices:
- PEP 8 style guide
- Type hints where applicable
- Comprehensive logging
- Modular architecture

## License

This project is for educational and research purposes.

## Acknowledgments

- Dataset: Network phishing data
- Framework: FastAPI, Scikit-learn
- Database: MongoDB
- MLOps: MLflow, DagHub
- Cloud: AWS ECR/EC2
- Author: Krish Naik

## Contact

For questions or issues, please refer to the project repository or contact the development team.
