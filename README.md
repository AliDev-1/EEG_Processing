# EEG Artifact Removal and Data Preparation Pipeline

This project was developed during my undergraduate studies in Exercise Science at Concordia University. The work was conducted while volunteering in Dr. Thanh Dang-Vu's Sleep Laboratory at the PERFORM Center for Health Research, under the support and guidance of his graduate students.

## Overview

This project focuses on processing and cleaning EEG (Electroencephalography) data stored in .mat files. The primary goal is to prepare high-quality EEG signals by removing noise and artifacts using Independent Component Analysis (ICA). The cleaned data can then be used for further analysis, visualization, or machine learning applications, such as mental state classification or medical diagnostics.

The dataset and all relevant files can be downloaded from [this link](https://1drv.ms/f/c/e9d6730bf89296e9/EsHD1AxS1dVGrGQ1IAN4NqEBZOpbhjY130he-xMpvviJHg?e=OsiLUf).

The dataset and processing methods are based on the research paper:
> Cavanagh, J.F., Rieger, R.E., Wilson, K.W., Gill, D., Fullerton, L., Brandt, E. & Mayer, A.W. (2019) Joint analysis of frontal theta synchrony and white matter following mild traumatic brain injury. Brain Imaging and Behavior

## Features

- Artifact Removal: Identifies and eliminates noise artifacts (e.g., eye movement, muscle activity) using ICA
- Batch Processing: Automatically processes multiple EEG data files, making it scalable for large datasets
- Cleaned Data Storage: Saves the cleaned EEG signals in an organized structure for future use
- Subject Tracking: Associates EEG trials with corresponding subject IDs for better data management
- Ready for Analysis: Prepares the data for advanced analysis or model training

## Data Description

### Subject Identification
- Each participant has two IDs:
  - URSI: Primary identification
  - SubID: Used for file naming conventions

### Available Data Files

1. **Quex.xlsx**: Contains four tabs:
   - Three sessions of questionnaire, demographic, and neuropsychological data
   - Color Key tab indicating missing data coding

2. **Rest_ICAs.xlsx**: 
   - Documents identified blink Independent Components (ICs) for each dataset
   - Includes basic quality check results
   - Note: ICs have not been removed from the raw data

3. **EEG Data Files**:
   - Located in the /Data directory
   - Naming convention: ID_session_Rest.mat
   - Pre-processed using APPLE_3AOB.m pipeline

## Data Processing and Machine Learning Workflow

### 1. Initial Processing
The data has undergone preliminary processing using the APPLE_3AOB.m pipeline:
- Data epoching
- Bad epoch removal
- Channel interpolation (applied to approximately 99% of the sample)
- Initial ICA computation (components not removed)

### 2. Loading EEG Data

- EEG data is loaded from .mat files using the scipy.io.loadmat function
- Key EEG attributes extracted:
  - data: EEG signal matrix (channels × time points × trials)
  - icaweights, icasphere: Matrices for ICA-based artifact removal
  - bad_ICAs: List of bad Independent Components to remove

### 3. Artifact Removal

- Contributions of bad Independent Components (ICs) are identified and removed
- ICA matrices (icaweights and icasphere) are used to reconstruct clean signals
- Blink ICs can be identified using the Rest_ICAs.xlsx file

### 4. Saving Cleaned Data

- Cleaned signals are saved in .mat format in a separate directory (cleaned/) for future analysis

### 5. Data Aggregation

- Cleaned data from multiple files is consolidated into a structured format
- Subject IDs are extracted from file names and linked with their trials

### 6. Preprocessing for Machine Learning

- **Data Reshaping**: 
  - Raw EEG data is transformed from 3D array (channels × time points × trials) to 2D array
  - Each row represents a trial, each column represents a feature
  - Features are flattened from channels and time points
  - Format optimized for machine learning algorithms

- **Data Combination**:
  - Multiple cleaned files are merged
  - Labels are matched to ensure each trial has corresponding class labels

- **Normalization**:
  - Features are standardized using StandardScaler
  - Scaled to mean of 0 and standard deviation of 1
  - Prevents features with different scales from dominating the model

### 7. Machine Learning Pipeline

#### Model Training
- Data split into training (80%) and testing (20%) sets using train_test_split
- Random Forest Classifier implementation:
  - Ensemble learning method using multiple decision trees
  - Robust pattern recognition in EEG data
  - Learns patterns corresponding to provided labels

#### Model Performance
We achieved excellent classification results with our Random Forest model:

```
Accuracy: 0.9849624060150376

Classification Report:
              precision    recall  f1-score   support
           0       1.00      0.97      0.98       268
           1       0.98      1.00      0.99       397
    accuracy                           0.98       665
   macro avg       0.99      0.98      0.98       665
weighted avg       0.99      0.98      0.98       665
```

These results demonstrate:
- Overall accuracy of 98.5%
- Perfect precision for class 0 and near-perfect for class 1
- Excellent balanced performance across both classes
- Robust model performance with substantial support size

#### Model Storage
- Trained Random Forest model saved as .pkl file using joblib
- Enables model reuse without retraining
- Facilitates future predictions

## Dependencies

- Python 3.8 or higher
- Required Python libraries:
  - numpy
  - scipy
  - pandas
  - scikit-learn
  - joblib
  - jupyter

## Usage

The entire data processing and analysis workflow is contained in the Jupyter notebook provided in this repository. To use this project:

1. Download all files from the provided link
2. Set up a Python environment with the required dependencies
3. Open and run the Jupyter notebook
4. Follow the step-by-step instructions within the notebook for:
   - Data loading and cleaning
   - EEG signal processing
   - Machine learning model training and evaluation

## Applications

- Medical Diagnostics: Analyze brain activity for disorders such as epilepsy or sleep apnea
- Mental State Classification: Detect states like rest, focus, or drowsiness
- Brain-Computer Interfaces (BCIs): Use EEG signals for real-time applications like communication or gaming
- Traumatic Brain Injury Research: Investigate effects of mild TBI on brain activity patterns

## Future Enhancements

- Automate feature extraction from cleaned EEG signals
- Add visualization for EEG signals (before and after cleaning)
- Implement machine learning pipelines for EEG data analysis
- Integrate additional neuropsychological data analysis from Quex.xlsx

## References

Cavanagh, J.F., et al. (2019). Joint analysis of frontal theta synchrony and white matter following mild traumatic brain injury. Brain Imaging and Behavior.
