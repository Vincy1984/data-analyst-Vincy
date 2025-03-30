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

  
