```python
import pandas as pd

#File location
file_path = 'C:\\Users\\prave\\OneDrive\\Desktop\\College\\Course 8 - Capstone_Business Intelligence and Data Analytics\\Data_set\\Medicaid_Managed_Care_Q4_2024.csv'

#Check File path
import os
print(os.path.exists(file_path))

# Read the CSV into a pandas DataFrame
df = pd.read_csv(file_path, encoding='latin1')
columns = [
    'State',
    'County',
    'MCO Name',
    'Service Category',
    'Number of Active Patients',
    'Number of Eligible MCO Patients',
    'Number of Providers',
    'Percent Of Eligible Patients Receiving Services',
    'Number of Services per Active Patient',
    'Number of Active Patients per Provider',
    'Calendar Year',
    'Plan Category'
]

# Preview the first few rows
df.head()
```

    True
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ï»¿State</th>
      <th>County</th>
      <th>MCO Name</th>
      <th>Service Category</th>
      <th>Number of Active Patients</th>
      <th>Number of Eligible MCO Patients</th>
      <th>Number of Providers</th>
      <th>Percent Of Eligible Patients Receiving Services</th>
      <th>Number of Services per Active Patient</th>
      <th>Number of Active Patients per Provider</th>
      <th>Calendar Year</th>
      <th>Plan Category</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Arizona</td>
      <td>Apache</td>
      <td>United Healthcare</td>
      <td>Behavioral Health</td>
      <td>*</td>
      <td>*</td>
      <td>11</td>
      <td>10.47%</td>
      <td>7.0</td>
      <td>*</td>
      <td>2020</td>
      <td>Comprehensive MCO</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Arizona</td>
      <td>Apache</td>
      <td>Mercy Care</td>
      <td>Behavioral Health</td>
      <td>*</td>
      <td>*</td>
      <td>8</td>
      <td>30.56%</td>
      <td>9.0</td>
      <td>*</td>
      <td>2020</td>
      <td>Comprehensive MCO</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Arizona</td>
      <td>Apache</td>
      <td>Banner University Family Care</td>
      <td>Behavioral Health</td>
      <td>*</td>
      <td>*</td>
      <td>7</td>
      <td>5.88%</td>
      <td>1.0</td>
      <td>*</td>
      <td>2020</td>
      <td>Comprehensive MCO</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Arizona</td>
      <td>Apache</td>
      <td>Ltc Dd Des</td>
      <td>Behavioral Health</td>
      <td>67</td>
      <td>107</td>
      <td>17</td>
      <td>62.62%</td>
      <td>371.0</td>
      <td>4:01</td>
      <td>2020</td>
      <td>Comprehensive MCO</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Arizona</td>
      <td>Apache</td>
      <td>Az Complete Health</td>
      <td>Behavioral Health</td>
      <td>*</td>
      <td>*</td>
      <td>10</td>
      <td>15.79%</td>
      <td>10.0</td>
      <td>*</td>
      <td>2020</td>
      <td>Comprehensive MCO</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Research Question 1 : 
# How does the number of providers affect the percentage of eligible patients receiving services across different counties and states?
import matplotlib.pyplot as plt

# Create a new column combining State and County for clearer labels
df['State_County'] = df['ï»¿State'] + ' - ' + df['County']

# Make sure the data is numeric
df['Number of Active Patients per Provider'] = pd.to_numeric(
    df['Number of Active Patients per Provider'], errors='coerce'
)

# Drop rows with missing or invalid data
df_clean = df.dropna(subset=['Number of Active Patients per Provider'])

# sort by value for better visualization
df_sorted = df_clean.sort_values(by='Number of Active Patients per Provider', ascending=False)
```


```python
# Research Question- 2: 
# Which MCOs or plan categories deliver the highest number of services per active patient, and what does this suggest about care intensity or efficiency?

# convert to integer
df['Number of Active Patients'] = pd.to_numeric(
    df['Number of Active Patients'], errors='coerce'
)

# compute service level summary
service_summary = df.groupby('Service Category')['Number of Active Patients'].sum()

#Plot the pie chart based on the distribution of Active Patients by Service Category
plt.figure(figsize=(10, 10))

plt.pie(
    service_summary.values,
    labels=service_summary.index,
    autopct='%1.1f%%',
    startangle=140
)

plt.title('Distribution of Active Patients by Service Category', fontsize=14)
plt.axis('equal') 

plt.tight_layout()
plt.show()
```


    
![png](output_2_0.png)
    



```python
# Research Question- 3:
# What are the trends in the number of active patients and the number of services per active patient across states?

import numpy as np
# Conversion to int
df['Number of Active Patients'] = pd.to_numeric(
    df['Number of Active Patients'], errors='coerce'
)
df['Number of Services per Active Patient'] = pd.to_numeric(
    df['Number of Services per Active Patient'], errors='coerce'
)

# Group by State and sum both columns:
state_summary = df.groupby('ï»¿State').agg({
    'Number of Active Patients': 'sum',
    'Number of Services per Active Patient': 'sum'
}).sort_values(by='Number of Active Patients', ascending=False)


print(state_summary.head())
print(state_summary.info())

print(df[['ï»¿State', 'Number of Active Patients', 'Number of Services per Active Patient']].head())



plt.figure(figsize=(16, 8))

# Recalculate x positions
x = np.arange(len(state_summary))

# Bar width
width = 0.35

# Plot bars
bars1 = plt.bar(x - width/2, state_summary['Number of Active Patients'], width, label='Active Patients')
bars2 = plt.bar(x + width/2, state_summary['Number of Services per Active Patient'], width, label='Services')

# Add labels and title
plt.xlabel('State', fontsize=12)
plt.ylabel('Count', fontsize=12)
plt.title('Total Active Patients vs Number of Services per State', fontsize=14)
plt.xticks(x, state_summary.index, rotation=45, ha='right')

plt.legend()
plt.tight_layout()
plt.show()
```

                Number of Active Patients  Number of Services per Active Patient
    ï»¿State                                                                    
    Michigan                     897831.0                               161105.0
    New Mexico                   317029.0                                30484.0
    Arizona                      180819.0                                52261.0
    Nevada                        11438.0                                 2124.0
    <class 'pandas.core.frame.DataFrame'>
    Index: 4 entries, Michigan to Nevada
    Data columns (total 2 columns):
     #   Column                                 Non-Null Count  Dtype  
    ---  ------                                 --------------  -----  
     0   Number of Active Patients              4 non-null      float64
     1   Number of Services per Active Patient  4 non-null      float64
    dtypes: float64(2)
    memory usage: 96.0+ bytes
    None
      ï»¿State  Number of Active Patients  Number of Services per Active Patient
    0  Arizona                        NaN                                    7.0
    1  Arizona                        NaN                                    9.0
    2  Arizona                        NaN                                    1.0
    3  Arizona                       67.0                                  371.0
    4  Arizona                        NaN                                   10.0
    


    
![png](output_3_1.png)
    



```python

```
