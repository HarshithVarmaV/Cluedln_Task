# Comprehensive Data Engineering Report
Advanced company profiling, cleansing, and enrichment pipeline using Companies House API (Python + Jupyter)

# Objective
This report outlines a comprehensive data engineering solution developed to profile, cleanse, deduplicate, validate, and enrich sample records from UK companies using Python in a Jupyter Notebook environment. The solution leverages the Companies House REST API to ensure accuracy, reliability, and integrity of the company information.

# Detailed Methodology

## 1. Data Acquisition and Initial Loading
First we try to install and import necessary libraries for this python notebook.

![Screenshot 2025-05-07 at 8 57 05 PM](https://github.com/user-attachments/assets/5480a531-55f4-40e1-92fa-9f84c11bbc7b)

![Screenshot 2025-05-07 at 8 57 44 PM](https://github.com/user-attachments/assets/65ec13c5-f202-4997-b8f5-1bf8260782e1)

Then we import the provided company dataset (Company.csv) into a structured pandas DataFrame for further analysis and then we try to preview the data.

![Screenshot 2025-05-07 at 9 35 09 PM](https://github.com/user-attachments/assets/32b35b8a-f40b-40da-9a9c-1819378a7536)

## 2. Exploratory Data Profiling
Here we conduct a comprehensive preliminary analysis using the "ydata-profiling" tool to identify data structure, completeness, distribution anomalies, and potential quality issues. Profiling highlighted critical issues, including inconsistent date formats, placeholder values, missing data, and potential duplicates.

![Screenshot 2025-05-07 at 9 06 49 PM](https://github.com/user-attachments/assets/757bab55-bd8d-4ede-b43f-a39bf44c85f6)
![Screenshot 2025-05-07 at 9 08 49 PM](https://github.com/user-attachments/assets/dd967b3c-f344-4916-b147-893f852c0847)
![Screenshot 2025-05-07 at 9 26 41 PM](https://github.com/user-attachments/assets/4c12b210-3113-4e08-8166-e614a172c152)

From profiling tool, we could identify that "CompanyNumber", which can be used for uniquely identifying the Company details from RESTAPI, so we need to work on handling duplicates records. 

Then verified the data types and number of rows and columns of the sample dataset.

![Screenshot 2025-05-07 at 9 31 14 PM](https://github.com/user-attachments/assets/dc4891c3-6bf0-477c-a4d4-186b64029383)
![Screenshot 2025-05-07 at 9 31 44 PM](https://github.com/user-attachments/assets/6d4f36f1-3466-4af8-9598-d259ca28fd5c)

From data types, we could identify that field data types are not accurate so we need to work on standardizing the data types.

## 3. Data Cleansing and Transformation
Here we try to standardize and cleanse data to correct inconsistencies, parse dates uniformly, handle missing values, and optimize data types.

Initially we standardized missing values by replacing ['--', 'n/a'] with NaN

![Screenshot 2025-05-07 at 9 41 48 PM](https://github.com/user-attachments/assets/f6aa8cc5-67d8-4ab8-99d3-3c4403495fcb)

Then we try removing leading/trailing whitespace from column names to avoid mismatches.

![Screenshot 2025-05-07 at 9 42 56 PM](https://github.com/user-attachments/assets/679f3cd6-fede-433b-9e86-4a31ce85aa52)

Then we try to identify date fields and standardize them as in those fields we identified some different type entries such as no spaces or special characters and different type of special characters to seperate Date, Month and Year.

![Screenshot 2025-05-07 at 9 43 43 PM](https://github.com/user-attachments/assets/6161c866-0c0a-4851-ac91-9b403664fa83)

Similarly, we try to identify categorical, numerical and string type of fields and changed the data types appropriately.

![Screenshot 2025-05-07 at 9 46 07 PM](https://github.com/user-attachments/assets/abd7d185-ae22-4e69-a399-b855e60e7b4f)

After data type transformations, each fields data type looks as below.

![Screenshot 2025-05-07 at 9 48 02 PM](https://github.com/user-attachments/assets/5f19ffcf-c9eb-443a-817f-c13a499b0777)
![Screenshot 2025-05-07 at 9 48 23 PM](https://github.com/user-attachments/assets/124ed2f9-c931-4510-92f6-f07041bd5b57)

As "CompanyNumber" can be used for identifying the Company details from RESTAPI, so we try to eliminate rows with empty "CompanyNumber" from further analysis and then pad "CompanyNumber" with leading zeros as this field should have 8 characters. 

![Screenshot 2025-05-07 at 9 50 45 PM](https://github.com/user-attachments/assets/73961497-b05f-439c-9a95-0bfe4e7d6c5d)

## 4. Deduplication
Deduplication significantly reduces redundancy, resulting in a streamlined dataset suitable for effective API validation. And as each duplicate row has different rules for identifying as unique identifier for each record company details, so we did not consider these rows as well for our further analysis.

![Screenshot 2025-05-07 at 9 54 04 PM](https://github.com/user-attachments/assets/8e1dcbea-16b7-4091-a58d-8dac8571f534)

After removing duplicate records, the count of number of rows and columns are as shown below.

![Screenshot 2025-05-07 at 9 57 02 PM](https://github.com/user-attachments/assets/b3a41196-79e6-41ec-a5d0-cc65de1bf73c)

## 5. Matching (Validation with Companies House API)
Leveraged Companies House REST API to validate company records by matching company numbers and names. API validation identified discrepancies, including mismatches and no responses, essential for subsequent enrichment and corrections.

We have created an application in "Companies House Rest API" in "live" environment and then try to create a key for this application. We need to use this key for accessing this RESTAPI. As these keys should not be hardcoded in the code because of security reasons and the same code can be used to access different environments data, so we have created a ".env" file to store this RESTAPI key.

![Screenshot 2025-05-07 at 10 06 32 PM](https://github.com/user-attachments/assets/fe3e35f6-6f12-42fb-95c3-4b526620094a)

Then we try to enrich this dataset by validating the data from RESTAPI and adding new fields like "API_CompanyName", "CompanyStatus_Matched", "IncorporationDate_Matched", "DissolutionDate", etc.
Here we call this RESTAPI by passing "CompanyNumber" of each record to get the information.

![Screenshot 2025-05-07 at 10 08 34 PM](https://github.com/user-attachments/assets/fb27d106-4727-45b9-a91e-888363b44858)

We tried to validate the records which got failed (No match in company name or company number) to validate RESTAPI call.
![Screenshot 2025-05-07 at 10 33 48 PM](https://github.com/user-attachments/assets/d37722d0-c1f2-456d-ab16-3985329ef559)

Then we tried to check the count of records which are miss matched in CompanyName and CompanyNumber as below.

![Screenshot 2025-05-07 at 10 35 37 PM](https://github.com/user-attachments/assets/bf62e31d-9e77-4ddc-b682-43ea66c03a86)

## 6. Visualization

We tried to vizualize the number of companies which are having matching company names, miss match in company names (Name mismatch) and company numbers (No API response).

![Screenshot 2025-05-07 at 10 37 01 PM](https://github.com/user-attachments/assets/8ed959a7-8fe8-442c-945c-616579444ffb)

The “Validation Result Distribution” chart illustrates that out of 274 company records processed, 251 were exact matches with the Companies House API, indicating a high data accuracy rate of approximately 89.2%. This reflects strong alignment between the input dataset and the official registry. However, 22 records exhibited name mismatches, likely due to differences in formatting, the use of trading names, or minor entry errors. These discrepancies should be flagged for review and potentially reconciled using fuzzy matching or manual intervention. Only one record resulted in an API failure, suggesting the integration was reliable and that the input data was largely clean. Overall, the validation process was highly effective, with minimal anomalies that can be addressed through targeted refinement.

And then we tried to visualize the split of company status for the matched company names records.
![Screenshot 2025-05-07 at 10 40 23 PM](https://github.com/user-attachments/assets/a14a423c-2f2c-44ff-b3bb-81ce8d658d99)
![Screenshot 2025-05-07 at 10 41 24 PM](https://github.com/user-attachments/assets/82de3401-1727-4f7c-a5ab-ae57f8e2cbc2)

The horizontal bar chart titled “Company Status Distribution Among Valid Matches” provides a breakdown of the official statuses of companies that were successfully validated using the Companies House API. The majority of these companies—209 out of 251—are marked as active, indicating that most of the dataset consists of currently operational businesses. A smaller segment, 33 companies, are dissolved, reflecting entities that have been formally closed. The chart also identifies 6 companies in liquidation, 2 as merely registered (but likely not trading yet), and 1 under voluntary arrangement, which typically suggests a financial restructuring agreement.

This distribution confirms that the dataset is predominantly composed of live and active companies, which is beneficial for downstream analytics like customer engagement or risk profiling. However, the presence of dissolved and liquidated companies suggests a need for business rules to exclude or flag these records for certain applications such as marketing or credit evaluation.

These visualizations offered insights into company operational statuses and data alignment with the Companies House registry.

And then we tried to verify the count of number of records which are having success in API calls is as shown below.
![Screenshot 2025-05-07 at 10 42 54 PM](https://github.com/user-attachments/assets/66183602-6fbe-454c-8df2-0c6173fc0000)

Then we tried to conduct a exploratory data analysis using the "ydata-profiling" tool to identify data structure, completeness, distribution anomalies, and potential quality issues. Profiling highlighted critical issues, including inconsistent date formats, placeholder values, missing data, and potential duplicates.

![Screenshot 2025-05-07 at 10 44 26 PM](https://github.com/user-attachments/assets/8c88582b-2662-4c86-8463-092fb7f9026f)
![Screenshot 2025-05-07 at 10 46 12 PM](https://github.com/user-attachments/assets/4ab6e3a7-91bc-4694-8abe-ddc8e9b09cc8)
![Screenshot 2025-05-07 at 10 46 39 PM](https://github.com/user-attachments/assets/2610a051-c1af-4582-a92d-4e60c198085d)

The final dataset is more trustworthy, complete, and semantically rich compared to its initial state. Though the percentage of missing values remains around 51%, that’s expected due to additional fields from API enrichment (which may not apply to every record). Overall, the data transformation workflow significantly elevated the dataset’s analytical value and readiness for downstream business use.

Exported the final enriched dataset in a format suitable for downstream analytics.

![Screenshot 2025-05-07 at 10 51 46 PM](https://github.com/user-attachments/assets/31431fc7-0f97-4ff2-8fc4-4eafa046c690)

# Outcome

This project successfully transformed company dataset into a validated and enriched data asset. It demonstrates reliable API integration, effective data cleansing, and high analytical quality. The resulting dataset supports business intelligence, customer profiling, and regulatory analysis.













