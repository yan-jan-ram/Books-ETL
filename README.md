![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![Tool](https://img.shields.io/badge/Built%20with-Python-blue?logo=python)
![Domain](https://img.shields.io/badge/Domain-BooksRatingsUsers-darkblue)

# 📚 Books ETL Pipeline (Python → Excel → MySQL)

This project is a complete **ETL (Extract, Transform, Load)** pipeline built using **Python, Pandas, SQLAlchemy, and MySQL**.  
It processes a large book-ratings dataset (1.4M+ records), cleans and transforms the data, creates a staging Excel file, and finally loads it into a MySQL database.

A full **logging system** is implemented to track each ETL step with timestamps.

---

## 🚀 Workflow Overview

### **1️⃣ Extract**
- Reads **Books**, **Ratings**, and **Users** CSV files  
- Handles:
  - Incorrect delimiters  
  - Latin-1 encoding  
  - Quoted column names  
  - Missing values  
  - Bad lines in CSV  
- Normalizes column names  
- Reports file shapes

### **2️⃣ Transform**
Cleaning includes:

#### **Books**
- Fix invalid years (<1500 or >current year)  
- Convert types  
- Ensure ISBN is clean  
- Remove duplicates  

#### **Ratings**
- Convert user_id/rating to numeric  
- Remove records missing keys  
- Remove duplicates  

#### **Users**
- Clean age (allowed: 5–100)  
- Split `location` into:
  - city  
  - state  
  - country  
- Remove duplicates  

All cleaned data is written to a **staging Excel file**, automatically splitting sheets if a dataset exceeds Excel row limits.

### **3️⃣ Load**
The final cleaned datasets are loaded into **MySQL tables**:

| Sheet | Rows Loaded |
|-------|-------------|
| books | 250,011 |
| ratings | 1,149,780 |
| users | 278,700 |

---

## 🗂 Project Structure

