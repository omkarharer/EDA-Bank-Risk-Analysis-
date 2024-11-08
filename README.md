
# EDA (BANK RISK ANALYSIS )

This project aims to identify indicators of loan default risk, enabling informed actions to manage lending decisions and reduce financial risk through exploratory data analysis (EDA).






## Business Context

 - Industry : Banking Domain 
 - Objective : The company wants to identify key factors that strongly indicate loan default to improve portfolio management and risk assessment.
 - Stakeholders : risk, portfolio, credit, finance, senior management, compliance, data, and customer service teams, all of whom rely on insights into default risk factors for strategic decision-making and financial stability.

## Objective of Data Analysis
 - Key Objective : The objective of this case study is to identify patterns indicating potential loan default to guide decisions like denying, reducing, or adjusting loan terms, ensuring reliable customers are approved.

 
## Data Analysis Approach

- Data Cleaning: Data Cleaning involves handling missing values by using methods like mean, median, or mode imputation, and addressing outliers or anomalies to ensure data consistency and accuracy.

```bash
# Set the cutoff value for the percentage of missing values
cutoff = 30

# Create a list of column names where the percentage of missing values is greater than the cutoff
cols = list(app.columns[app.isnull().mean() * 100 > cutoff])

# Display the list of columns
cols

# Drop the columns specified in the 'cols' list from the DataFrame 'app'
app = app.drop(cols, axis=1)

# List of irrelevant columns to be dropped from the dataframe
irrelevant_columns = [
    'FLAG_MOBIL', 'FLAG_EMP_PHONE', 'FLAG_WORK_PHONE', 'FLAG_CONT_MOBILE', 'FLAG_PHONE', 'FLAG_EMAIL',  # Binary flags
    'FLAG_DOCUMENT_2', 'FLAG_DOCUMENT_3', 'FLAG_DOCUMENT_4', 'FLAG_DOCUMENT_5', 'FLAG_DOCUMENT_6', 
    'FLAG_DOCUMENT_7', 'FLAG_DOCUMENT_8', 'FLAG_DOCUMENT_9', 'FLAG_DOCUMENT_10', 'FLAG_DOCUMENT_11', 
    'FLAG_DOCUMENT_12', 'FLAG_DOCUMENT_13', 'FLAG_DOCUMENT_14', 'FLAG_DOCUMENT_15', 'FLAG_DOCUMENT_16', 
    'FLAG_DOCUMENT_17', 'FLAG_DOCUMENT_18', 'FLAG_DOCUMENT_19', 'FLAG_DOCUMENT_20', 'FLAG_DOCUMENT_21'  # Document flags
]

# Dropping the irrelevant columns from the dataframe
app.drop(columns=irrelevant_columns, inplace=True)

app['AMT_REQ_CREDIT_BUREAU_MON'].median()

# List of columns to replace NaN values with their median
selected_columns = ['AMT_REQ_CREDIT_BUREAU_QRT', 'AMT_REQ_CREDIT_BUREAU_HOUR', 'AMT_REQ_CREDIT_BUREAU_DAY', 'AMT_REQ_CREDIT_BUREAU_WEEK', 'AMT_REQ_CREDIT_BUREAU_MON', 'AMT_REQ_CREDIT_BUREAU_YEAR', 'EXT_SOURCE_3']

# Replace NaN values with the median values for each column
for col in selected_columns:
    median_value = app[col].median()
    app[col].fillna(median_value, inplace=True)

# Calculate the mode of the NAME_TYPE_SUITE column
mode_value = app['NAME_TYPE_SUITE'].mode()[0]

# Fill the missing values in the NAME_TYPE_SUITE column with the mode
app['NAME_TYPE_SUITE'].fillna(mode_value, inplace=True)

```

- Feature Engineering : Created new columns (e.g., converting birth dates to age), merged with an additional dataset for improved insights, and saved the final dataset in CSV format for further analysis.

```bash
# Calculate age in years from the 'DAYS_BIRTH' column and store it in a new column 'AGE'
appn['AGE'] = (-appn['DAYS_BIRTH'] // 365.25).astype(int)

# Print the 'AGE' column to display the calculated ages
print(appn['AGE'])

# Display the first 3 rows of the dataframe to verify the changes
print(appn.head(3))

appn.to_csv('After_EDA_APP.csv', index=False)

```
- Exploratory Data Analysis (EDA) : Conducted univariate, segmented univariate, bivariate, and multivariate analyses to uncover key insights and created a correlation matrix.

```bash
cat_cols=[]
num_cols=[]
for i in application.columns:
    if application[i].nunique()>30:
        num_cols.append(i)
        
    else:
        cat_cols.append(i)

for col in num_cols:
    plt.figure(figsize=(10, 5))
    sns.boxplot(x=application[col])
    plt.title(f'Distribution of {col}')
    plt.show()
    # plt.savefig(f'Distribution of {col}')

# Select only numeric columns
numeric_columns = application.select_dtypes(include=['number']).columns

# Filter datasets to include only numeric columns
defaulter_numeric = defaulter[numeric_columns]
non_defaulter_numeric = non_defaulter[numeric_columns]

# Calculate correlation matrices
corr_defaulter = defaulter_numeric.corr()
corr_non_defaulter = non_defaulter_numeric.corr()

# Create subplots
fig, axes = plt.subplots(2, 1, figsize=(16, 24))

# Set the main title
fig.suptitle('Correlation Matrices', fontsize=20)

# Heatmap for defaulters
sns.heatmap(corr_defaulter, annot=True, fmt='.2f', cmap='coolwarm', cbar=False, ax=axes[0])
axes[0].set_title('Defaulters')

# Heatmap for non-defaulters
sns.heatmap(corr_non_defaulter, annot=True, fmt='.2f', cmap='coolwarm', cbar=False, ax=axes[1])
axes[1].set_title('Non-Defaulters')

# Adjust layout
plt.tight_layout(rect=[0, 0, 1, 0.95])

# Display the plot
plt.show()

```


## Conclusion

In conclusion, our analysis reveals key patterns to improve loan approval and repayment outcomes. Cash loans are generally repaid more reliably, particularly by working-class clients, females, homeowners, and clients without cars. Clients in the 20-40 age range, living in densely populated areas, or with higher education are also good repayment candidates. Higher loan amounts (250K-500K) correlate with better repayment when aligned with goods values. For larger loans, target males, clients with cars, and those with income above 500K. Certain business sectors (e.g., legal services) show favorable repayment behavior for larger loans, while clients in co-op apartments or low-income brackets tend to default more often. Prioritizing loan purposes such as home or car purchases for high-value loans and focusing on new or refreshed clients can further enhance repayment rates, driving sustainable growth and reducing loan defaults.

