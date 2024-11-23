# Microsoft-Cyber-Security-Incident-Grade-Classification

**Objective**

This project develops machine learning and deep learning models to classify Microsoft cybersecurity incidents into severity grades. By leveraging data preprocessing, traditional models, and deep learning architectures, the system achieves 81.18% accuracy using LightGBM and delivers actionable insights to prioritize incident responses effectively.

**Dataset**

**Feature Description**
The dataset contains a variety of features describing cybersecurity incidents, their attributes, and metadata. Below are the features:

**Id:** Unique ID for each OrgId-IncidentId pair.
**IncidentId:** Organizationally unique incident identifier.
**AlertId:** Unique identifier for an alert.
**Timestamp:** Time the alert was created.
**DetectorId:** Unique ID for the alert-generating detector.
**AlertTitle:** Title of the alert.
**Category:** Category of the alert.
**MitreTechniques:** MITRE ATT&CK techniques involved in the alert.
**IncidentGrade:** SOC grade assigned to the incident (Target Variable).
**ActionGrouped:** SOC alert remediation action (high level).
**ActionGranular:** SOC alert remediation action (fine-grain).
**EntityType:** Type of entity involved in the alert.
**EvidenceRole:** Role of the evidence in the investigation.
**RolesAdditional:** Metadata on evidence role in the alert.
**DeviceId:** Unique identifier for the device.
**DeviceName:** Name of the device.
**Sha256:** SHA-256 hash of the file.
**IpAddress:** IP address involved.
**Url:** URL involved.
**AccountSid:** On-premises account identifier.
**AccountUpn:** Email account identifier.
**AccountObjectId:** Entra ID account identifier.
**AccountName:** Name of the on-premises account.
**NetworkMessageId:** Org-level identifier for the email message.
**EmailClusterId:** Unique identifier for the email cluster.
**RegistryKey:** Registry key involved.
**RegistryValueName:** Name of the registry value.
**RegistryValueData:** Data of the registry value.
**ApplicationId:** Unique identifier for the application.
**ApplicationName:** Name of the application.
**OAuthApplicationId:** OAuth application identifier.
**ThreatFamily:** Malware family associated with a file.
**FileName:** Name of the file.
**FolderPath:** Path of the file folder.
**ResourceIdName:** Name of the Azure resource.
**ResourceType:** Type of Azure resource.
**OSFamily:** Family of the operating system.
**OSVersion:** Version of the operating system.
**AntispamDirection:** Direction of the antispam filter.
**SuspicionLevel:** Level of suspicion.
**LastVerdict:** Final verdict of threat analysis.
**CountryCode:** Country code where evidence appears.
**State:** State where evidence appears.
**City:** City where evidence appears.

**Detailed Workflow**

**A. Data Cleaning and Preprocessing**

**1. Null Value Treatment:**
   
Columns with >90% Null Values: Dropped to reduce dimensionality and noise.

Target Column (IncidentGrade): Analyzed rows with missing values and imputed them based on probabilistic patterns derived from other features:

**2. Removing Redundant Columns:**
   
Purely identifier columns (e.g., Id, IncidentId, AlertId) were removed to avoid data leakage.

**3. Removing Duplicates:**
   
Identified and removed duplicate columns and records to ensure dataset integrity.

**4. Data Type Analysis**
   
Converted all features into numerical formats to support machine learning models.

**5. Feature Engineering**
   
**Timestamp Features:** Extracted temporal attributes (hour, day, week, month, year) from Timestamp.
**AlertTitle Mapping:** Computed proportions of IncidentGrade categories associated with specific alert titles.
**Location Risk Score:** Created a risk score for CountryCode, State, and City based on their relationship with IncidentGrade.
**Filename, FolderPath, DeviceId:** Calculated proportions of IncidentGrade classes for each feature and derived risk metrics for IncidentGrade and SuspicionLevel.
**SHA256 Hash Frequency:** Normalized frequency of Sha256 values to identify commonly affected hashes.

**6. Encoding Techniques**
   
**One-Hot Encoding:** Applied to categorical features such as Category, OAuthApplicationId, OSFamily, SuspicionLevel, and LastVerdict.

**Target Variable Mapping:** Mapped IncidentGrade to numerical values: 

'FalsePositive': 0,   # Not a real threat
'BenignPositive': 1,  # Real but not malicious
'TruePositive': 2     # Real and malicious

**Target Encoding:** Encoded high cardinality categorical features (e.g., EntityType, OSVersion, RegistryKey, RegistryValueName, RegistryValueData, ApplicationName) using target mean encoding.

**Binary Encoding:** Applied to binary categorical features such as EvidenceRole.

**B. Exploratory Data Analysis (EDA)**

**Statistical Analysis:**
Summarized feature distributions to understand their impact on severity grades.

**Correlation Analysis:**
Identified relationships between features like Detection_Time and Severity_Grade.

**Visualization:**
Heatmaps, bar plots, and box plots were used to illustrate class distributions and feature trends.

**Outlier Analysis:**
Outliers are found using box plots and their relevancy is studied.

**C. Model Selection and Experimentation**

**Traditional Machine Learning Models**

**Logistic Regression:** Baseline linear model for classification tasks.
**Random Forest:** Ensemble method leveraging decision trees for higher accuracy.
**XGBoost and Gradient Boosting:** Gradient-boosting models for efficient and high-performing classification.
**LightGBM:** Optimized gradient boosting for large-scale datasets, achieving the best performance.

**Deep Learning Models**

**Basic Neural Network:** Three-layer architecture with ReLU activation and softmax output.
**Enhanced Neural Network:** Introduced dropout layers and weight regularization for better generalization.
**Stacked Model:** Combined Neural Network output with Random Forest as a meta-classifier.

**D. Imbalance Handling**

To address the inherent imbalance in severity grade classes, the following techniques were applied:

**Class Weights:** Adjusted model training penalties for misclassifications in minority classes.
**Oversampling:** Used SMOTE to generate synthetic samples for underrepresented classes.
**Undersampling:** Reduced majority class samples to balance the dataset.

**E. Model Testing and Tuning**

Each model was tested with the test dataset and under different sampling techniques:

**Logistic Regression:**

Baseline performance with class weights showed balanced recall and precision.

**Random Forest:**

Best performance with undersampling, achieving 80.79% accuracy.

**XGBoost:**

High precision but suffered from poor recall with SMOTE.

**Gradient Boosting:**

Balanced performance using undersampling, achieving consistent F1-scores.

**LightGBM:**

Achieved 81.18% accuracy with excellent balance across all metrics.

**Deep Learning Models**

**Basic Neural Network:**

Achieved 77% accuracy with a simple three-layer architecture.

**Enhanced Neural Network:**

Improved performance to 79% with dropout and weight regularization.

**Stacked Model:**

Combined the strengths of Neural Networks and Random Forest, achieving 80% accuracy with balanced metrics.

![Screenshot (275)](https://github.com/user-attachments/assets/c79fb643-b9f5-4b9f-8df1-a1d3cb814d05)

**Key Findings**

LightGBM was the best-performing model, offering the highest accuracy and well-balanced metrics.

Random Forest with undersampling provided a robust alternative.

Deep learning approaches required significant tuning but underperformed traditional models in this context.

SMOTE improved recall for minority classes but negatively impacted overall accuracy in most cases.

**Conclusion**

This project successfully classified cybersecurity incidents into severity grades with high accuracy and balanced performance. LightGBM emerged as the optimal model, demonstrating superior handling of class imbalance and overall reliability.

The developed model and Streamlit application provide a practical solution for Microsoft to prioritize cybersecurity incidents effectively and reduce response times.

**Future Scope:**

Explore Transformer-based architectures for better feature extraction and classification.

Test the model on larger, real-world datasets for enhanced generalization.

Integrate real-time detection and classification in cybersecurity workflows.

**BEST MODEL RESULTS: LIGHTGBM Hypertuned**

![Screenshot (269)](https://github.com/user-attachments/assets/bcd9f818-e5b7-40a8-8111-9a0d97b6f129)

![Screenshot (267)](https://github.com/user-attachments/assets/9a121122-3962-4f35-8cc7-122a511b9bb5)

![Screenshot (268)](https://github.com/user-attachments/assets/350738c2-c260-4c2f-b30f-cf2012fffaa7)

![Screenshot (270)](https://github.com/user-attachments/assets/cfec3623-2c68-4755-b2ce-3df231c5a41c)

![Screenshot (271)](https://github.com/user-attachments/assets/4cb35955-edfb-457e-9104-435c3edad1de)



