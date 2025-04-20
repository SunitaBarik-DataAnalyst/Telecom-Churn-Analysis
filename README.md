# import libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from anyio.abc import value

# LOAD DATA
# Loading the CSV data to a pandas dataframe
df=pd.read_csv(r"C:\Users\sunit\Documents\DATA ANALYSTS\Python\Churn Analysis Project\Customer Churn.csv")
pd.set_option('display.max_columns', None)  # Show all columns
pd.set_option('display.width', None) # Show all rows
print(df.head(20))

# data info
df.info() # Shows the non-null count and datatype

# wants only columns datatypes
print(df.dtypes)

# Let's convert totalcharges null rows into 0 and change its data type from object to float
df["TotalCharges"]= df["TotalCharges"].replace(" ","0")
df["TotalCharges"]= df["TotalCharges"].astype("float")
df.info()

# Let's check null values in our data
df.isnull().sum()

# Let's do descriptive analysis through the describe function
data_desc=df.describe()
print(data_desc)

# let's check duplicates
data = df.duplicated().sum()
print(data)
data1 = df["customerID"].duplicated().sum() 
print(data)


# converted 0 and 1 values of senior citizen to yes/no to make it easier to understand.
def conv(value):
    if value == 1:
        return "Yes"
    else:
        return "No"

df["SeniorCitizen"] = df["SeniorCitizen"].apply(conv)
print(df.head(50))

# Let's check out how many customers churned out
ax = sns.countplot(x="Churn", data = df)
ax.bar_label(ax.containers[0])
plt.title("Count of Customers Churned")
plt.show()

# Let's check the percentage of customers churned
gb= df.groupby("Churn").agg({"Churn":"count"})
plt.pie(gb["Churn"], labels = gb.index, autopct="%1.2f%%")
plt.title("Percentage of Churned Customer")
plt.show()

# let's explore the reason behind the customer churn through gender
sns.countplot(x="gender",data = df,hue= "Churn")
plt.title("Churned by Gender")
plt.show()

# Let's count senior citizens
ax = sns.countplot(x="SeniorCitizen", data = df)
ax.bar_label(ax.containers[0])
plt.title("Count of Customers by SeniorCitizen")
plt.show()
# Let's check through Senior Citizen
sns.countplot(x="SeniorCitizen",data = df,hue= "Churn")
plt.title("Churned by SeniorCitizen")
plt.show()


# Stacked bar chart with labels as %
grouped = df.groupby(["SeniorCitizen", "Churn"]).size().unstack().fillna(0)
percent = grouped.div(grouped.sum(axis=1), axis=0) * 100  # convert to percentages

# Step 1: Calculate percentage data
grouped = df.groupby(['SeniorCitizen', 'Churn']).size().unstack(fill_value=0)

# Step 2: Convert to percentages
percentages = grouped.div(grouped.sum(axis=1), axis=0) * 100

# Step 3: Plot
ax = percentages.plot(kind='bar', stacked=True, figsize=(8,6))

# Step 4: Add labels on bars
for i, row in enumerate(percentages.values):
    cumulative = 0
    for j, val in enumerate(row):
        if val > 0:
            plt.text(i, cumulative + val/2, f'{val:.1f}%', ha='center', va='center', fontsize=10)
            cumulative += val

plt.title("Churn Percentage by Senior Citizen")
plt.ylabel("Percentage")
plt.xlabel("Senior Citizen")
plt.xticks(rotation=0)
plt.legend(title="Churn")
plt.tight_layout()
plt.show()

# Let's check the churn rate on the basis of tenure
sns.histplot(x="tenure",data=df,hue="Churn",bins=72)
plt.show()

# Let's count the customers on the basis of a contract.
ax = sns.countplot(x="Contract", data = df,hue="Churn")
ax.bar_label(ax.containers[0])
plt.title("Count of Customers By Contracts")
plt.show()

print(df.columns.values)

# Let's create subplots for all the services provided to the customers and check their churn rate

# List of columns you want to plot
cols = [
    'PhoneService', 'MultipleLines', 'InternetService', 'OnlineSecurity',
    'OnlineBackup', 'DeviceProtection', 'TechSupport',
    'StreamingTV', 'StreamingMovies'
]

# Set up the subplot grid
n_cols = 3  # Number of columns in subplot grid
n_rows = (len(cols) + n_cols - 1) // n_cols  # Auto-adjust rows

plt.figure(figsize=(18, n_rows * 4))  # Adjust figure size based on rows

# Loop through columns and create countplots
for idx, col in enumerate(cols, 1):
    plt.subplot(n_rows, n_cols, idx)
    sns.countplot(x=col, data=df, hue='Churn')
    plt.title(f'{col} vs Churn')
    plt.xlabel(col)
    plt.ylabel('Count')
    plt.xticks(rotation=45)

plt.tight_layout()
plt.show()

# Let's count the churned customers on the basis of payment method.
ax = sns.countplot(x="PaymentMethod", data = df,hue="Churn")
ax.bar_label(ax.containers[0])
ax.bar_label(ax.containers[1])
plt.title("Churned Customers By PaymentMethod")
plt.xticks(rotation=45)
plt.show()
