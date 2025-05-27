 **PostgreSQL on GCP** with:

* ✅ **Console-based steps**
* ✅ **Cloud Shell commands**
* ✅ **IAM role mapping for each user** ()
* ✅ **Testing expectations**
* ✅ **Access matrix** for real-time verification

---

## 🔬 Lab: GCP Cloud SQL – PostgreSQL for Data Engineering

---

### 🎯 Lab Objective

You will:

* ✅ Create a PostgreSQL instance on GCP
* ✅ Connect via Cloud Shell & Console
* ✅ Create a database, table, and insert sample data
* ✅ Assign IAM roles
* ✅ Validate access for different user roles

---

## 🧩 Step-by-Step Instructions

---

### ✅ STEP 1: Enable Cloud SQL Admin API

#### 📍Via Console:

1. Go to: [https://console.cloud.google.com/apis/library/sqladmin.googleapis.com](https://console.cloud.google.com/apis/library/sqladmin.googleapis.com)
2. Click **"Enable"**

#### 🖥️ Via Cloud Shell:

```bash
gcloud services enable sqladmin.googleapis.com
```

---

### ✅ STEP 2: Create PostgreSQL Instance

#### 📍Via Console:

1. Go to: **SQL > Create Instance**
2. Choose **PostgreSQL**
3. Set:

   * Name: `pg-dataeng-lab`
   * Version: `PostgreSQL 14`
   * Region: `us-central1`
   * Machine Type: `Shared Core (db-f1-micro)`
   * Set root password: `Pg@12345678`
4. Click **Create**

#### 🖥️ Via Cloud Shell:

```bash
gcloud sql instances create pg-dataeng-lab \
  --database-version=POSTGRES_14 \
  --cpu=1 \
  --memory=3840MB \
  --region=us-central1 \
  --tier=db-f1-micro \
  --root-password="Pg@12345678"
```

---

### ✅ STEP 3: Create a Database

#### 📍Via Console:

1. Navigate to your instance
2. Click on **Databases** tab → **Create database**
3. Name: `labdb` → Click **Create**

#### 🖥️ Via Cloud Shell:

```bash
gcloud sql databases create labdb --instance=pg-dataeng-lab
```

---

### ✅ STEP 4: Create DB User for Connection

#### 📍Via Console:

1. Instance → **Users** tab → **Create User**
2. Username: `dataengineer`
   Password: `Engineer@123`
3. Click **Create**

#### 🖥️ Via Cloud Shell:

```bash
gcloud sql users create dataengineer \
  --instance=pg-dataeng-lab \
  --password="Engineer@123"
```

---

### ✅ STEP 5: Connect via Cloud Shell and psql

#### 🖥️ From Cloud Shell:

```bash
gcloud sql connect pg-dataeng-lab --user=postgres
```

> 🔐 Allow Cloud Shell to whitelist IP when prompted.

---

### ✅ STEP 6: Create Table and Insert Data (inside `psql`)

```sql
-- Connect to your database
\c labdb;

-- Create table
CREATE TABLE employees (
  id SERIAL PRIMARY KEY,
  name VARCHAR(50),
  role VARCHAR(50)
);

-- Insert sample records
INSERT INTO employees (name, role) VALUES
('Alice', 'Data Engineer'),
('Bob', 'ETL Developer'),
('Charlie', 'Data Analyst');

-- View inserted data
SELECT * FROM employees;
```

To exit:

```sql
\q
```

---

## 🛡️ STEP 7: Assign IAM Roles to Team Users

| Email                                | IAM Role                | Purpose                        |
| ------------------------------------ | ----------------------- | ------------------------------ |
| `data.engineer.labuser@gmail.com`    | `roles/cloudsql.admin`  | Full Cloud SQL Admin           |
| `data.viewer.labuser@gmail.com`      | `roles/cloudsql.viewer` | Read-only access               |
| `pipeline.manager.labuser@gmail.com` | `roles/cloudsql.client` | Can connect but no admin       |
| `custom.engineer.labuser@gmail.com`  | `roles/cloudsql.editor` | Modify metadata, no full admin |

#### 🖥️ Assign via Cloud Shell:

```bash
# Admin
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:data.engineer.labuser@gmail.com" \
  --role="roles/cloudsql.admin"

# Viewer
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:data.viewer.labuser@gmail.com" \
  --role="roles/cloudsql.viewer"

# Client
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:pipeline.manager.labuser@gmail.com" \
  --role="roles/cloudsql.client"

# Editor
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:custom.engineer.labuser@gmail.com" \
  --role="roles/cloudsql.editor"
```

#### 📍Assign via Console:

1. Go to: **IAM & Admin > IAM**
2. Click **+ Grant Access**
3. Enter email and assign the role
4. Repeat for each user above

---

## 🧪 STEP 8: Access Testing Scenarios

| Email                                | Role   | Should Be Able To...             | Testing Steps                          |
| ------------------------------------ | ------ | -------------------------------- | -------------------------------------- |
| `data.engineer.labuser@gmail.com`    | Admin  | Full create/update/delete access | Connect, insert new data, create table |
| `data.viewer.labuser@gmail.com`      | Viewer | View metadata only               | Cannot connect via SQL                 |
| `pipeline.manager.labuser@gmail.com` | Client | Connect and query                | Try `gcloud sql connect` + `SELECT *`  |
| `custom.engineer.labuser@gmail.com`  | Editor | Modify settings                  | Cannot add users but can change flags  |

---

## 🧹 STEP 9: Cleanup (Optional)

```bash
gcloud sql instances delete pg-dataeng-lab --quiet
```

---

## 🧾 Final Access Matrix

| Email                                | IAM Role | Connect via Shell | Create DB/Table | Insert Data              | View Config |
| ------------------------------------ | -------- | ----------------- | --------------- | ------------------------ | ----------- |
| `data.engineer.labuser@gmail.com`    | Admin    | ✅                 | ✅               | ✅                        | ✅           |
| `data.viewer.labuser@gmail.com`      | Viewer   | ❌ (API only)      | ❌               | ❌                        | ✅           |
| `pipeline.manager.labuser@gmail.com` | Client   | ✅                 | ❌               | ✅ (if DB access granted) | ❌           |
| `custom.engineer.labuser@gmail.com`  | Editor   | ✅                 | ❌               | ❌                        | ✅           |

---

