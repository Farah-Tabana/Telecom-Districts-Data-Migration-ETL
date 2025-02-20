# Telecom-Districts-Data-Migration-ETL

## Overview

The District Migration Project is designed to extract, clean, and migrate data from our legacy system into a new platform. The project leverages advanced SQL queries to consolidate data from various sources, apply rigorous transformation rules, and prepare the cleaned data for upload into the new system. This ensures data consistency and integrity throughout the migration process.

## File Structure

- **all_district.txt**  
  Contains the main SQL query that extracts, cleans, and transforms the core dataset. This query is comprehensive—it:
  - Joins multiple legacy tables such as `Vu_Simcards`, `Vu_Subscriptions`, `Vu_Subs_Packages`, `Vu_Ue_Sales_Serial_Lines`, and more.
  - Applies complex transformation logic to standardize fields like serial numbers, MAC addresses, product classifications, and statuses.
  - Uses conditional logic and multiple CASE statements to handle edge cases (for example, product model variations and specific ICC patterns).
  - Unions multiple subqueries to combine different segments of data, including handling records with different statuses (e.g., provisioned, active) and even extracting wallet-related information.

- **extract all district.txt**  
  Provides a complementary SQL query that extracts additional key data points such as subscription key values, IMSI, ICC, item codes, and package codes. This query is used for:
  - Cross-validating data against the primary extraction.
  - Supporting supplemental extraction needs and ensuring that all necessary attributes are captured for the migration process.

## SQL Query Details

The SQL queries perform the following key functions:

### Data Extraction and Transformation

- **Extracting Core Data:**  
  The queries pull data from multiple legacy systems by joining several tables. This includes:
  - **SIM Cards and Subscriptions:** Data is drawn from tables like `Vu_Simcards` and `Vu_Subscriptions` to capture details such as ICC numbers, IMSI, creation dates, and customer information.
  - **Product and Package Information:** By joining with tables like `Vu_Subs_Packages`, `Vu_Packages`, and `Vu_Ue_Sales_Serial_Lines`, the queries capture product codes, package codes, and other metadata.

- **Data Cleaning and Standardization:**  
  Extensive use of `CASE` statements and string functions (such as `TRIM`, `SUBSTR`, and `CONCAT`) are employed to:
  - **Standardize Serial Numbers:**  
    Different ICC formats are normalized based on model codes (e.g., `SWU-9200`, `BM632W`) and specific string patterns. For instance, certain prefixes are trimmed or substituted to produce a consistent serial number format.
  - **Format MAC Addresses:**  
    The queries generate MAC addresses by reformatting the IMSI into a colon-separated format.
  - **Determine Product Class and Tags:**  
    Depending on the ICC pattern or package code, the queries map records to standardized product classes (e.g., 'SWU-9200', 'BM632w') and assign tags such as `["commercial","chemchamal_shorsh","cpe"]`, which help in categorizing the devices.
  - **Map Subscription Codes:**  
    Conditional logic is used to assign subscription codes like `basic`, `super`, or `premium_unlimited` based on the package code, ensuring that migrated records match the new system’s subscription models.
  - **Setting Dates:**  
    The queries extract and format dates for when the record was provisioned and activated, using functions like `TO_CHAR` and `TRUNC` to ensure consistency.

- **Handling Multiple Data Segments:**  
  The main query is structured in segments (using `UNION`) to address different record types:
  - **Normal Cases:**  
    Extracts and cleans data for the majority of records.
  - **Provisioned Records:**  
    A specific section handles records that are only provisioned (without activation), ensuring they are included appropriately.
  - **Intermediate Data Extraction:**  
    Additional unions combine data segments to fill any gaps and ensure that all district-related data is captured.

### Data Consolidation and Wallet Extraction

- **Combining Data Sources:**  
  The queries join data from various sources and consolidate them into a single unified dataset. This ensures that all relevant customer and subscription details are available for migration.
  
- **Wallet Information:**  
  A separate subquery calculates wallet amounts and points:
  - It aggregates data from customer wallet tables.
  - It uses grouping and aggregate functions (like `MAX` and arithmetic operations) to determine the highest wallet amount per customer, which is then joined back to the main dataset.
  
### Extract All District Query

- **Focused Extraction:**  
  The query in `extract all district.txt` focuses on extracting key identifiers and package codes. It joins subscription data, SIM card details, and package information:
  - This query is designed to capture a subset of data, specifically for districts, and it uses unions to include records from different legacy tables.
  - The output includes subscription key values, IMSI, ICC, and item codes—critical for verifying that each district’s data is properly mapped and included in the migration.

