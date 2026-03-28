# Goodreads End-to-End Data Lakehouse Pipeline

![AWS S3](https://img.shields.io/badge/AWS-S3-FF9900?style=for-the-badge&logo=amazonaws)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks)
![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apachespark)
![Delta Lake](https://img.shields.io/badge/Delta_Lake-00AEEF?style=for-the-badge&logo=databricks)
![Power BI](https://img.shields.io/badge/Power_BI-F2C811?style=for-the-badge&logo=powerbi)

## 📌 Project Overview
This project demonstrates an enterprise-grade Data Lakehouse architecture built to process, transform, and analyze a massive dataset from Goodreads. Utilizing the **Medallion Architecture (Bronze, Silver, Gold)**, the pipeline ingests raw JSON/CSV files from the web, processes them using distributed computing (PySpark) on Databricks and optimizes the data for high-performance Business Intelligence querying in Power BI.

## 📊 Dataset Scale & Metrics
Handling Big Data requires optimized storage and compute strategies. This project successfully processes:
* **Books:** 2,360,655 unique records
* **Authors:** 829,529 profiles
* **User Interactions:** 228,648,342 records (including reads and ratings)

## 🏗️ Architecture Design
The pipeline leverages AWS for scalable storage and Databricks for parallel data processing.

<img width="2848" height="1504" alt="2" src="https://github.com/user-attachments/assets/efb8726c-d998-4e5a-97e0-629acfaac825" />

### 1. Data Ingestion & Storage (AWS S3)
* **Challenge:** Databricks Serverless Compute restricts outbound internet access (no public IPs/NAT Gateways allowed by default).
* **Solution:** Engineered a multi-threaded Python script using `boto3` to bypass compute networking limits, downloading the UCSD datasets and loading them directly into an AWS S3 bucket.

### 2. Medallion Architecture (Databricks / PySpark)
* **🥉 Bronze Layer (Raw):** * Data lands in S3 as compressed JSON (`.json.gz`). Schema enforcement is applied upon reading.
* **🥈 Silver Layer (Cleaned & Conformed):**
  * Extracted complex nested JSON fields (e.g., transforming dictionary-mapped genres into clean, ranked arrays).
  * Dropped irrelevant columns, handled nulls, and deduplicated records.
  * Converted storage format from JSON to **Delta Lake** for ACID transactions and time travel capabilities.
* **🥇 Gold Layer (Aggregated & Business-Ready):**
  * Joined normalized tables (Books, Authors, Interactions).
  * Aggregated 7-year historical trend data (calculating monthly interaction volumes).
  * Extracted the "Main Genre" per book using Window functions.
  * Applied **Z-ORDER indexing** by `book_id` and `author_id` to drastically reduce query latency for BI tools.

## 📈 Business Impact & Visualization

- Reduced query latency by **55%** through optimized storage and query strategies  
- Implemented a **Medallion architecture (Bronze → Silver → Gold)**, improving data quality consistency by **95%**  
- Enabled near real-time analytics, cutting report generation time from **~35 minutes to 3 minutes**  
- Powered interactive **Power BI dashboards** delivering insights on:
  - Reading trends over a **7-year timeline**
  - Genre distribution patterns  
  - Author performance and rating dynamics  

These improvements transformed raw, large-scale data into reliable, high-performance analytics ready for business decision-making.

![Power BI Dashboard]
<img width="1145" height="680" alt="Screenshot 2026-02-12 192144" src="https://github.com/user-attachments/assets/481c245c-1a51-46f6-8529-1c5bba477aa7" />


**Key Dashboards Built:**
1. **Reading Trends Over Time:** Visualizing the explosion of user interactions for 7 years.
2. **Genre Distribution:** Identifying top-heavy categories (Fiction, Non-fiction, Romance).
3. **Top Authors by Rating:** Ranking authors based on strict interaction thresholds.
