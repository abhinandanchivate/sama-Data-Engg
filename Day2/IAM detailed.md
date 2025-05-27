**complete step-by-step guide IAM**, 

---

## Full GCP IAM Lab for Data Engineering – Cloud Shell Script with Steps

---

### 🔧 Step 1: Set the Project

```bash
# Set your GCP project
gcloud config set project abhi-461006
```

---

### 📂 Step 2: Enable Required APIs

```bash
gcloud services enable bigquery.googleapis.com \
                       storage.googleapis.com \
                       dataflow.googleapis.com \
                       iam.googleapis.com
```

---

### 👤 Step 3: Create IAM Users and Assign Predefined Roles

```bash
# Replace with real user emails
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:data.engineer.labuser@gmail.com" \
  --role="roles/bigquery.dataEditor"

gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:data.viewer.labuser@gmail.com" \
  --role="roles/bigquery.dataViewer"

gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:pipeline.manager.labuser@gmail.com" \
  --role="roles/dataflow.developer"

gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:storage.admin.labuser@gmail.com" \
  --role="roles/storage.admin"
```

---

### 🧰 Step 4: Create a Custom Role for Limited Data Engineering Access

```bash
cat > permissions.yaml <<EOF
includedPermissions:
- bigquery.jobs.create
- bigquery.datasets.get
- storage.objects.list
- dataflow.jobs.create
stage: GA
title: "Custom Data Engineer"
description: "Limited role for pipeline execution and read access"
EOF

gcloud iam roles create customDataEngineer \
  --project=abhi-461006 \
  --file=permissions.yaml
```

---

### 🔗 Step 5: Assign the Custom Role to a New User

```bash
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:custom.engineer.labuser@gmail.com" \
  --role="projects/abhi-461006/roles/customDataEngineer"
```

---

### ✅ Step 6: Validate Access (Manual Steps)

Ask each user to log in via [https://console.cloud.google.com/](https://console.cloud.google.com/):

* `data.engineer.labuser` → Should be able to create/query BigQuery datasets
* `pipeline.manager.labuser` → Should be able to submit Dataflow jobs
* `data.viewer.labuser` → Should have read-only BigQuery access
* `custom.engineer.labuser` → Limited functionality as defined

---

### 🧹 Step 7: Cleanup (Optional)

```bash
# Remove users
gcloud projects remove-iam-policy-binding abhi-461006 \
  --member="user:data.engineer.labuser@gmail.com" \
  --role="roles/bigquery.dataEditor"

# Delete custom role
gcloud iam roles delete customDataEngineer --project=abhi-461006
```

---

### 📌 Summary Table

| User Email                                                                      | Role Assigned        | Purpose                 |
| ------------------------------------------------------------------------------- | -------------------- | ----------------------- |
| [data.engineer.labuser@gmail.com](mailto:data.engineer.labuser@gmail.com)       | BigQuery Data Editor | Query + Modify datasets |
| [data.viewer.labuser@gmail.com](mailto:data.viewer.labuser@gmail.com)           | BigQuery Data Viewer | Read-only queries       |
| [pipeline.manager.labuser@gmail.com](mailto:pipeline.manager.labuser@gmail.com) | Dataflow Developer   | Submit jobs             |
| [storage.admin.labuser@gmail.com](mailto:storage.admin.labuser@gmail.com)       | Storage Admin        | Full access to buckets  |
| [custom.engineer.labuser@gmail.com](mailto:custom.engineer.labuser@gmail.com)   | Custom Data Engineer | Limited scoped access   |

---

