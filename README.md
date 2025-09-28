# Data Cleaning with AI Support

## Student Information
- Name: Emmanuel Fitz C. Ciano
- Course Year: BSCS 4
- Date: 2025-09-29

## Dataset
- Source: [Kaggle - Titanic: Machine Learning from Disaster](https://www.kaggle.com/c/titanic/data)
- Name: Titanic dataset

## Issues found
- **Missing values**: 
  - Age: 177 missing values (19.87%)
  - Cabin: 687 missing values (77.1%)
  - Embarked: 2 missing values (0.22%)
- **Duplicates**: No exact duplicate rows found
- **Inconsistencies**: 
  - Text formatting in Name, Sex, and Embarked columns
  - No standardized title extraction from names
- **Outliers**: 
  - Fare column contained extreme values that could skew analysis

## Cleaning steps
1. **Missing values**: 
   - Age: Filled with median value (28 years)
   - Cabin: Created new 'Has_Cabin' binary feature and dropped original column
   - Embarked: Filled with mode ('S')
2. **Duplicates**: Checked for and removed any duplicate rows
3. **Inconsistencies**: 
   - Standardized text formatting (lowercase for Sex, uppercase for Embarked)
   - Extracted and standardized titles from Name column
4. **Outliers**: 
   - Detected outliers in Fare using IQR method
   - Capped extreme values instead of removing to preserve data

## AI prompts used
- **Prompt 1**: "Generate Pandas code to handle missing values in Titanic dataset for Age, Cabin, and Embarked columns appropriately"
- **AI Response**: 
  ```python
  # Age: Fill with median (less sensitive to outliers)
  age_median = df['Age'].median()
  df['Age'] = df['Age'].fillna(age_median)
  
  # Cabin: Too many missing, consider dropping or creating 'Has_Cabin' feature
  df['Has_Cabin'] = df['Cabin'].notna().astype(int)
  df = df.drop('Cabin', axis=1)
  
  # Embarked: Fill with mode (most frequent)
  embarked_mode = df['Embarked'].mode()[0]
  df['Embarked'] = df['Embarked'].fillna(embarked_mode)
  ```

- **Prompt 2**: "How can I detect and treat outliers in the Fare column using IQR method without losing data?"
- **AI Response**: 
  ```python
  # IQR method for Fare outliers
  Q1 = df['Fare'].quantile(0.25)
  Q3 = df['Fare'].quantile(0.75)
  IQR = Q3 - Q1
  lower_bound = Q1 - 1.5 * IQR
  upper_bound = Q3 + 1.5 * IQR
  
  # Cap outliers instead of removing to preserve data
  df['Fare'] = np.where(df['Fare'] > upper_bound, upper_bound, df['Fare'])
  df['Fare'] = np.where(df['Fare'] < lower_bound, lower_bound, df['Fare'])
  ```

## Results
- **Rows before**: 891
- **Rows after**: 891 (no rows lost during cleaning)
- **Columns before**: 12
- **Columns after**: 13 (added Has_Cabin and Title features)
- **Missing values**: All resolved (0 missing values in final dataset)
- **Outliers**: 116 Fare outliers detected and capped using IQR method

## Before and After Snapshots

### Dataset Shape
- **Before**: (891, 12)
- **After**: (891, 13)

### Missing Values Summary
- **Before**: Age (177), Cabin (687), Embarked (2)
- **After**: 0 missing values in all columns

### Sample Data Comparison
**Before (Raw Data):**
```
PassengerId,Survived,Pclass,Name,Sex,Age,SibSp,Parch,Ticket,Fare,Cabin,Embarked
1,0,3,"Braund, Mr. Owen Harris",male,22,1,0,A/5 21171,7.25,,S
2,1,1,"Cumings, Mrs. John Bradley (Florence Briggs Thayer)",female,38,1,0,PC 17599,71.2833,C85,C
```

**After (Cleaned Data):**
```
PassengerId,Survived,Pclass,Name,Sex,Age,SibSp,Parch,Ticket,Fare,Embarked,Has_Cabin,Title
1,0,3,"Braund, Mr. Owen Harris",male,22.0,1,0,A/5 21171,7.25,S,0,Mr
2,1,1,"Cumings, Mrs. John Bradley (Florence Briggs Thayer)",female,38.0,1,0,PC 17599,65.6344,C,1,Mrs
```

### Summary Statistics
- **Age**: Mean 29.36, Median 28.0 (missing values filled with median)
- **Fare**: Mean 24.05, Range 0.00-65.63 (outliers capped)
- **Survival Rate**: 38.38%
- **Cabin Availability**: 22.90% had cabin information