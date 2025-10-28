
# 🧊 Snowflake SQL CheatSheet — Time Travel & Cloning

> SQL walkthrough for table retention, cloning, query tracking, and time travel recovery using the Tasty Bytes dataset.

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

## 🧠 UPT/WIT Tags

- **UPT**: Understand cloning, retention, and time travel recovery using query IDs and timestamps  
- **WIT**: Practice offset recovery, variable tracking, and mistake rollback

---

## 🙌 Credits

Crafted by **Ferdinand** — Data Engineer
