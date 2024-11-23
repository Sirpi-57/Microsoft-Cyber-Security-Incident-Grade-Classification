# **Data Preprocessing Documentation**

## **1\. Introduction**

The preprocessing phase ensures the data is cleaned, structured, and prepared for analysis or machine learning workflows. Below are the detailed steps, along with their purposes and implementations.

---

## **2\. Data Import**

### **2.1 Importing Libraries**

The following Python libraries were imported to handle the data preprocessing:

* **pandas**: For data manipulation and cleaning.  
* **numpy**: For numerical operations.  
* **matplotlib & seaborn**: For data visualization (though not explicitly used in the shared code).

### **2.2 Loading Large Datasets in Chunks**

**Reason:**  
Given the large size of the dataset (`9,516,837 rows x 45 columns`), it was loaded in chunks to handle memory constraints efficiently.

**Process:**

* Used the `chunksize` parameter of `pd.read_csv()` to load smaller parts of the dataset iteratively.  
* Concatenated all chunks into a single DataFrame.

---

## **3\. Exploratory Steps**

### **3.1 Shape of the Dataset**

The dataset initially contained **9,516,837 rows and 45 columns**.

---

## **4\. Duplicate Handling**

### **4.1 Identifying Duplicate Rows**

* **Purpose:** To remove redundant information and ensure data consistency.  
* **Result:** Identified **22,559 duplicate rows**.

### **4.2 Removing Duplicates**

* Removed duplicate rows using `df.drop_duplicates()`.  
* Verified the updated dataset, ensuring all duplicates were successfully removed.

---

## **5\. Sampling for Analysis**

### **5.1 Purpose of Sampling**

Given the large size of the dataset, a sample of **500,000 rows** was extracted for analysis and training to reduce computational costs.

### **5.2 Sampling Method**

* Used the `sample()` method with `random_state=42` for reproducibility.

---

## **6\. Understanding Columns**

### **6.1 Column Description**

The dataset originally consisted of **45 columns**. Below are some key features:

#### **Id \- Unique ID for each OrgId-IncidentId pair**

#### **IncidentId \- Organizationally unique incident identifier**

#### **AlertId \- Unique identifier for an alert**

#### **Timestamp \- Time the alert was created**

#### **DetectorId \- Unique ID for the alert generating detector**

#### **AlertTitle \- Title of the alert**

#### **Category \- Category of the alert**

#### **MitreTechniques \- MITRE ATT\&CK techniques involved in alert**

#### **IncidentGrade \- SOC grade assigned to the incident**

#### **ActionGrouped \- SOC alert remediation action (high level)**

#### **ActionGranular \- SOC alert remediation action (fine-grain)**

#### **EntityType \- Type of entity involved in the alert**

#### **EvidenceRole \- Role of the evidence in the investigation**

#### **Roles Additional \- metadata on evidence role in alert**

#### **DeviceId \- Unique identifier for the device**

#### **DeviceName \- Name of the device**

#### **Sha256 \- SHA-256 hash of the file**

#### **IpAddress \- IP address involved**

#### **Url \- URL involved**

#### **AccountSid \- On-premises account identifier**

#### **AccountUpn \- Email account identifier**

#### **AccountObjectId \- Entra ID account identifier**

#### **AccountName \- Name of the on-premises account**

#### **NetworkMessageId \- Org-level identifier for email message**

#### **EmailClusterId \- Unique identifier for the email cluster**

#### **RegistryKey \- Registry key involved**

#### **RegistryValueName \- Name of the registry value**

#### **RegistryValueData \- Data of the registry value**

#### **ApplicationId \- Unique identifier for the application**

#### **ApplicationName \- Name of the application**

#### **OAuthApplicationId \- OAuth application identifier**

#### **ThreatFamily \- Malware family associated with a file**

#### **FileName \- Name of the file**

#### **FolderPath \- Path of the file folder**

#### **ResourceIdName \- Name of the Azure resource**

#### **ResourceType \- Type of Azure resource**

#### **OSFamily \- Family of the operating system**

#### **OSVersion \- Version of the operating system**

#### **AntispamDirection \- Direction of the antispam filter**

#### **SuspicionLevel \- Level of suspicion**

#### **LastVerdict \- Final verdict of threat analysis**

#### **CountryCode \- Country code evidence appears in**

#### **State \- State of evidence appears in**

#### **City \- City evidence appears in[¶](http://localhost:8888/notebooks/Desktop/Cybersecurity%20-%20Microsoft/Data%20PreProcessing.ipynb#City---City-evidence-appears-in)**

---

## **7\. Null Value Analysis**

### **7.1 Summary of Null Values**

Performed a null value analysis using `df.isnull().sum()`. Below are the key observations:

* Certain columns, like `MitreTechniques` and `ActionGrouped`, had a **high percentage of null values**.

|  Column Name |  Null Count |  Comments |
| :---- | :---- | :---- |
| `MitreTechniques` | 286,995 | High percentage of null values (dropped later). |
| `IncidentGrade` | 2,678 | Key target variable with minimal missing values. |

---

## **8\. Handling Columns with High Null Values**

### **8.1 Dropping Columns with ≥90% Null Values**

**Purpose:** Columns with a high proportion of missing data provide minimal information and can be dropped.

**Dropped Columns:**

| Column Name | Reason for Dropping |
| :---- | :---- |
| `ActionGrouped` | \>90% missing values. |
| `MitreTechniques` | \>90% missing values. |

---

## **9\. Target Variable (`IncidentGrade`) Analysis**

### **9.1 Observing Null Values in `IncidentGrade`**

**Steps Taken:**

1. Analyzed rows where `IncidentGrade` was null and summarized patterns in associated features (`SuspicionLevel`, `LastVerdict`, etc.).  
2. Observed key trends:  
   * Null rows shared characteristics with `BenignPositive` and `FalsePositive` categories.

### **9.2 Assigning Null Values in `IncidentGrade`**

**Approach:**

* Used probabilistic patterns derived from the dataset to impute missing values:  
  * Rows with `SuspicionLevel = Suspicious` and `Category = Exfiltration` were assigned to `BenignPositive`.  
  * Rows with `SuspicionLevel = Suspicious` and `LastVerdict = NoThreatsFound` were assigned to `FalsePositive`.

**Result:**  
Null values in `IncidentGrade` were imputed, ensuring a complete target variable.

---

## **10\. Handling Other Null Values**

### **10.1 Filling Null Values**

* Replaced missing values in `SuspicionLevel` with "Unknown".  
* Replaced missing values in `LastVerdict` with "No Verdict".

### **10.2 Removing Invalid Values**

**Steps Taken:**

* Excluded rows where `LastVerdict` contained invalid values such as `DomainPII_50d8b4a941c...`.

**Result:**  
Cleaned dataset with all null and invalid values addressed.

---

## **11\. Final Dataset**

### **11.1 Final Data Summary**

* **Number of Rows:** 497,338  
* **Number of Columns:** 37

### **11.2 Value Counts for Key Features**

* `IncidentGrade`:  
  * `BenignPositive`: 216,825  
  * `TruePositive`: 173,525  
  * `FalsePositive`: 106,988

## **12\. Data Transformation and Feature Engineering**

## **Dropping Pure Identifier Columns**

To remove columns that serve as identifiers and do not contribute to meaningful feature extraction, the following columns were dropped:

python  
Copy code  
`df.drop(columns=[`  
    `'Id', 'OrgId', 'IncidentId', 'AlertId', 'DetectorId', 'IpAddress',`   
    `'Url', 'AccountSid', 'AccountUpn', 'AccountObjectId', 'AccountName',`  
    `'NetworkMessageId', 'ApplicationId'`  
`], inplace=True)`

This ensures the focus remains on actionable and meaningful features for modeling.

---

## **13.Feature Engineering**

### **Timestamp Analysis**

The `Timestamp` column was converted into several derived time-related features:

1. **Basic time features:**  
   * `hour`: Extracts the hour of the event.  
   * `day`: Extracts the day of the month.  
   * `weekday`: Identifies the day of the week (0 for Monday, 6 for Sunday).  
   * `month`: Extracts the month.  
   * `year`: Extracts the year.  
2. **Derived features:**  
   * `is_weekend`: Identifies if the event occurred on a weekend (1 for Saturday/Sunday, 0 otherwise).  
   * `is_business_hours`: Indicates whether the event occurred during business hours (9 AM to 5 PM, weekdays).  
3. **Time periods:**  
   * Categorized the `hour` into bins representing periods of the day:  
     * Night (0–6 hours)  
     * Morning (6–12 hours)  
     * Afternoon (12–18 hours)  
     * Evening (18–24 hours)

Finally, the original `Timestamp` column was dropped:

python  
Copy code  
`df.drop(columns='Timestamp', inplace=True)`

---

### **AlertTitle Transformation**

To enhance the granularity of the `AlertTitle` feature, the proportions of `IncidentGrade` classes (`TruePositive`, `FalsePositive`, `BenignPositive`) for each alert title were calculated and mapped back to the original dataset. The `AlertTitle` column was subsequently dropped:

python  
Copy code  
`df.drop(columns='AlertTitle', inplace=True)`

---

### **Location Feature Engineering**

Created a composite location hierarchy combining `CountryCode`, `State`, and `City`:  
python  
Copy code  
`df['location'] = df['CountryCode'] + '_' + df['State'] + '_' + df['City']`

1.   
2. Calculated `IncidentGrade` proportions for each location and mapped them back to the dataset as:  
   * `Loc_TruePositiveProportion`  
   * `Loc_FalsePositiveProportion`  
   * `Loc_BenignPositiveProportion`

Computed a **location risk score** using inverse class weights:  
python  
Copy code  
`df['location_risk_score'] = df['location'].apply(lambda x: sum(location_severity.loc[x] * class_weights.values()))`

3. 

Dropped the original location-related columns:  
python  
Copy code  
`df.drop(columns=['CountryCode', 'State', 'City'], inplace=True)`

4. 

---

### **File and Device Risk Metrics**

The `FileName`, `FolderPath`, and `DeviceId` columns were used to calculate risk metrics based on `IncidentGrade` and `SuspicionLevel`:

* **Proportions of `IncidentGrade` classes** for each unique file, folder, and device:  
  * `*_true_positive`, `*_false_positive`, `*_benign_positive`  
* **Proportions of `SuspicionLevel` classes** for each unique file, folder, and device:  
  * `*_suspicious`, `*_incriminated`, `*_suspicion_unknown`

Finally, the original columns (`FileName`, `FolderPath`, `DeviceId`, `DeviceName`) were dropped:

python  
Copy code  
`df.drop(columns=['FileName', 'FolderPath', 'DeviceId', 'DeviceName'], inplace=True)`

---

### **Categorical Encoding**

#### **One-Hot Encoding**

Performed one-hot encoding for categorical columns with manageable cardinality:

python  
Copy code  
`df = pd.get_dummies(df, columns=['Category', 'OAuthApplicationId', 'OSFamily', 'SuspicionLevel', 'LastVerdict'])`

#### **Target Encoding**

For high-cardinality categorical columns, target encoding was applied to incorporate information from the target variable (`IncidentGrade`):

python  
Copy code  
`target_encoder = ce.TargetEncoder(cols=['EntityType', 'OSVersion', 'RegistryKey', 'RegistryValueName', 'RegistryValueData', 'ApplicationName'])`  
`df_encoded = target_encoder.fit_transform(df, y)`

#### **Binary Encoding**

For binary categorical features like `EvidenceRole`, binary encoding was used to ensure efficient representation:

python  
Copy code  
`binary_encoder = ce.BinaryEncoder(cols=['EvidenceRole'])`  
`df_binary_encoded = binary_encoder.fit_transform(df)`

---

### **SHA256 Hash Frequency**

The frequency of each unique `Sha256` hash was calculated and mapped back to the dataset as `hash_frequency`. The original `Sha256` column was then dropped.

python  
Copy code  
`df.drop(columns='Sha256', inplace=True)`

---

### **Final Cleanup**

Removed duplicated columns to prevent redundancy:  
python  
Copy code  
`df = df.loc[:, ~df.columns.duplicated()]`

1.   
2. Converted all boolean values to binary (1/0).  
3. Dropped any remaining non-essential columns, including `location` after processing.  
4. The `time_period` column was encoded as integers representing its categories (night, morning, afternoon, evening) and its data type was updated to `int`.

---

### **Data Summary**

After all transformations, the processed dataset contains **147 features**, with a mix of:

* 32 `float64` columns  
* 5 `int32` columns  
* 110 `int64` columns

The total memory usage of the dataset is approximately **552.1 MB**, making it ready for training.

python  
Copy code  
`df.info()`

---

### **Export and Repeat for Test Data**

The processed dataset was saved as `for_EDA_before_encoding.csv`, and the same preprocessing steps were applied to the test data.

---

### **Visualization and Class Weights**

**1.IncidentGrade Distribution**:

* Bar chart and pie chart were used to visualize the class proportions.  
  * Based on the proportions:  
    * `TruePositive`: 34.9%  
    * `FalsePositive`: 21.5%  
    * `BenignPositive`: 43.6%

**2\. Class Weights**: Inverse class proportions were used to compute weights:  
python  
Copy code  
`class_weights = {`  
    `'TruePositive': 2.87,`  
    `'FalsePositive': 4.65,`  
    `'BenignPositive': 2.29`  
`}`

These weights are particularly useful in addressing class imbalance during model training.

## **14\. Conclusion**

The preprocessing steps ensured that the dataset was cleaned, structured, and free of major issues like duplicates, null values, and invalid data. This final processed dataset is now ready for further exploratory data analysis (EDA) or machine learning tasks.

