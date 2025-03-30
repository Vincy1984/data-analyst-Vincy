# **Project 1** - Implementation of DAP of Service Request Dataset in City of Vancouver in AWS

**Exploratory Data Anlysis**

**Objective** 	

The City of Vancouver generates and collects vast amounts of data, with data coming from numerous sources such as permits, public services, and infrastructure records. Building a DAP is paramount for turning raw data into applied insight, enabling better decisions, resource optimization, and citizen service improvement. This platform will enable stakeholders to derive actionable insights from historical and real-time data, such as tracking infrastructure changes, monitoring public service efficiency, and predicting future needs. The city can leverage scalable cloud infrastructure by migrating to AWS to efficiently store, process, and analyze large datasets. 

![CC Week #2 - HR - Vincy-CC Solution #2](https://github.com/user-attachments/assets/77e3e8bf-a02c-460e-b039-66425f2b3887)

**Dataset**

The 3 1 1 Service request Dataset consists of information of service request raised by community and their status if they have been resovled or closed. includes details
- Service Request Type
- Status
- Closure reason
- Service Request Close date
- Last modified timestamp
- Address
- Local area
- Channel
- Latitude
- Longitude

 **Methodology**
  
 **Data Ingestion**

  Service request data from Vancouver is being transferred to the AWS data center to support data-driven decision-making. The raw operational data, including metadata like request status, closure reasons, and timestamps, is stored in CSV format. Using AWS S3 in the North Virginia region, a bucket (cs-raw-vin) is created under Team Community Service with a structured folder hierarchy: community service → service request-list → year=2025 → quarter=1 → day=20. The raw CSV data is then uploaded for scalable storage and processing.

  ![Rawdataset](https://github.com/user-attachments/assets/5f5f0964-1d4b-4d01-8c20-bea6500b1ea7)

A cost analysis is conducted for storage, communication, and processing. The S3 storage cost is determined by factors such as access type and object size. The raw dataset (60.3 KB, CSV) is stored in S3 Standard-IA class, as it requires daily read access with only inbound data transfer.
A cs-trf-vin bucket is created for data migration, serving as temporary storage before processing and final transfer. This transfer bucket is structured with folders: community service → service request-list → system and user usage subfolders for efficient organization.

![Transfer Bucket](https://github.com/user-attachments/assets/e165b257-41af-44ea-922c-25efd8e3ade3)

**Data Profiling**

After ingesting the City of Vancouver's Service Request data into AWS S3, data profiling was conducted using AWS Glue DataBrew to detect and fix errors. Minimal errors were found, primarily missing values in the "address" column, which were replaced with "null," and schema names were edited.
A data profiling project (cs-srq-lst-prj-vin) and dataset (cs-srq-lst-ds-vin) were created in AWS Glue DataBrew. The dataset, stored in S3 (cs-raw-vin → service request-list) in CSV format, was accessed by granting AWS permissions for retrieval. Spaces in column names were removed, and missing values were handled before proceeding with data cleaning.

![Prj created](https://github.com/user-attachments/assets/c9b56013-4423-488a-8ea4-94b8ea391bcf)

After creating the project, data profiling is conducted by selecting the dataset to review its details. This helps determine the need for data cleaning.![Data profiling](https://github.com/user-attachments/assets/77f2f2b6-2859-4d32-bf74-03e945d4fb5b)
A data profiling job (cs-srq-lst-ds-prf-vin) is created, processing the full dataset due to its small size. The results are stored in the cs-trf-vin folder. Labrole permissions are granted, and the job is executed. Data profiling identifies 12 columns and 404 rows, highlighting missing values, valid values, and other issues for data cleaning.

![Prf intiated](https://github.com/user-attachments/assets/d35e0502-2cd4-457f-ae66-89846e5e310a)
![Uploading Data profiling.png…]()

**Data Cleaning**

The dataset has columns and values, and using the data project, we can see the poor-quality data with analytical visualization. A detailed analysis is carried out by columns and rows. Every step of the changes we make generates recipes that are applied to the sample displayed, and when we apply the corrections or changes, they are applied to the whole dataset.

All the schema names are concatenated, removing the spaces between each word in all five schemas. Next, the format for the Service Request Open Timestamp, Service Request Close Date, and Last Modified Timestamp is changed to the "yyyy-mm-dd" format. In the last step, missing values were identified in the Address and Local Area columns, and the empty cells were replaced with "null" values.

![Data Cleaning](https://github.com/user-attachments/assets/eb3c04f9-ef27-477c-bb61-952c2b03e227)

With the data cleaned, a cleaning job (cs-srq-lst-cln-vin) is created. The cleaned data is saved in multiple formats:

CSV Format – Stored in S3 (cs-trf-vin → User folder) for easy access and analysis. The file overwrites old values instead of creating new ones, with no partitioning.

Parquet Format – Stored in S3 (cs-trf-vin → System folder) using a column-based, compressed format for efficient analysis. The file is auto-generated and partitioned by Address, a key categorical value.

Permissions are granted as labrole, and the cleaning job is executed. 

![Saved in sys format](https://github.com/user-attachments/assets/25024ff5-cd6c-4367-b190-80b52d53f13b)

**Data Cataloging**

After data cleaning, the dataset is structured into a tabular format using AWS Glue. A Data Catalog (cs-data-catalog-vin) is created to store the schema for the City of Vancouver's Service Request data.

A Crawler (cs-crw-vin) is set up to automatically discover and update the catalog. It retrieves the CSV file from the S3 transfer bucket (Transform zone → System-friendly output) and loads it into the database. The crawler is run on demand, and upon completion, it generates a table in the database for structured analysis.

![draw io data catalog](https://github.com/user-attachments/assets/c4d91136-1f6b-427a-a0b1-6c97b4307c39)

![data table](https://github.com/user-attachments/assets/55369583-d5a2-40b3-9249-5b3d61b272b4)

**Data Summerisation**

The ETL pipeline for summarizing the City of Vancouver's Service Request dataset is built using AWS Glue without coding. Data is extracted from an S3 bucket and summarized into a tabular format. The dataset is transformed by applying summarization techniques, such as changing the schema to remove unnecessary columns (e.g., latitude and longitude) and filtering rows where the closure reason is "Service provided." The data is then aggregated based on the "Address" column, with counts used for aggregation. After mapping the columns, the cleaned data is saved in a curated S3 bucket, with the option to partition based on "Address." However, for user convenience, the data is combined into one partition and loaded into the User folder. The catalog tables are updated, and the pipeline is run, with progress monitored in Job Monitoring. The result is a structured and summarized dataset for service requests in Vancouver.

![ETL Job 1](https://github.com/user-attachments/assets/34e8efbe-62cf-4f51-a75a-c57280c416db)
![ETL 3](https://github.com/user-attachments/assets/e0994bd6-7ea2-40e5-bdd6-c802eff1e99e)
![ETL 2](https://github.com/user-attachments/assets/800f32e1-c8a1-492a-931d-9c4ef6ba425b)

**Data Analysis**
To implement DAP, the dataset used is identified as an OLTP relational model, structured in a tabular format with rows and columns, where each row represents a unique service request. The data is normalized and does not require formatting or correction. Athena, a serverless query service, is used to analyze the dataset directly from the S3 raw bucket. Athena integrates with AWS Glue for easy pipeline creation and is suitable for ad-hoc queries, though not ideal for smaller datasets. The curated bucket with the updated service request dataset is selected in Athena, and the cs-data-catalog-vin database is used for querying. The results of the queries are stored in the cs-cur-vin S3 bucket. The query used filters the data to count the total number of service requests.

![AWS Athena](https://github.com/user-attachments/assets/c238e2aa-79b4-4db0-ad2a-e7f8b52c9ac5)

**Data Security**

To ensure data security and protection for the service request data stored in AWS, encryption and decryption are used through AWS Key Management Services (KMS). The process includes creating duplicates of the Raw, Transfer, and Curated buckets in S3 for availability. In KMS, a symmetric key is used for both encryption and decryption, and the key administrator role is assigned with permissions to build and use the key. The key is applied for encrypting and decrypting data in the Raw, Transfer, and Curated buckets. For each bucket, encryption is configured, and versioning is enabled to retain multiple versions of the data. Additionally, backup buckets are created for replication. The replication rule is set to copy data from the original buckets to the backup buckets (e.g., cs-raw-bac-vin for the Raw bucket) to ensure data availability and protection.

![versioning](https://github.com/user-attachments/assets/4c74ec48-372b-4d3e-893f-37d78f799739)
![Key](https://github.com/user-attachments/assets/340a4c45-eb52-41e1-afdb-0ec5bbfff125)
![Key 1](https://github.com/user-attachments/assets/dc300a6d-a74c-4824-a7b5-d444a208048b)

**Data Governance**

For data governance, AWS Glue is used to perform a data quality check on the 3-1-1 request dataset. The dataset is extracted from the cs-raw-vin bucket into a pipeline, with results saved in separate folders based on whether they pass or fail the quality check. In the cs-trf-vin bucket, a "quality check" folder is created with two subfolders: "Passed" and "Failed." The quality check evaluates completeness, freshness, and uniqueness. Rows are categorized as passed or failed based on the evaluation, and a conditional router is added to the pipeline. The passed results are stored in the appropriate subfolder within the cs-trf-vin folder.

![Data Gov](https://github.com/user-attachments/assets/e34bf25a-7589-419d-9d47-da08f39c6f6a)
![Dat Gov 2](https://github.com/user-attachments/assets/7f8a80bf-8092-47ab-8cb3-2c3a5e9fed6a)


**Data Monitoring**
A monitoring dashboard named cs-srq-MCR-vin was created in AWS CloudWatch to track metrics from S3 buckets and AWS Glue jobs. For S3, storage metrics from the Raw zone bucket were monitored over two weeks, with graphs refreshing every 15 minutes. Similarly, Glue job runs were tracked for one week with the same refresh rate. To enable proactive control, an alarm was set with a threshold of 60,000 for the bucket size, triggering email notifications via an SNS topic named notification_for_community_services_data_team. Additionally, AWS CloudTrail was used to log and review all activities for auditing purposes. This setup ensures real-time monitoring and automated alerts for storage and job execution metrics.

![Cloudwatch](https://github.com/user-attachments/assets/55df050e-858f-43ae-bd3e-9026e3987f7e)
![Cloud trail](https://github.com/user-attachments/assets/85a3a302-c8d8-4c4c-a2e8-5f28eec35365)


#**Data Visulisation**

![Screenshot 2025-03-29 232246](https://github.com/user-attachments/assets/96da7acb-d822-4f5e-9db9-f34b9b9103a7)
![Screenshot 2025-03-29 232159](https://github.com/user-attachments/assets/dadfe7a8-e008-40bf-a5fa-67ff8c04499d)
![Screenshot 2025-03-29 232100](https://github.com/user-attachments/assets/a14182a1-0e44-45b3-b41f-a6d56bb71cb3)

**Key insights these visualizations reveal**:

- The vast majority of requests 80% are resolved with "Service provided"

- Most requests are resolved within 1-3 days (median resolution time)

- Phone is the dominant channel (99% of requests)

- Request volume is relatively consistent throughout the year with slight dips in summer

- The few geolocated requests cluster around downtown Vancouver (Hamilton St area)

**Recommendations**
- Check why digital channels are underutilised.
- Collect more location data to find high risk areas.
- Automate reminders for unresolved cases.

# **Tools Used**
- S3 Bucket
- AWS GLue Data Brew
- AWS Glue
- AWS Athena
- AWS CloudWatch
- AWS Cloud Trail

# **Acknowledgements**
This project was built to demonstrate end-to-end data analytics skills using real tools like AWS, Pandas, Seaborn, and GitHub markdown.

Thanks to the City of Vancouver for the public dataset!
