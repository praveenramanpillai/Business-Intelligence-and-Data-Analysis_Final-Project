```python
import pandas as pd
import matplotlib.pyplot as plt

# Set File Path
file_path = r'C:\\Users\\prave\\OneDrive\\Desktop\\College\\Course 8 - Capstone_Business Intelligence and Data Analytics\\Data_set\\Medicare_IP_Hospitals_by_Geography_and_Service_2023.csv'  

df = pd.read_csv(file_path, encoding='latin1')

df.columns = df.columns.str.strip()

df.head()

columns = [
    'Rndrng_Prvdr_Geo_Lvl',
    'Rndrng_Prvdr_Geo_Cd',
    'Rndrng_Prvdr_Geo_Desc',
    'DRG_Cd',
    'DRG_Desc',
    'Tot_Dschrgs',
    'Avg_Submtd_Cvrd_Chrg',
    'Avg_Tot_Pymt_Amt',
    'Avg_Mdcr_Pymt_Amt'
]

df = pd.read_csv(file_path, encoding='latin1', names=columns, header=None)


numeric_cols = ['Tot_Dschrgs', 'Avg_Submtd_Cvrd_Chrg', 'Avg_Tot_Pymt_Amt', 'Avg_Mdcr_Pymt_Amt']
df[numeric_cols] = df[numeric_cols].apply(pd.to_numeric, errors='coerce')

```


```python
# Research Question- 4: 
# How do average Medicare payments compare to average submitted charges across different DRGs?

# Convert to numeric
df['Avg_Submtd_Cvrd_Chrg'] = pd.to_numeric(df['Avg_Submtd_Cvrd_Chrg'], errors='coerce')
df['Avg_Mdcr_Pymt_Amt'] = pd.to_numeric(df['Avg_Mdcr_Pymt_Amt'], errors='coerce')
df['Tot_Dschrgs'] = pd.to_numeric(df['Tot_Dschrgs'], errors='coerce')


# Calculate total amounts per DRG category
df['Total_Submitted_Amount'] = df['Tot_Dschrgs'] * df['Avg_Submtd_Cvrd_Chrg']
df['Total_Medicare_Paid_Amount'] = df['Tot_Dschrgs'] * df['Avg_Mdcr_Pymt_Amt']

drg_summary = df.groupby('DRG_Desc').agg({
    'Total_Submitted_Amount': 'sum',
    'Total_Medicare_Paid_Amount': 'sum'
}).sort_values(by='Total_Submitted_Amount', ascending=False)

plt.figure(figsize=(20, 12))

# Plot stacked bars
p1 = plt.bar(drg_summary.index, drg_summary['Total_Submitted_Amount'], label='Total Submitted Amount')
p2 = plt.bar(drg_summary.index, drg_summary['Total_Medicare_Paid_Amount'], 
             bottom=drg_summary['Total_Submitted_Amount'], label='Total Medicare Paid Amount')

plt.xlabel('DRG Category', fontsize=12)
plt.ylabel('Amount ($)', fontsize=12)
plt.title('Total Submitted vs Medicare Paid Amount by DRG Category', fontsize=14)
plt.xticks(rotation=45, ha='right')
plt.legend()

plt.tight_layout()
plt.show()
```


```python

```


```python

```
