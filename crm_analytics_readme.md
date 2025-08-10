# TASK 1
Import the CRM events and CRM call center logs tables into a PostgreSQL database. Use
SQL to join the tables and summarize the average time to resolve complaints across a
number of different dimensions.

## Prerequisites

- Docker desktop installed on your system
- Docker images for PostgreSQL install on Docker Desktop
- CSV file CRM events and call center logs data
- SQL Client (DBeaver, etc)

## Setup Instructions

### 1. PostgreSQL Database Setup

Start a PostgreSQL Docker container with the following configuration:

```bash
docker run --name consolidatedash -e POSTGRES_USER=cons_dash -e POSTGRES_PASSWORD=consdash2023 -p 5432:5432 -d postgres
```

### 2. Database Schema Creation

Connect to your PostgreSQL instance and create the required schema and tables:

```sql
-- Create schema
CREATE SCHEMA IF NOT EXISTS crm;

-- Create CRM events table
CREATE TABLE crm.events(
    Date_received DATE,
    Product VARCHAR(200),
    Sub_product VARCHAR(200),
    Issue VARCHAR(200),
    Sub_issue VARCHAR(200),
    Consumer_complaint_narrative VARCHAR(5000),
    Tags VARCHAR(200),
    Consumer_consent_provided VARCHAR(200),
    Submitted_via VARCHAR(200),
    Date_sent_to_company DATE,
    Company_response_to_consumer VARCHAR(200),
    Timely_response VARCHAR(200),
    Consumer_disputed VARCHAR(200),
    Complaint_ID VARCHAR(200),
    Client_ID VARCHAR(200)
);

-- Create call center logs table
CREATE TABLE crm.call_center_logs(
    Date_received DATE,
    Complaint_ID VARCHAR(200),
    rand_client VARCHAR(200),
    phonefinal VARCHAR(200),
    vru_line VARCHAR(200),
    call_id VARCHAR(200),
    priority VARCHAR(200),
    type VARCHAR(200),
    outcome VARCHAR(200),
    server VARCHAR(200),
    ser_start VARCHAR(200),
    ser_exit VARCHAR(200),
    ser_time TIME
);
```

## Data Import Process

1. **Prepare your data files**: Ensure CRM events and call center logs are in CSV format
2. **Import data**: Use DBaver features to import CSV file to table. Ensure that the column names in the file match those in the table.
3. **Validate data**: Run basic counts and checks to ensure data integrity
4. **Execute analysis**: Run the provided queries to generate insights

## Analysis Queries

### 1. Average Resolution Time by Product

Analyzes resolution times for different product categories (Banking, Credit Card):

```sql
SELECT 
    e.product, 
    AVG(ccl.ser_time) 
FROM crm.events e
JOIN crm.call_center_logs ccl ON e.complaint_id = ccl.complaint_id 
WHERE e.complaint_id IS NOT NULL
GROUP BY e.product 
ORDER BY e.product;
```

### 2. Average Resolution Time by Issue Type
Breaks down resolution times by issue categories:

```sql
SELECT 
    e.issue, 
    AVG(ccl.ser_time) 
FROM crm.events e
JOIN crm.call_center_logs ccl ON e.complaint_id = ccl.complaint_id 
WHERE e.complaint_id IS NOT NULL
GROUP BY e.issue 
ORDER BY e.issue;
```

### 3. Average Resolution Time by Month

Shows trends in complaint resolution:

```sql
SELECT 
    TO_CHAR(e.date_received, 'YYYY-MM') as month, 
    AVG(ccl.ser_time) 
FROM crm.events e
JOIN crm.call_center_logs ccl ON e.complaint_id = ccl.complaint_id 
WHERE e.complaint_id IS NOT NULL
GROUP BY TO_CHAR(e.date_received, 'YYYY-MM')
ORDER BY month;
```