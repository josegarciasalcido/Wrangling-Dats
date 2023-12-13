# Wrangling-Data
# Below is my code:
import pandas as pd
df = pd.read_csv("https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/LargeData/m1_survey_data.csv")
print(len(df[df.duplicated()])) #Print the number of duplicated rows

print(df['Respondent'].duplicated().sum()) # Count the number of duplicates in the 'Respondent' column

df2 = df.drop_duplicates() # Create a new data frame without duplicates
print(len(df2[df2.duplicated()])) # Verify that the new data frame does not have duplicates
print(df2.shape[0]) # find the number of remaining rows
print(df2['EdLevel'].isna().sum())# find the number of blank rows under column EdLevel
print('For country: ' + str(df2['Country'].isna().sum()))# find the number of blank rows under column Country
print('Majority Category of Employment is ' + str(df2['Employment'].mode().iloc[0]))
print('Minority Category of UnderGradMajor is ' + df2['UndergradMajor'].value_counts().idxmin())
#print(df2.isnull()) # Find the missing values for all columns
print(df2['WorkLoc'].isnull().sum())# Find out how many rows are missing in the column 'WorkLoc'
print(df2['WorkLoc'].value_counts()) # Find the value counts for the column WorkLoc. # Office is the most frequent
df3 = df2.copy() # create an identical copy of the data frame to create a new one
df3['WorkLoc'].fillna('Office', inplace=True) # replace empty values with most frequent value 'Office'
print(df3['WorkLoc'].isnull().sum())# Verify no empty values
print(df3['CompFreq'].unique()) # Find the categories in column CompFreq
print(df.columns.get_loc('CompFreq'))
print('# of people paid yearly = ' + str(df3['CompFreq'].value_counts().get('Yearly', 0)))

# Define conversion factors for CompFreq
conversion_factors = {'Yearly': 1, 'Monthly': 12, 'Weekly': 52}

def norm_comp_func(row, conversion_factors):
    if pd.notnull(row['CompFreq']): # if total compensation is not empty
        return row['CompTotal'] * conversion_factors.get(row['CompFreq'], 1) # compute normalized total yearly compensation
    else:
        return row['CompTotal']

# Update the values in the new 'NormalizedAnnualCompensation' column
df3['NormalizedAnnualCompensation'] = df3.apply(lambda row: norm_comp_func(row, conversion_factors), axis=1)

print("The median of the Normalized Annual Compensation is " + str(df3['NormalizedAnnualCompensation'].median()))

# Specify the Excel file path (replace 'output_file.xlsx' with your desired file name)
excel_file_path = 'Capstone Excel File.xlsx'
# Write the DataFrame to an Excel file
df3.to_excel(excel_file_path, index=False)
print(f'DataFrame has been written to {excel_file_path}')
print(df3.head())
