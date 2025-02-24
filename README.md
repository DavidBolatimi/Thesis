# Thesis
Utilizing Python to compare healthcare services utilization across the healthcare system
between frequent and non-frequent emergency department (ED) users among Medicaid enrollees in South Carolina.

Performed exploratory data analysis with Github on healthcare datasets to identify trends in patient visits, using Python and Pandas for data wrangling.
• Orchestrated a system using Github to automate tracking hospital visitation rates per quarter using Python scripting; this initiative resulted in saving approximately three hours weekly spent compiling manual reports across departments.
• Examined over 2,000 patient visits annually to quantify attendance trends by seasonality and demographics, delivering crucial findings illuminating the three primary drivers of increased hospital traffic throughout the year.

Currently editing Python code (Blueprint):

import pandas as pd
pd.set_option("display.max_rows", None)  # Show all rows

# Define the file path
file_path = "/Users/macbook/Desktop/NGH EMERGENCY DEPT.csv"

# Load the Excel file
df = pd.read_csv(file_path)

# Show all columns
print(df.columns.tolist())

#Check all departments
categories = df['ED_Depart_Name'].unique()
#print(categories)

#Filter data for each department
filtered_df = df[df['ED_Depart_Name'] == 'MCB MD360']
#print(filtered_df)
filtered_df.to_csv('MCB MD360.csv')

# Ensure the ED_Admission_Date column is in datetime format
df["ED_Admis_Date"] = pd.to_datetime(df["ED_Admis_Date"])

# Create a new column for quarters
df["Quarter"] = df["ED_Admis_Date"].dt.to_period("Q")

#print(df.tail(20))
# Save the updated DataFrame back to the CSV file
#df.to_csv("NGH EMERGENCY DEPT.csv", index=False) <--Need This Later

#####Is patient regular on a quarterly or annual basis? Group by Quarters

###Count the frequency of each Patient_code within each Quarter
df["No_of_Visits"] = df.groupby(["Patient_code", "Quarter"])["Patient_code"].transform("count")

# Display the updated DataFrame
print(df[["Patient_code", "Quarter", "No_of_Visits"]])

###Has Patient visited more than once in any Quarter?
#Count visits per Patient_code per Quarter
visit_counts = df.groupby(["Patient_code", "Quarter"]).size().reset_index(name="No_of_Visits")

# Filter patients who visited more than once in any quarter
multiple_visits = visit_counts[visit_counts["No_of_Visits"] > 1]

# Display the result
print(multiple_visits)

###(1)List from Max Visits to Min Visits Overall
# Count visits per Patient_code
visit_counts = df["Patient_code"].value_counts().reset_index()

# Rename columns for clarity
visit_counts.columns = ["Patient_code", "No_of_Visits"]

# Sort by No_of_Visits in descending order
visit_counts = visit_counts.sort_values(by="No_of_Visits", ascending=False)

# Display the result
print(visit_counts)

###(2) List from Max Visits to Min Visits by Quarter
# Count visits per Patient_code per Quarter
visit_counts = df.groupby(["Quarter", "Patient_code"]).size().reset_index(name="No_of_Visits")

# Sort by Quarter and No_of_Visits in descending order
visit_counts = visit_counts.sort_values(by=["Quarter", "No_of_Visits"], ascending=[True, False])

# Display the result
print(visit_counts)

##Visualization
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd

# Sorting by Quarter and No_of_Visits (Descending)
df = df.sort_values(by=["Quarter", "No_of_Visits"], ascending=[True, False])

# Create the plot
sns.barplot(x=df["Quarter"], y=df["No_of_Visits"], hue=df["Patient_code"], data=df)

# Add labels and title
plt.xlabel("Quarter")
plt.ylabel("Number of Visits")
plt.title("Patient Visits Per Quarter")
plt.legend(title="Patient Code")
plt.grid(axis="y", linestyle="--", alpha=0.7)

# Show the plot
plt.show()

