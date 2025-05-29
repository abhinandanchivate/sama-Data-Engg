Absolutely! Here's your **complete, detailed guide** to connect and work with **Firestore from the terminal (macOS/Linux shell)** using the `gcloud` CLI and SDK tools.

---

## ✅ 0. 🔐 **Authenticate & Setup**

### 🧭 Prerequisites (you’ve already done some of these):

```bash
gcloud init
gcloud auth login
gcloud config set project [YOUR_PROJECT_ID]
```

---

## ✅ 1. 🔗 **Connect to Firestore**

Firestore is a **fully-managed NoSQL document database**. You can interact with it using:

* `gcloud firestore` for some limited admin ops
* Firebase CLI (`firebase`) for real-time document access
* SDKs (`Python`, `Node.js`) for full data access

Let’s start with what you can do directly from the terminal.

---

## ✅ 2. 📋 **List Firestore Databases (All Modes)**

### ▶️ **List in Default Format**

```bash
gcloud firestore databases list
```

### ▶️ **List in Table Format**

```bash
gcloud firestore databases list --format="table(name.basename(), type, locationId, appEngineIntegrationMode)"
```

### ▶️ **List in JSON Format**

```bash
gcloud firestore databases list --format=json
```

### ▶️ **List in YAML Format**

```bash
gcloud firestore databases list --format=yaml
```

### ▶️ **Custom Table Columns**

```bash
gcloud firestore databases list --format="table(name.basename(), locationId, type)"
```

---

## ✅ 3. 🔧 **Create Firestore Database (Native Mode) — Optional**

Firestore databases are usually auto-created, but if needed:

```bash
gcloud firestore databases create --database=projects-db5 --location=asia-south1 --type=firestore-native
```

---

## ✅ 4. 📄 **Perform CRUD Operations on Firestore (Documents)**

Note: `gcloud` CLI has **limited document-level operations**. For full features, use the **Firebase CLI or SDKs (Python/Node.js)**.

### ▶️ Create a Document

```bash
gcloud firestore documents create \
  projects/[PROJECT_ID]/databases/(default)/documents/users/user1 \
  --data='{"name":"Alice", "email":"alice@example.com"}'
```

### ▶️ Read a Document

```bash
gcloud firestore documents get \
  projects/[PROJECT_ID]/databases/(default)/documents/users/user1
```

### ▶️ Update a Document

```bash
gcloud firestore documents update \
  projects/[PROJECT_ID]/databases/(default)/documents/users/user1 \
  --update='email=newalice@example.com'
```

### ▶️ Delete a Document

```bash
gcloud firestore documents delete \
  projects/[PROJECT_ID]/databases/(default)/documents/users/user1
```

---

## ✅ 5. 🔁 **Firebase CLI for Real-Time Data Access (Optional)**

### ▶️ Install Firebase CLI:

```bash
npm install -g firebase-tools
```

### ▶️ Login:

```bash
firebase login
```

### ▶️ Access Firestore Data:

```bash
firebase firestore:get /users --project=[PROJECT_ID]
```

> You can also `set`, `delete`, and `update` documents using this CLI.

---

## ✅ 6. 🐍 Use Python SDK (Optional, Powerful)

### ▶️ Install SDK:

```bash
pip install google-cloud-firestore
```

### ▶️ Sample Python Code:

```python
from google.cloud import firestore

db = firestore.Client()
doc_ref = db.collection("users").document("user1")
doc_ref.set({"name": "Alice", "email": "alice@example.com"})

doc = doc_ref.get()
print(doc.to_dict())
```

---

## ✅ 7. 📤 Export / 📥 Import Data (Firestore Admin)

### ▶️ Export (to GCS bucket):

```bash
gcloud firestore export gs://[BUCKET_NAME]/exports/
```

### ▶️ Import:

```bash
gcloud firestore import gs://[BUCKET_NAME]/exports/[EXPORT_FOLDER]/
```

Make sure the bucket exists and is in the **same region** as the Firestore DB.

---

## ✅ 8. ⚙️ Emulator for Local Testing (Optional)

```bash
gcloud components install cloud-firestore-emulator
gcloud beta emulators firestore start
```

---

## 🧾 Summary Cheatsheet

| Task             | Command                                                 |
| ---------------- | ------------------------------------------------------- |
| Login            | `gcloud auth login`                                     |
| Init SDK         | `gcloud init`                                           |
| List DBs (table) | `gcloud firestore databases list --format="table(...)"` |
| Create Doc       | `gcloud firestore documents create`                     |
| Read Doc         | `gcloud firestore documents get`                        |
| Update Doc       | `gcloud firestore documents update`                     |
| Delete Doc       | `gcloud firestore documents delete`                     |
| Export           | `gcloud firestore export gs://...`                      |
| Firebase Access  | `firebase firestore:get`                                |
| Emulator Start   | `gcloud beta emulators firestore start`                 |

---


