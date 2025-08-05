# 🏨 Hotel Booking Cancellation Prediction

This project predicts whether a hotel booking will be **canceled or not**, based on various features like guest info, booking lead time, pricing, and more. It follows an **end-to-end ML workflow**, from data ingestion to CI/CD and deployment on **Google Cloud Run** using **Docker** and **Jenkins**.

---

## 📁 Dataset Overview

The dataset includes hotel booking information with the following sample structure:

| Booking_ID | no_of_adults | no_of_children | no_of_weekend_nights | no_of_week_nights | ... | booking_status |
|------------|---------------|----------------|----------------------|-------------------|-----|----------------|
| INN00001   | 2             | 0              | 1                    | 2                 | ... | Not_Canceled   |
| INN00002   | 2             | 0              | 2                    | 3                 | ... | Not_Canceled   |

### Key Columns

- `lead_time`: Number of days between booking and arrival.
- `avg_price_per_room`: Average cost per room.
- `room_type_reserved`, `type_of_meal_plan`, `market_segment_type`: Categorical booking details.
- `booking_status`: Target variable (Canceled or Not_Canceled).

---

## 🛠️ Workflow Overview

### 🔽 1. **Data Ingestion**
- Data was ingested directly from **Google Cloud Storage (GCS)** using a Python script.
- Bucket: `my_firstbucket789`
- File: `Hotel_Reservations.csv`

### 🧹 2. **Data Preprocessing**
- Handled missing values, outliers, and inconsistent entries.
- Converted categorical columns to numerical via encoding.
- Normalized/Standardized relevant numerical features.

### ⚖️ 3. **Handling Imbalanced Data**
- The target variable `booking_status` was **highly imbalanced**.
- Applied **SMOTE (Synthetic Minority Over-sampling Technique)** to balance the classes.

### 🌲 4. **Feature Selection**
- Used **Random Forest Classifier** to assess feature importance.
- Selected **top 10 features** for model training based on importance scores.

### 🧠 5. **Model Training**
- Final model trained on selected features using classification models.
- Models evaluated using accuracy, F1-score, ROC-AUC, etc.

### 🔁 6. **Experiment Tracking**
- Integrated **MLflow** for tracking:
  - Parameters
  - Metrics
  - Artifacts (e.g., model binaries)
  - Visualizations

---

## 🌐 Flask Prediction UI

- Built a **Flask-based web interface** for users to input booking details and get predictions.
- Deployed locally for testing and feedback.

---

## ⚙️ CI/CD Pipeline with Jenkins

### 🧱 CI Pipeline Includes:
- Linting and code checks
- Unit tests for data and model pipeline
- Triggered on every GitHub push

### 📦 Dockerization
- App was containerized using a Dockerfile.
- Tagged and pushed to **Google Container Registry (GCR)**.

```bash
docker build -t gcr.io/<your-project-id>/ml-project:latest .
docker push gcr.io/<your-project-id>/ml-project:latest
