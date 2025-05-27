
---

## 🔬 LAB: Firestore in Native Mode for Data Engineering

**Objective**:
You will:

* ✅ Start Firestore in **Native mode**
* ✅ Create **collections and documents**
* ✅ Assign **IAM roles** to user accounts
* ✅ Test access and restrictions

---

### 🧰 Prerequisites

* GCP project: `abhi-461006`
* Billing enabled
* Cloud Shell enabled
* User emails:

  * `data.engineer.labuser@gmail.com`
  * `data.viewer.labuser@gmail.com`
  * `pipeline.manager.labuser@gmail.com`
  * `custom.engineer.labuser@gmail.com`

---

## ✅ STEP 1: Enable Firestore API

### 📍Via Console:

1. Go to: [https://console.cloud.google.com/apis/library](https://console.cloud.google.com/apis/library)
2. Search for **"Cloud Firestore API"**
3. Click **Enable**

### 🖥️ Via Cloud Shell:

```bash
gcloud services enable firestore.googleapis.com
```

---

## ✅ STEP 2: Create Firestore in Native Mode

### 📍Via Console:

1. Go to: [https://console.cloud.google.com/firestore](https://console.cloud.google.com/firestore)
2. Choose:

   * **Native mode**
   * Location: `nam5` (multi-region) or `us-central`
3. Click **Create Database**

> ⚠️ This is a one-time setup per project. It cannot be reversed.

---

## ✅ STEP 3: Add Collections and Documents

### 📍Via Console:

1. Go to: **Firestore > Start Collection**
2. Collection ID: `projects`
3. Click **Next**, then:

   * Document ID: Auto-ID or `p1`
   * Add fields:

     * `name`: `ETL Pipeline`
     * `owner`: `data.engineer.labuser@gmail.com`
     * `status`: `active`
4. Click **Save**

### 🖥️ Via Cloud Shell (gcloud CLI):

```bash
gcloud firestore databases describe

# Set up Firestore using REST API or SDKs (recommended), or via Firebase CLI

# Example using Firestore REST API via curl (advanced)
# Note: `gcloud firestore` CLI does not support native document insert
```

> 🧠 Firestore operations are **UI, SDK or API-driven**, not CLI like SQL or Storage.

---

## ✅ STEP 4: Assign IAM Roles

| Email                                | Role                                | Permissions             |
| ------------------------------------ | ----------------------------------- | ----------------------- |
| `data.engineer.labuser@gmail.com`    | `roles/datastore.user`              | Read/Write access       |
| `data.viewer.labuser@gmail.com`      | `roles/datastore.viewer`            | Read-only access        |
| `pipeline.manager.labuser@gmail.com` | `roles/datastore.importExportAdmin` | Backup/export access    |
| `custom.engineer.labuser@gmail.com`  | `roles/datastore.editor`            | Update/delete docs only |

### 🖥️ Assign via Cloud Shell:

```bash
# Data Engineer (RW)
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:data.engineer.labuser@gmail.com" \
  --role="roles/datastore.user"

# Viewer (Read-only)
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:data.viewer.labuser@gmail.com" \
  --role="roles/datastore.viewer"

# Export Admin (no access to DB itself)
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:pipeline.manager.labuser@gmail.com" \
  --role="roles/datastore.importExportAdmin"

# Editor (update but not create roles/policies)
gcloud projects add-iam-policy-binding abhi-461006 \
  --member="user:custom.engineer.labuser@gmail.com" \
  --role="roles/datastore.editor"
```

---

## 🧪 STEP 5: Testing Role-Based Access

### ✅ Using Firebase Emulator (optional)

You can simulate access using:

```bash
npm install -g firebase-tools
firebase login
firebase init firestore
firebase emulators:start
```

### ✅ Console-Based Tests

| Email                                | Role          | Should Be Able To              | Cannot                                  |
| ------------------------------------ | ------------- | ------------------------------ | --------------------------------------- |
| `data.engineer.labuser@gmail.com`    | User          | Add/edit/view/delete documents | Change rules                            |
| `data.viewer.labuser@gmail.com`      | Viewer        | View documents only            | Edit or add                             |
| `pipeline.manager.labuser@gmail.com` | Import/Export | Use `gcloud firestore export`  | View documents                          |
| `custom.engineer.labuser@gmail.com`  | Editor        | Update/delete                  | Create or read data outside permissions |

---

## 📥 Optional: Export or Import Data

### Export (by pipeline.manager):

```bash
gcloud firestore export gs://your-export-bucket/firestore-backup
```

> Create a bucket beforehand and assign `roles/storage.admin` to `pipeline.manager.labuser@gmail.com`.

---

## 🧹 Cleanup (Optional)

Firestore cannot be "deleted" — but you can delete the GCP project or clear data:

### Delete documents manually:

1. Navigate to Firestore Console
2. Select each document and click **Delete**

### Delete project (if lab-only):

```bash
gcloud projects delete abhi-461006
```

---

## ✅ Final Access Matrix

| Email                                                                           | Role                | Create | Read | Update | Delete | Export |
| ------------------------------------------------------------------------------- | ------------------- | ------ | ---- | ------ | ------ | ------ |
| [data.engineer.labuser@gmail.com](mailto:data.engineer.labuser@gmail.com)       | User                | ✅      | ✅    | ✅      | ✅      | ❌      |
| [data.viewer.labuser@gmail.com](mailto:data.viewer.labuser@gmail.com)           | Viewer              | ❌      | ✅    | ❌      | ❌      | ❌      |
| [pipeline.manager.labuser@gmail.com](mailto:pipeline.manager.labuser@gmail.com) | Import/Export Admin | ❌      | ❌    | ❌      | ❌      | ✅      |
| [custom.engineer.labuser@gmail.com](mailto:custom.engineer.labuser@gmail.com)   | Editor              | ❌      | ✅\*  | ✅      | ✅      | ❌      |

> `*` read may depend on custom access rules; editor role does not guarantee view-only access unless explicitly granted.

---

