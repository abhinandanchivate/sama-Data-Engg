 **GCP IAM Lab for Data Engineering** with **clear steps, workflows, role mappings, validation tasks**, and a **workflow diagram**. 

---

# 🧪 GCP IAM Lab – Data Engineering Stream

> **Title**: Creating IAM Users, Assigning Project-Level Permissions, and Using Predefined Roles in GCP
> **Duration**: 90–120 mins
> **Objective**: Learn how to create IAM users, assign predefined and custom roles at the project level for data engineering activities, and validate permissions.

---

## 🔁 LAB WORKFLOW OVERVIEW

```plaintext
[ Admin User (Owner Role) ]
        |
        v
[1] Create Project ➜ [2] Enable APIs ➜ [3] IAM Console
        |
        v
[4] Add IAM Users ➜ [5] Assign Predefined Roles
        |
        v
[6] Validate User Permissions via Incognito or Role Simulator
        |
        v
[7] (Optional) Create Custom Role ➜ Assign ➜ Test
        |
        v
[8] Clean Up Resources and Access
```

---

## 🎯 MODULE 1: PROJECT SETUP

### ✅ Step 1: Create GCP Project

* Login to: [https://console.cloud.google.com/](https://console.cloud.google.com/)
* Top navigation → Click **Project Selector** > **New Project**

  * Project name: `data-eng-iam-lab`
  * Billing account: select or create free-tier billing
  * Location: Choose organization or "No organization"
* Click **Create**

---

## 📦 MODULE 2: API ENABLING

### ✅ Step 2: Enable APIs for Data Engineering

Go to `APIs & Services > Library`
Enable these:

| API Name          | Description                |
| ----------------- | -------------------------- |
| BigQuery API      | Data warehouse             |
| Cloud Storage API | Object storage             |
| Dataflow API      | Pipeline execution         |
| IAM API           | Role and policy management |

---

## 🔑 MODULE 3: IAM SETUP

### ✅ Step 3: Navigate to IAM & Admin

* Go to `IAM & Admin > IAM`
* View current members
* Ensure your current account has `Owner` privileges

---

## 👤 MODULE 4: IAM USER CREATION & ROLE ASSIGNMENT

### ✅ Step 4: Add IAM Users (One-by-One)

1. Click **+ Grant Access**
2. Under **New Principals**, add these emails (use your own other Gmail accounts):

   * `data.engineer.lab@gmail.com`
   * `bq.viewer.lab@gmail.com`
   * `pipeline.runner.lab@gmail.com`
3. Under **Roles**, assign as below:

| User Email                                                            | Role(s)                                        |
| --------------------------------------------------------------------- | ---------------------------------------------- |
| [data.engineer.lab@gmail.com](mailto:data.engineer.lab@gmail.com)     | `BigQuery Data Editor`, `Storage Object Admin` |
| [bq.viewer.lab@gmail.com](mailto:bq.viewer.lab@gmail.com)             | `BigQuery Data Viewer`                         |
| [pipeline.runner.lab@gmail.com](mailto:pipeline.runner.lab@gmail.com) | `Dataflow Developer`, `Storage Object Viewer`  |

✅ Check “Send email notification” → Click **Save**

---

## 🧠 MODULE 5: UNDERSTANDING PREDEFINED ROLES FOR DATA ENGINEERS

| Role                 | Purpose                                    |
| -------------------- | ------------------------------------------ |
| BigQuery Data Editor | Create, update, delete datasets/tables     |
| BigQuery Data Viewer | Read-only access to datasets               |
| Storage Object Admin | Full access to storage buckets             |
| Dataflow Developer   | Submit & monitor dataflow jobs             |
| Pub/Sub Editor       | Manage messaging pipelines (if applicable) |

> View roles via: `IAM & Admin > Roles > View Role Details`

---

## 🔍 MODULE 6: VALIDATION OF PERMISSIONS

### ✅ Step 6: Access Validation

#### 🔎 OPTION A: Manual Login (Incognito Mode)

1. Open **Incognito Window**
2. Login with: `data.engineer.lab@gmail.com`
3. Try:

   * Go to **BigQuery Console**
   * Create Dataset → Run SQL
   * Upload a file to Cloud Storage

#### 🔎 OPTION B: Policy Troubleshooter (Admin Perspective)

* Go to: `IAM & Admin > Policy Troubleshooter`
* Principal: `data.engineer.lab@gmail.com`
* Resource: Project or Dataset
* Permission: `bigquery.datasets.create`
* Click **Check Access**

✅ Ensure:

* `data.engineer.lab` can run BigQuery queries
* `bq.viewer.lab` cannot create datasets
* `pipeline.runner.lab` can deploy a sample Dataflow job

---

## 🧰 MODULE 7: CUSTOM ROLE CREATION

### ✅ Step 7: Custom Role

1. Go to `IAM & Admin > Roles > Create Role`
2. Fill:

   * Title: `RestrictedBQUser`
   * ID: `restrictedBQUser`
   * Permissions:

     * `bigquery.jobs.create`
     * `bigquery.tables.list`
     * `bigquery.datasets.get`
3. Save
4. Go to IAM > Grant Access to a user with this custom role
5. Validate limited access using Incognito or Policy Troubleshooter

---

## 🧼 MODULE 8: CLEAN-UP STEPS

1. Go to IAM Console
2. Remove temporary users added during lab
3. Optionally delete the project if not required

---

## 🧭 FINAL WORKFLOW DIAGRAM

```plaintext
         [ GCP Admin - Owner ]
                   |
     ┌─────────────┼──────────────────┐
     |             |                  |
[Create IAM]  [Assign Predefined] [Create Custom Role]
     |             |                  |
     └─────────────┼──────────┐       |
                   |          |       |
             [Validate Roles] | [Assign to User]
                   |          |       |
              [BigQuery] [Storage] [Dataflow Jobs]
```

---

## ✅ LAB COMPLETION CHECKLIST

| Task                      | Status       |
| ------------------------- | ------------ |
| Project Created           | ✅            |
| APIs Enabled              | ✅            |
| IAM Users Added           | ✅            |
| Predefined Roles Assigned | ✅            |
| Permissions Validated     | ✅            |
| Custom Role Created       | ✅ (Optional) |
| Cleanup Done              | ✅            |

---
