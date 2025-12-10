![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![Built With](https://img.shields.io/badge/Built%20With-Python-blue?logo=python)
![Notebook](https://img.shields.io/badge/Environment-Jupyter-orange?logo=jupyter)
![Dataset](https://img.shields.io/badge/Dataset-CSV%20Files-purple)

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
```DAX
Books-ETL/
│
├── etl_log.txt # Auto-generated log file
├── books_staging.xlsx # Excel staging output
├── data/
│ ├── books.csv
│ ├── ratings.csv
│ └── users.csv
├── code/
│ └── books_etl.ipynb # Jupyter notebook with full ETL logic
└── README.md
```

## 🧠 Key Features

## ✔ Robust error-handled CSV extraction  
### ✔ Column-name normalization  
### ✔ Duplicate & invalid data handling  
### ✔ Location parsing (city, state, country)  
### ✔ Excel staging with multi-sheet splitting  
### ✔ MySQL bulk load using SQLAlchemy  
### ✔ Structured logging with timestamps  

## 📝 Logging Example (etl_log.txt)
```log
[2025-12-10 18:21:01] ========= ETL JOB STARTED =========
[2025-12-10 18:21:01] MySQL connection established successfully
[2025-12-10 18:21:02] Loaded books.csv - 250012 rows
[2025-12-10 18:21:02] Loaded ratings.csv - 1149780 rows
[2025-12-10 18:21:02] Loaded users.csv - 278700 rows
[2025-12-10 18:21:03] Books: removed 1 duplicate rows
[2025-12-10 18:21:05] Staging Excel created
[2025-12-10 18:21:20] Loaded sheet 'books' -> 250011 rows
[2025-12-10 18:21:21] Loaded sheet 'ratings' -> 1149780 rows
[2025-12-10 18:21:22] Loaded sheet 'users' -> 278700 rows
[2025-12-10 18:21:22] ========= ETL JOB COMPLETED =========
```

## 🧮 Core ETL Code Snippet

### **Column Cleaning Function**
```python
def edit_columns(df):
    df = df.copy()
    df.columns = (
        df.columns
        .str.strip()
        .str.lower()
        .str.replace(' ', '_')
        .str.replace('-', '_')
        .str.replace(r'[^0-9a-z_]', '', regex=True)
    )
    return df
```

### **Extract Function**
```python
def read_csv_safe(filepath):
    df = pd.read_csv(
        filepath, 
        sep=';', 
        quoting=csv.QUOTE_NONE, 
        encoding='latin-1', 
        on_bad_lines='skip',
        engine='python'
    )
    df.columns = [c.strip().strip('"') for c in df.columns]
    df = edit_columns(df)
    return df
```

### **Load to MySQL**
```python
def load_to_mysql(df, table):
    df.to_sql(
        name=table,
        con=engine,
        if_exists='replace',
        index=False
    )
    log_message(f"Loaded sheet '{table}' -> {df.shape[0]} rows")
```

## 🛠 Technologies Used

- Python (Pandas, SQLAlchemy, OpenPyXL)
- MySQL Workbench
- Jupyter Notebook
- Structured Logging
- Excel Staging Pipeline


## 📌 Summary

- This project demonstrates a real-world production-style ETL process, including:
- Working with large datasets (1M+ rows)
- Data cleaning & transformation logic
- Retry-safe extraction
- Logging for auditability
- Database loading automation
