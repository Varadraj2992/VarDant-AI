# AI-Based Crop Recommendation System

![Python](https://img.shields.io/badge/Python-3.11-blue?logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/UI-Streamlit-FF4B4B?logo=streamlit&logoColor=white)
![scikit-learn](https://img.shields.io/badge/ML-scikit--learn-F7931E?logo=scikitlearn&logoColor=white)
![Firebase](https://img.shields.io/badge/Sensor_data-Firebase-FFCA28?logo=firebase&logoColor=black)

A Streamlit application that recommends a crop from soil nutrients, soil type, and environmental measurements. It uses a saved machine-learning model and encoders. An alternate mode can read temperature and humidity from Firebase when connected to a sensor publisher.

---

## 1. Overview

The Crop Recommendation System provides an interactive way to enter soil and environmental conditions and receive a predicted crop. The project includes manual-input apps, a Firebase-backed app for live temperature and humidity, and a script for publishing Arduino sensor readings.

This is a crop prediction aid based on a trained model. It does not replace local agronomic expertise or guarantee crop yield.

## 2. Key Features

### Manual crop recommendation

- Enter nitrogen (N), phosphorus (P), and potassium (K) values.
- Select a soil type from the saved soil encoder's classes.
- Enter temperature, humidity, and soil pH.
- View the recommended crop.
- View the model's highest predicted class probability when supported.

### Firebase sensor mode

- Fetches temperature and humidity from the configured Firebase Realtime Database endpoint.
- Lets the user provide the remaining soil values in the app.
- Retains the latest available sensor values in Streamlit session state if a later fetch has no data.

### Sensor publishing script

- Reads Arduino serial data and sends temperature and humidity to Firebase.
- Can run alongside the automatic Streamlit app.

## 3. System Flow

### Manual mode

```text
Soil and environment inputs
            ↓
Encode soil type
            ↓
Trained crop prediction model
            ↓
Decode predicted crop label
            ↓
Recommended crop and optional confidence
```

### Sensor-assisted mode

```text
Arduino sensor → firebase_sender.py → Firebase Realtime Database
                                             ↓
                                  app_automatic.py
                                             ↓
                         Model inputs → crop recommendation
```

## 4. Project Structure

```text
2. Crop Recomendation System/
├── app.py                              # Main manual-input Streamlit app
├── app_manual.py                       # Alternate manual-input app
├── app_automatic.py                    # Firebase temperature/humidity app
├── firebase_sender.py                  # Arduino serial-to-Firebase publisher
├── crop_recommendation_model.pkl        # Trained prediction model
├── soil_encoder.pkl                    # Soil type encoder
├── crop_encoder.pkl                    # Crop label decoder
├── Crop recommendation dataset.csv      # Original dataset
├── cleaned_dataset.csv                  # Cleaned dataset
└── README.md
```

The model, encoder, and CSV assets are tracked using Git LFS in this repository. The Python apps require the three `.pkl` assets at runtime; they do not load the CSV files.

## 5. Technology Stack

- **Language:** Python 3.11
- **App interface:** Streamlit
- **Numerical processing:** NumPy
- **Model and asset loading:** scikit-learn-compatible model, joblib
- **Sensor data:** Arduino serial connection, Firebase Realtime Database
- **Dependencies:** repository-level `requirements.txt`

## 6. Setup

Run the following from the repository root.

### 6.1 Download Git LFS assets

```powershell
git lfs install
git lfs pull
```

The model and dataset files must be downloaded as real files, not left as Git LFS pointer text.

### 6.2 Create and activate a virtual environment

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### 6.3 Install dependencies

```powershell
python -m pip install -r requirements.txt
```

The repository-level requirements file installs packages for the broader project, not only this crop recommendation module.

## 7. Run Locally

From the repository root, enter the project folder:

```powershell
Set-Location "2. Crop Recomendation System"
```

### Manual app

```powershell
streamlit run app.py
```

Streamlit prints a local URL, usually `http://localhost:8501`. The alternate manual app can be started with:

```powershell
streamlit run app_manual.py
```

### Firebase-backed app

```powershell
streamlit run app_automatic.py
```

The app reads the `temperature` and `humidity` properties from the Firebase `sensor` object at the endpoint configured in `app_automatic.py`. Update that endpoint in the script if using a different database.

## 8. Connect an Arduino Sensor (Optional)

`firebase_sender.py` currently uses serial port `COM4` at `9600` baud and expects comma-separated sensor output. Before running it:

1. Connect the Arduino and update the serial port in `firebase_sender.py` if needed.
2. Add a Firebase service-account JSON file named `serviceAccountKey.json` to this folder, or change the credential path in the script.
3. Ensure the Firebase database URL configured by the sender matches the endpoint used by `app_automatic.py`.
4. Run the sender from the project folder:

```powershell
python firebase_sender.py
```

The publisher runs continuously; stop it with `Ctrl+C`. Keep the service-account key private and do not commit it to source control.

## 9. Inputs and Model Details

The apps collect the following model features in this order:

1. Encoded soil type
2. Nitrogen (N)
3. Phosphorus (P)
4. Potassium (K)
5. Temperature (°C)
6. Humidity (%)
7. Soil pH

The manual app's input ranges are N/P/K: 0–500, temperature: 0–60 °C, humidity: 0–100%, and pH: 0–14. Soil type options come from `soil_encoder.pkl`. The predicted label is converted to a crop name using `crop_encoder.pkl`.

The saved model and both encoders must come from the same training run. Replacing only one of these files may cause incorrect predictions or runtime errors. The displayed probability, when available, is the model's predicted probability for its most likely class; it is not a guarantee of accuracy in field conditions.

## 10. Project Status

This folder contains a Streamlit-based crop recommendation workflow with manual input and an optional Firebase sensor path. Model training and retraining are not performed by the Streamlit apps; the apps load the supplied serialized model and encoder files.

## 11. Author

**Varad Bhandare**

M.Sc. Data Science & Big Data Analytics

Areas of interest:

- Data Analytics
- Data Science
- Machine Learning
- Precision Agriculture
- Internet of Things (IoT)
- Agricultural Technology

## 12. License

No license has been specified for this project yet. Until a `LICENSE` file is added, reuse, modification, and redistribution are not explicitly granted.

## 13. Disclaimer

This project is intended for educational and demonstration purposes. Recommendations depend on the supplied data and trained model and should be validated against local soil tests, climate conditions, and advice from qualified agricultural professionals.
