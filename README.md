# ❄️ Snowflake Foundations – Module 2 of 3  
**Focus:** Table Types, Time Travel, Resource Monitors  
**Source:** Visual Slides & Quiz Content  
**Scope:** Coursera-aligned | Drill-ready | Medicaid pipeline compatible

---

## 📘 What We Learned

### 🧩 Table Types
- Differences between **Permanent**, **Transient**, and **Temporary** tables
- How to **create** transient and temporary tables
- Fail-safe behavior:
  - **Permanent**: 7-day fail-safe
  - **Transient/Temporary**: No fail-safe
- Retention logic:
  - Permanent tables (Enterprise Edition) support **up to 90-day Time Travel**

### ⏳ Time Travel
- Querying a table:
  - **As of a timestamp**
  - **As of seconds ago**
  - **Before a specific query ID**
- How to:
  - Save a **query ID**
  - Save a **current timestamp**
  - Use **Time Travel** to recover historical data

### 🛠️ Resource Monitors
- Difference between **account-level** and **warehouse-level** monitors
- How to:
  - Create a monitor via UI
  - Use `CREATE RESOURCE MONITOR` SQL
  - Adjust parameters
  - Drop a monitor
- Trigger actions:
  - `NOTIFY`, `SUSPEND`, `SUSPEND_IMMEDIATE`

### 🧠 Retention & Monitoring
- How to **check** and **set** retention time for a table
- How to **set and use variables** in SQL

---

## 🙌 Credits

This module summary was compiled by **Ferdinand**, Data Engineer

## 🧪 SQL Anchors

```sql
-- Create a transient table
CREATE TRANSIENT TABLE my_transient_table (id INT);

-- Time Travel query
SELECT * FROM my_table AT (TIMESTAMP => '2025-10-28 12:00:00');

-- Create a resource monitor
CREATE RESOURCE MONITOR my_monitor
WITH CREDIT_QUOTA = 100
TRIGGERS ON 80 PERCENT DO NOTIFY
ON 100 PERCENT DO SUSPEND;

## 🙌 Credits

This module summary was compiled by **Ferdinand**, a data engineer
