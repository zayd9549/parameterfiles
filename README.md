## **3. Parameter Files**

Oracle uses **Parameter Files** to store **initialization parameters** required to start and configure a database instance.

These parameters define the **runtime environment** for the Oracle instance, such as memory settings, process limits, file locations, etc.

---

### ✅ **What Is a Parameter File?**

* A **Parameter File** is read by the Oracle Database during **startup**.
* It contains configuration parameters that **control instance behavior**.
* Parameter files can be either **text-based (PFILE)** or **binary (SPFILE)**.

---

### 📁 **Default Location**

```bash
$ORACLE_HOME/dbs
```

This is the default directory where Oracle looks for both PFILEs and SPFILEs during instance startup.

---

### 🔄 **Types of Parameter Files**

| Type                               | Description                                                       |
| ---------------------------------- | ----------------------------------------------------------------- |
| **PFILE** (Parameter File)         | A **client-side**, text-based file manually maintained by DBAs.   |
| **SPFILE** (Server Parameter File) | A **server-side**, binary file that Oracle manages automatically. |

---

### 🔬 **PFILE vs SPFILE – Comparison Table**

| Feature      | **PFILE**                           | **SPFILE**                              |
| ------------ | ----------------------------------- | --------------------------------------- |
| Format       | Text (ASCII)                        | Binary                                  |
| Location     | `$ORACLE_HOME/dbs/init<SID>.ora`    | `$ORACLE_HOME/dbs/spfile<SID>.ora`      |
| Editable     | Yes, using a text editor (e.g., vi) | No, must use `ALTER SYSTEM` command     |
| Change Scope | Static – Restart required           | Static & Dynamic – Depends on parameter |
| RMAN Support | Not supported                       | Fully supported                         |
| Use Case     | For recovery or testing             | Recommended for production use          |
| Modification | Manual editing                      | Dynamically modifiable                  |

---

### 🧠 **How to Check If the Database Is Using PFILE or SPFILE**

From SQL\*Plus, run:

```sql
SQL> show parameter spfile;
```

#### 🔎 Interpretation:

* If the output is **blank**, the database was started using a **PFILE**.
* If a file path is shown (e.g., `/u01/app/oracle/dbs/spfilePROD.ora`), then the instance is using an **SPFILE**.

---

### 🛠️ **Modifying Parameters When Using SPFILE**

To modify a parameter:

```sql
SQL> ALTER SYSTEM SET parameter_name = value SCOPE = MEMORY|SPFILE|BOTH;
```

#### 📌 Example:

```sql
SQL> ALTER SYSTEM SET db_cache_size = 500M SCOPE = BOTH;
```

---

### 🧾 **Understanding the SCOPE Clause**

| **SCOPE** | **Parameter Type** | **Effect**                                                     |
| --------- | ------------------ | -------------------------------------------------------------- |
| `SPFILE`  | Static or Dynamic  | Change is saved in SPFILE; **requires restart** to take effect |
| `MEMORY`  | Dynamic only       | Change is **temporary**; exists until next restart             |
| `BOTH`    | Dynamic only       | Change is **immediate** and **persistent**                     |

> ⚠️ Static parameters **cannot** use `MEMORY` or `BOTH`. Use `SCOPE=SPFILE` and restart the instance.

---

### 🔍 **How to Check if a Parameter is Static or Dynamic**

Use the `V$PARAMETER` view:

```sql
SQL> SELECT name, issys_modifiable FROM v$parameter WHERE name = 'db_cache_size';
```

#### Values of `ISSYS_MODIFIABLE`:

| **Value**   | **Meaning**                                                                       |
| ----------- | --------------------------------------------------------------------------------- |
| `IMMEDIATE` | Parameter is **dynamic** and can be changed **immediately** without restarting    |
| `DEFERRED`  | Parameter change is **postponed** until instance **restart**                      |
| `FALSE`     | Parameter is **static**; **must** restart the instance for changes to take effect |

---

### 📋 **Sample Parameter File Names**

* **PFILE**:

  ```bash
  $ORACLE_HOME/dbs/initPROD.ora
  ```

* **SPFILE**:

  ```bash
  $ORACLE_HOME/dbs/spfilePROD.ora
  ```

> Replace `PROD` with your actual Oracle SID.

---

### 🛡️ **Backup & Recovery**

* **PFILE**: Backup manually (text file copy).
* **SPFILE**: Can be backed up using RMAN.

```sql
RMAN> BACKUP SPFILE;
```

---

### 📌 **Additional Examples**

* Create SPFILE from PFILE:

  ```sql
  SQL> CREATE SPFILE FROM PFILE='/u01/initPROD.ora';
  ```

* Create PFILE from SPFILE:

  ```sql
  SQL> CREATE PFILE='/tmp/initPROD.ora' FROM SPFILE;
  ```

