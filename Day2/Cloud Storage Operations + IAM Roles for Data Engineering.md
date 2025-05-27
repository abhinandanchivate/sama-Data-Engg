**GCP IAM + Cloud Storage lab** .

---

## 🔬 LAB: Cloud Storage Operations + IAM Roles for Data Engineering

---

### 🎯 OBJECTIVE:

You will:

* ✅ Create a Cloud Storage bucket
* ✅ Upload, download, delete objects
* ✅ Enable versioning and lifecycle policies
* ✅ Assign **bucket-level IAM roles** to Data Engineering team members
* ✅ Generate signed URLs for secure access

---

### 👥 EMAIL ACCOUNTS USED:

| Email                                | Purpose                    |
| ------------------------------------ | -------------------------- |
| `data.engineer.labuser@gmail.com`    | Object Admin (full access) |
| `data.viewer.labuser@gmail.com`      | Object Viewer              |
| `pipeline.manager.labuser@gmail.com` | Object Creator             |
| `custom.engineer.labuser@gmail.com`  | Read-only legacy           |

---

## ✅ STEP 1: Set Project Context

```bash
gcloud config set project abhi-461006
```

---

## ✅ STEP 2: Enable Required APIs

```bash
gcloud services enable storage.googleapis.com iam.googleapis.com
```

---

## ✅ STEP 3: Create the Storage Bucket

```bash
# Ensure a globally unique bucket name
BUCKET_NAME=abhi-dataeng-bucket-$(date +%s)
REGION=us-central1

# Create bucket
gcloud storage buckets create gs://$BUCKET_NAME --location=$REGION

# Confirm creation
gcloud storage buckets list | grep $BUCKET_NAME
```

---

## ✅ STEP 4: Upload, Download, and Delete Objects

### 📁 Create a test file

```bash
echo "Data Engineering GCP lab content" > lab-object.txt
```

### 📤 Upload to Bucket

```bash
gcloud storage cp lab-object.txt gs://$BUCKET_NAME/
```

### 📥 Download it back

```bash
gcloud storage cp gs://$BUCKET_NAME/lab-object.txt downloaded-object.txt
```

### ❌ Delete the object

```bash
gcloud storage rm gs://$BUCKET_NAME/lab-object.txt
```

---

## ✅ STEP 5: Enable Object Versioning

```bash
gcloud storage buckets update gs://$BUCKET_NAME --versioning
```

✅ Verify:

```bash
gcloud storage buckets describe gs://$BUCKET_NAME | grep versioning
```

---

## ✅ STEP 6: Set Lifecycle Rule (Auto-delete after 30 Days)

Create rule file:

```bash
cat > lifecycle.json <<EOF
{
  "rule":
  [
    {
      "action": {"type": "Delete"},
      "condition": {"age": 30}
    }
  ]
}
EOF

# Apply rule
gcloud storage buckets update gs://$BUCKET_NAME --lifecycle-file=lifecycle.json
```

✅ Verify:

```bash
gcloud storage buckets describe gs://$BUCKET_NAME | grep lifecycle
```

---

## ✅ STEP 7: Assign Bucket-Level IAM Roles to Users

```bash
# 1. Full Access – Admin
gcloud storage buckets add-iam-policy-binding gs://$BUCKET_NAME \
  --member="user:data.engineer.labuser@gmail.com" \
  --role="roles/storage.objectAdmin"

# 2. Read-Only Access – Viewer
gcloud storage buckets add-iam-policy-binding gs://$BUCKET_NAME \
  --member="user:data.viewer.labuser@gmail.com" \
  --role="roles/storage.objectViewer"

# 3. Limited Upload Access – Creator
gcloud storage buckets add-iam-policy-binding gs://$BUCKET_NAME \
  --member="user:pipeline.manager.labuser@gmail.com" \
  --role="roles/storage.objectCreator"

# 4. Legacy Read Access – Custom user
gcloud storage buckets add-iam-policy-binding gs://$BUCKET_NAME \
  --member="user:custom.engineer.labuser@gmail.com" \
  --role="roles/storage.legacyBucketReader"
```

✅ Confirm IAM:

```bash
gcloud storage buckets get-iam-policy gs://$BUCKET_NAME
```

---

## ✅ STEP 8: Generate Signed URL (For secure temporary access)

```bash
# Re-upload a file to sign
echo "This file will be accessed via signed URL." > signed.txt
gcloud storage cp signed.txt gs://$BUCKET_NAME/

# Generate a signed URL valid for 10 minutes
gcloud storage sign-url gs://$BUCKET_NAME/signed.txt --duration=10m
```

> ⚠️ You must have **HMAC credentials or service account key** to use `sign-url` via `gcloud`.
> If not, create service account key and use:

```bash
gcloud iam service-accounts keys create key.json \
  --iam-account=<your-service-account>@abhi-461006.iam.gserviceaccount.com
```

Then re-authenticate using:

```bash
gcloud auth activate-service-account --key-file=key.json
```

---

## ✅ STEP 9: Test Access

> Ask your team members to login with respective Gmail accounts to test access:

| Email                                | Expected Behavior                 |
| ------------------------------------ | --------------------------------- |
| `data.engineer.labuser@gmail.com`    | Full access to upload/delete/list |
| `data.viewer.labuser@gmail.com`      | Can only view/download            |
| `pipeline.manager.labuser@gmail.com` | Can upload new objects, not read  |
| `custom.engineer.labuser@gmail.com`  | Legacy read-only access           |

---

## ✅ STEP 10: Cleanup

```bash
# Delete bucket and all objects
gcloud storage rm --recursive gs://$BUCKET_NAME

# Optional: Remove lifecycle config and IAM bindings
rm lab-object.txt downloaded-object.txt signed.txt lifecycle.json
```

---

## 📌 FINAL LAB STRUCTURE

| Task                               | Status |
| ---------------------------------- | ------ |
| Create Bucket                      | ✅      |
| Upload / Download / Delete Objects | ✅      |
| Enable Versioning                  | ✅      |
| Lifecycle Rule Setup               | ✅      |
| IAM Role Assignment                | ✅      |
| Signed URL Generation              | ✅      |
| User Access Testing                | ✅      |
| Cleanup                            | ✅      |

---
Absolutely! Here's the **extended version of the lab** with **access testing instructions for each user**.

This includes:

* ✅ Simulated login/role testing
* ✅ Expected output/actions
* ✅ Troubleshooting tips
* ✅ Manual and CLI test cases

---

## 🧪 STEP 11: Role-Based Testing for Each User

Ask each team member to **log in with their assigned email** at [https://console.cloud.google.com/](https://console.cloud.google.com/)
Or use **Incognito Window** to simulate login.

---

### 👤 User: `data.engineer.labuser@gmail.com`

**Role**: `roles/storage.objectAdmin`

#### ✅ Should be able to:

* Upload a file from UI or Cloud Shell
* Download any file from the bucket
* Delete objects
* List contents of the bucket

#### 🧪 CLI Test Commands:

```bash
# Upload
gcloud storage cp test-admin.txt gs://$BUCKET_NAME/

# List
gcloud storage ls gs://$BUCKET_NAME/

# Delete
gcloud storage rm gs://$BUCKET_NAME/test-admin.txt
```

---

### 👤 User: `data.viewer.labuser@gmail.com`

**Role**: `roles/storage.objectViewer`

#### ✅ Should be able to:

* View files in the bucket
* Download objects
* **Not allowed** to upload, delete, or change files

#### 🧪 CLI Test:

```bash
# View
gcloud storage ls gs://$BUCKET_NAME/

# Download
gcloud storage cp gs://$BUCKET_NAME/signed.txt downloaded-viewer.txt

# Upload (should fail)
gcloud storage cp downloaded-viewer.txt gs://$BUCKET_NAME/
```

#### ❌ Expected Error for Upload:

```
AccessDeniedException: 403 ... does not have storage.objects.create permission
```

---

### 👤 User: `pipeline.manager.labuser@gmail.com`

**Role**: `roles/storage.objectCreator`

#### ✅ Should be able to:

* Upload files (create only)
* **Cannot read or delete files**

#### 🧪 CLI Test:

```bash
# Upload
echo "Pipeline upload test" > uploader.txt
gcloud storage cp uploader.txt gs://$BUCKET_NAME/

# Try download (should fail)
gcloud storage cp gs://$BUCKET_NAME/signed.txt fail.txt
```

#### ❌ Expected Error for Read:

```
AccessDeniedException: 403 ... does not have storage.objects.get access
```

---

### 👤 User: `custom.engineer.labuser@gmail.com`

**Role**: `roles/storage.legacyBucketReader`

#### ✅ Should be able to:

* Read object metadata
* View file listings (sometimes limited)
* May face restrictions on downloading large objects depending on permissions

#### 🧪 CLI Test:

```bash
# Try listing
gcloud storage ls gs://$BUCKET_NAME/

# Try metadata
gcloud storage objects describe gs://$BUCKET_NAME/signed.txt
```

#### ⚠️ Note:

Legacy roles behave inconsistently in some cases and are primarily maintained for backward compatibility.

---

## 🔍 Troubleshooting Tips

| Issue                           | Cause                            | Fix                                                                |
| ------------------------------- | -------------------------------- | ------------------------------------------------------------------ |
| `403 Permission denied`         | User lacks proper IAM role       | Recheck IAM bindings using `gcloud storage buckets get-iam-policy` |
| `Bucket not visible in Console` | Region mismatch or access rights | Ensure correct region and proper read-level roles                  |
| `Signed URL not working`        | URL expired / missing auth       | Regenerate signed URL with valid duration                          |

---

## ✅ Automated Testing via Script (Optional)

Create a test runner file:

```bash
cat > test-access.sh <<EOF
echo "Running tests for bucket: $BUCKET_NAME"

echo "1. Listing objects"
gcloud storage ls gs://$BUCKET_NAME/ || echo "❌ Failed to list"

echo "2. Uploading object"
echo "Sample content" > sample.txt
gcloud storage cp sample.txt gs://$BUCKET_NAME/ || echo "❌ Failed to upload"

echo "3. Downloading object"
gcloud storage cp gs://$BUCKET_NAME/sample.txt sample-downloaded.txt || echo "❌ Failed to download"

echo "4. Deleting object"
gcloud storage rm gs://$BUCKET_NAME/sample.txt || echo "❌ Failed to delete"
EOF

chmod +x test-access.sh
```

> Each user can run the script to verify which steps they’re allowed to perform based on their IAM role.

---

## ✅ Summary of Permissions Matrix

| Email                                | Role                 | Upload | Download | Delete | List |
| ------------------------------------ | -------------------- | ------ | -------- | ------ | ---- |
| `data.engineer.labuser@gmail.com`    | `objectAdmin`        | ✅      | ✅        | ✅      | ✅    |
| `data.viewer.labuser@gmail.com`      | `objectViewer`       | ❌      | ✅        | ❌      | ✅    |
| `pipeline.manager.labuser@gmail.com` | `objectCreator`      | ✅      | ❌        | ❌      | ❌    |
| `custom.engineer.labuser@gmail.com`  | `legacyBucketReader` | ❌      | Maybe ✅  | ❌      | ✅    |

---


