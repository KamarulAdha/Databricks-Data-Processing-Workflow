# Databricks Data Processing Workflow

## 1. Data Ingestion
1.1. ADLS Connection
   - **Credentials Retrieval**: Access credentials are securely retrieved from Databricks secrets, allowing for secure, seamless connection to Azure Data Lake Storage (ADLS).
   - **Unmount Existing Containers**: To ensure a clean workspace and avoid conflicts, any previously mounted containers are unmounted. This step ensures that the process uses fresh, updated connections.
   - **Mount Required Containers**: Based on the environment setting, the notebook mounts specific ADLS containers. Mounting enables access to necessary files and directories within ADLS for subsequent data processing.

1.2. Data Loading
   - **Data Loading with Pandas**: The notebook reads Excel files from ADLS using Pandas. This approach allows for flexible data manipulation, including filtering, transformation, and visualization within the notebook.
   - **Source Data Categories**: Data is ingested from various business-specific categories, including Marketing & Sales (M&S), Financials, and other critical metrics. Each category represents a distinct area of the business, essential for comprehensive reporting.
   - **Output in Pandas DataFrames**: The raw data is initially loaded into Pandas DataFrames. These DataFrames serve as the foundation for subsequent data cleaning and transformation steps before being transferred into Spark DataFrames for distributed processing.

## 2. Data Type Conversion
2.1. Type Mapping
   - **Data Type Conversion Functions**: Custom functions are implemented to map Pandas data types to their corresponding Spark data types, ensuring compatibility when converting between the two. This helps maintain data integrity across processing steps.
   - **Example Conversions**: For example, a `datetime` type in Pandas is mapped to `TimestampType` in Spark. Other mappings include `float` to `DoubleType` and `int` to `IntegerType`, which prevent data loss or type errors during processing.

2.2. DataFrame Conversion
   - **Conversion to Spark DataFrames**: Once data types are mapped, the Pandas DataFrames are converted to Spark DataFrames. This transition leverages Spark’s distributed processing capabilities, which are critical for handling large volumes of data efficiently within Databricks.
   - **Purpose of Conversion**: By utilizing Spark DataFrames, the notebook can process data at scale, allowing for high-performance operations such as aggregations, joins, and data transformations. This step ensures the workflow is optimized for Databricks' architecture.

## 3. Data Transformation
3.1. Data Cleaning
   - **Column Standardization**: Column names are standardized to maintain consistency across different datasets. This may involve renaming columns to a uniform format or replacing special characters for easier handling.
   - **Null and Missing Value Handling**: The notebook employs techniques such as filling missing values with defaults or removing rows/columns where appropriate, ensuring that data is clean and reliable for further analysis.
   - **Date Normalization**: Dates are formatted consistently to facilitate date-based operations. For example, dates are often converted to a standard `YYYY-MM-DD` format to streamline comparisons and aggregations.
   - **Removal of Redundant Columns**: Any unnecessary columns that do not contribute to the analysis are removed, helping to focus on relevant data and improve processing speed.

3.2. Spark SQL Operations
   - **Temporary View Creation**: Spark SQL temporary views are created for each data category (e.g., M&S, Financial) to enable SQL-based transformations. This setup allows for complex queries that are challenging to implement directly in Pandas.
   - **SQL Query Execution**: Spark SQL queries are run to calculate critical metrics, such as monthly and yearly aggregations. This includes identifying boundary dates, filtering out adjustments, and calculating derived metrics like MTD (Month-to-Date) and YTD (Year-to-Date) figures.
   - **Derived Metric Calculation**: Using SQL functions, derived metrics are computed to enrich the data with additional insights, such as growth rates or variances. These calculations are essential for generating actionable insights on the dashboard.

3.3. Output Preparation
   - **Default Value Handling for Nulls**: Where necessary, default values are assigned to fields with null values to prevent issues during dashboard visualization and ensure data completeness.
   - **Decimal Precision Adjustment**: Decimal values are rounded to a specified precision to maintain consistency and accuracy in numerical data, especially for financial figures.
   - **Schema Restructuring**: The columns are reordered and renamed to match the output schema required for downstream processes. This restructuring facilitates smooth integration with other systems, such as BI tools or data warehouses.

## 4. Data Persistence
4.1. Parquet Conversion
   - **Conversion to Parquet Format**: The processed Spark DataFrames are saved in Parquet format, a columnar storage format optimized for both storage efficiency and fast query performance. Parquet supports compression and efficient data retrieval, making it ideal for large datasets.
   - **Rationale for Parquet**: Parquet files are highly compatible with Spark and many other analytics platforms. By using Parquet, the workflow ensures data is stored in a format that minimizes storage costs while maximizing performance.

4.2. ADLS Write Operations
   - **Define Output Directory Structure**: A clear directory structure is established within ADLS, organizing files by categories such as M&S, Financial, and One-Off. This categorization simplifies data retrieval and management.
   - **Parquet File Storage**: The processed data is written to designated locations within ADLS, categorized based on data types. This organization supports straightforward access by downstream systems or users who need to query the data directly from ADLS.

## 5. Visualization Integration
5.1. Data Validation
   - **Integrity and Consistency Checks**: Before making the data available for dashboard visualization, the notebook performs validation checks to confirm data integrity and consistency. This ensures that the data meets the requirements for accurate dashboard reporting.
   - **Dashboard Compatibility Assurance**: Data is checked for compatibility with the specific requirements of the CEO Dashboard, ensuring that the format, structure, and content align with the visualization tool's needs.

5.2. BI Tool Connection
   - **Data Source Linking**: The Parquet files are linked as data sources in BI tools or visualization platforms, enabling real-time data visualization for stakeholders.
   - **Real-Time Data Refresh Configuration**: The workflow configures automated data refresh intervals to keep the dashboard up-to-date with the latest processed data. This ensures that the CEO Dashboard reflects the most current metrics for decision-making.

## 6. Change Management and Notifications
6.1. Delta Detection
   - **Change Detection Logic**: The notebook implements logic to detect changes by comparing newly processed data against historical datasets. This is crucial for monitoring key updates, such as changes in Township Mapping.
   - **Focus on Critical Changes**: The detection process prioritizes changes that impact business operations, like adjustments in sales metrics or updates in financial figures.

6.2. Alert Generation
   - **Create and Save Alert Messages**: When a change is detected, the notebook generates an alert message detailing the detected change. These alerts are saved to a designated directory within ADLS for reference and auditing.
   - **Alert Data Storage**: The alert messages are stored in a structured format within ADLS, enabling easy access for stakeholders who need to review historical changes.

6.3. Notification Dispatch
   - **Trigger External Notification System**: The notebook calls a Logic App endpoint, which dispatches notifications to stakeholders through email or other channels. This step ensures timely communication of important updates.
   - **Configure Email Alerts**: Email alerts are configured to include relevant details about the changes detected, providing stakeholders with actionable insights promptly.

## Workflow Summary
This ETL pipeline manages the end-to-end processing of data from raw sources in ADLS to dashboard-ready Parquet files. It includes data ingestion, type conversion, thorough transformations, efficient storage, and visualization integration. Additionally, it incorporates a change detection mechanism with automated notifications, ensuring that stakeholders are kept informed of key updates. The workflow supports data-driven decision-making by providing reliable and timely metrics on the CEO Dashboard.
