# 🧊 Snowflake SQL CheatSheet — Time Travel, Cloning & Retention

> SQL walkthrough for table retention, cloning, query tracking, time travel recovery, transient/temporary table behavior, and metadata inspection using the Tasty Bytes dataset.

---

## 📋 Table Retention & Metadata

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `SHOW TABLES`       | List tables in current schema            | `SHOW TABLES;`                                                              | Table audit |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS` | Set retention period | `ALTER TABLE TASTY_BYTES.RAW_POS.TEST_MENU SET DATA_RETENTION_TIME_IN_DAYS = 90;` | Retain for 90 days |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS` | Reduce retention period | `ALTER TABLE TASTY_BYTES.RAW_POS.TEST_MENU SET DATA_RETENTION_TIME_IN_DAYS = 1;` | Retain for 1 day |

---

## 🧪 Table Cloning & Age Calculation

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `DROP TABLE`        | Remove dev table if exists               | `DROP TABLE TASTY_BYTES.RAW_POS.TRUCK_DEV;`                                 | Cleanup |
| `CREATE OR REPLACE TABLE ... CLONE ...` | Clone a table                        | `CREATE OR REPLACE TABLE tasty_bytes.raw_pos.truck_dev CLONE tasty_bytes.raw_pos.truck;` | Clone truck table |
| `SELECT ...`        | Preview cloned table                     | `SELECT t.truck_id, t.year, t.make, t.model FROM tasty_bytes.raw_pos.truck_dev t;` | View truck_dev |
| `SELECT ...`        | Correct age calculation                  | `SELECT t.truck_id, t.year, t.make, t.model, (YEAR(CURRENT_DATE()) - t.year) AS truck_age FROM tasty_bytes.raw_pos.truck_dev t;` | Calculate truck age |

---

## 🧠 Query & Timestamp Tracking

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `SET`               | Store last query ID                      | `SET good_data_query_id = LAST_QUERY_ID();`                                 | Save query ID |
| `SELECT $...`       | View stored query ID                     | `SELECT $good_data_query_id;`                                               | Confirm value |
| `SET`               | Store current timestamp                  | `SET good_data_timestamp = CURRENT_TIMESTAMP;`                              | Save timestamp |
| `SELECT $...`       | View stored timestamp                    | `SELECT $good_data_timestamp;`                                              | Confirm value |
| `SHOW VARIABLES`    | Confirm stored variables                 | `SHOW VARIABLES;`                                                           | Audit session variables |

---

## ❌ Mistakes & Overwrites

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `SELECT ...`        | Incorrect age calculation                | `SELECT t.truck_id, t.year, t.make, t.model, (YEAR(CURRENT_DATE()) / t.year) AS truck_age FROM tasty_bytes.raw_pos.truck_dev t;` | Mistake 1 |
| `UPDATE ... SET ...`| Overwrite year with incorrect logic      | `UPDATE tasty_bytes.raw_pos.truck_dev t SET t.year = (YEAR(CURRENT_DATE()) / t.year);` | Mistake 2 |
| `SELECT ...`        | View overwritten data                    | `SELECT t.truck_id, t.year, t.make, t.model FROM tasty_bytes.raw_pos.truck_dev t;` | Confirm overwrite |

---

## ⏳ Time Travel Recovery

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `SELECT ... AT(TIMESTAMP => $...)` | Recover data by timestamp         | `SELECT * FROM tasty_bytes.raw_pos.truck_dev AT(TIMESTAMP => $good_data_timestamp);` | Restore by timestamp |
| `SELECT ... AT(TIMESTAMP => '...')` | Manual timestamp recovery         | `SELECT * FROM tasty_bytes.raw_pos.truck_dev AT(TIMESTAMP => '2024-04-04 21:34:31.833 -0700'::TIMESTAMP_LTZ);` | Manual restore |
| `SELECT TIMESTAMPDIFF(...)` | Calculate offset in seconds         | `SELECT TIMESTAMPDIFF(second, CURRENT_TIMESTAMP, $good_data_timestamp);` | Offset calculation |
| `SELECT ... AT(OFFSET => -...)` | Recover data by offset             | `SELECT * FROM tasty_bytes.raw_pos.truck_dev AT(OFFSET => -45);`           | Restore by offset |
| `SELECT ... BEFORE(STATEMENT => $...)` | Recover data before query        | `SELECT * FROM tasty_bytes.raw_pos.truck_dev BEFORE(STATEMENT => $good_data_query_id);` | Restore before query |

---

## 🧱 Transient & Temporary Tables

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `CREATE TRANSIENT TABLE ... CLONE ...` | Create transient clone         | `CREATE TRANSIENT TABLE TASTY_BYTES.RAW_POS.TRUCK_TRANSIENT CLONE TASTY_BYTES.RAW_POS.TRUCK;` | Transient clone |
| `CREATE TEMPORARY TABLE ... CLONE ...` | Create temporary clone         | `CREATE TEMPORARY TABLE TASTY_BYTES.RAW_POS.TRUCK_TEMPORARY CLONE TASTY_BYTES.RAW_POS.TRUCK;` | Temporary clone |
| `SHOW TABLES LIKE 'TRUCK%'` | List truck-related tables         | `SHOW TABLES LIKE 'TRUCK%';`                                               | Filtered audit |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS = 90` | Set retention for standard table | `ALTER TABLE TASTY_BYTES.RAW_POS.TRUCK SET DATA_RETENTION_TIME_IN_DAYS = 90;` | Success |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS = 90` | Attempt on transient table     | `ALTER TABLE TASTY_BYTES.RAW_POS.TRUCK_TRANSIENT SET DATA_RETENTION_TIME_IN_DAYS = 90;` | Fails |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS = 90` | Attempt on temporary table     | `ALTER TABLE TASTY_BYTES.RAW_POS.TRUCK_TEMPORARY SET DATA_RETENTION_TIME_IN_DAYS = 90;` | Fails |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS = 0`  | Set retention to 0 (transient) | `ALTER TABLE TASTY_BYTES.RAW_POS.TRUCK_TRANSIENT SET DATA_RETENTION_TIME_IN_DAYS = 0;` | Success |
| `ALTER TABLE ... SET DATA_RETENTION_TIME_IN_DAYS = 0`  | Set retention to 0 (temporary) | `ALTER TABLE TASTY_BYTES.RAW_POS.TRUCK_TEMPORARY SET DATA_RETENTION_TIME_IN_DAYS = 0;` | Success |

---

## 🧬 Advanced Cloning & Metadata Inspection

| **Command**         | **Purpose**                             | **Syntax**                                                                 | **Example** |
|---------------------|------------------------------------------|-----------------------------------------------------------------------------|-------------|
| `CREATE OR REPLACE TABLE ... CLONE ...` | Clone truck table             | `CREATE OR REPLACE TABLE tasty_bytes.raw_pos.truck_clone CLONE tasty_bytes.raw_pos.truck;` | Clone truck |
| `SELECT * FROM INFORMATION_SCHEMA.TABLE_STORAGE_METRICS` | View storage metadata        | `SELECT * FROM TASTY_BYTES.INFORMATION_SCHEMA.TABLE_STORAGE_METRICS WHERE TABLE_NAME = 'TRUCK_CLONE' OR TABLE_NAME = 'TRUCK';` | Compare size |
| `SELECT * FROM INFORMATION_SCHEMA.TABLES` | View table metadata           | `SELECT * FROM TASTY_BYTES.INFORMATION_SCHEMA.TABLES WHERE TABLE_NAME = 'TRUCK_CLONE' OR TABLE_NAME = 'TRUCK';` | Compare metadata |
| `INSERT INTO ... SELECT * FROM ...` | Duplicate data into clone     | `INSERT INTO tasty_bytes.raw_pos.truck_clone SELECT * FROM tasty_bytes.raw_pos.truck;` | Double clone size |
| `CREATE OR REPLACE SCHEMA ... CLONE ...` | Clone schema                  | `CREATE OR REPLACE SCHEMA tasty_bytes.raw_pos_clone CLONE tasty_bytes.raw_pos;` | Schema clone |
| `CREATE OR REPLACE DATABASE ... CLONE ...` | Clone database                | `CREATE OR REPLACE DATABASE tasty_bytes_clone CLONE tasty_bytes;` | DB clone |
| `CREATE OR REPLACE TABLE ... CLONE ... AT(OFFSET => ...)` | Time travel clone            | `CREATE OR REPLACE TABLE tasty_bytes.raw_pos.truck_clone_time_travel CLONE tasty_bytes.raw_pos.truck AT(OFFSET => -60*10);` | Clone past state |

---

## 🧠 UPT/WIT Tags

- **UPT**: Understand cloning types, retention policies, time travel syntax, and metadata inspection  
- **WIT**: Practice schema/database cloning, offset-based recovery, and storage metrics analysis

---

## 🙌 Credits

Crafted by **Ferdinand** — Data Engineer
