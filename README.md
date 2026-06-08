# Retail Sales Analysis & Exploration (PostgreSQL)

##  Project Overview
This project presents an end-to-end data engineering and exploratory data analysis (EDA) solution using **PostgreSQL**. The workflow encompasses initializing a data schema, performing strategic data cleaning to isolate structural anomalies, exploring foundational transactional metrics, and solving advanced analytical business problems. 
---
##  Project Objectives
1. **Database Setup**: Set up and configure a structured database environment utilizing robust typing and transactional boundaries.
2. **Data Cleaning & Quality Assurance**: Isolate and eliminate records containing critical missing values (`NULL`) to establish an audited data baseline.
3. **Exploratory Data Analysis (EDA)**: Map the dataset's scale, count customer unique variants, and categorize product spreads.
4. **Business Analytics Solutions**: Craft specific queries to solve explicit real-world business tracking questions
---
##  Database Schema Architecture

```sql
-- Create Database
CREATE DATABASE sql_project_sq1;

-- Create Table
CREATE TABLE retail_sales (
    transactions_id INT PRIMARY KEY,
    sale_date DATE,	
    sale_time TIME,
    customer_id INT,	
    gender VARCHAR(10),
    age INT,
    category VARCHAR(35),
    quantity INT,
    price_per_unit FLOAT,	
    cogs FLOAT,
    total_sale FLOAT
);
