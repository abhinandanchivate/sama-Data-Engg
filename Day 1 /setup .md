**Student Manual for Lab 1: GCP Setup**, broken into **micro-steps**, with real-world context, checkpoints, UI navigation help, icons, and annotations you can easily convert into a printable workbook, slide deck, or LMS module.

---

# 🛠️ **Lab 1: Google Cloud Platform (GCP) Setup – Full Student Manual**

---

## 🎯 **Objective:**

To successfully create and configure a free-tier GCP account, set up billing with budget alerts, create a project, and enable the required APIs. This lab is foundational for all subsequent labs.

---

## 📦 **Lab Duration:** 1.5 to 2 hours

## 🧪 Hands-on Intensity: 70%

## 📚 Pre-Reads: GCP Overview, Regions, Projects, VPC, IAM

---

## 🛒 **Step-by-Step Walkthrough**

---

### ✅ **Step 1: Account Setup – Sign Up for GCP Free Tier**

#### 📍Navigation:

Go to → [https://cloud.google.com](https://cloud.google.com)

#### 📝 Actions:

1. Click on **“Get started for free”**
2. Login with a **Google Account (Gmail)**. Preferably use a new one for training.
3. Fill in:

   * **Country** (e.g., India)
   * Accept **Terms and Conditions**
4. Proceed to phone number verification:

   * Receive OTP on mobile and verify identity
5. Enter **Credit/Debit card details** (for identity only)

   * GCP will make a small test charge (₹1 or \$1) and immediately refund it.
6. Complete sign-up and click **“Start My Free Trial”**

#### 🎁 Benefits:

* You get **\$300 in credits**
* Valid for **90 days**
* Many GCP services are in the **Always Free Tier**

#### 📷 Screenshot/Diagram to Include:

* Google Cloud Free Tier Signup Page
* Highlight: Phone verification, billing card input, and free trial confirmation

---

### ✅ **Step 2: Billing Configuration**

#### 📍Navigation:

`Menu → Billing → Manage Billing Accounts`

#### 📝 Actions:

1. Confirm your **Billing Account** is created.
2. Click on the **Billing Account Name** to open the dashboard.
3. Under **“Budgets & Alerts”**:

   * Click **“CREATE BUDGET”**
   * Name: `TrialBudget_YourName`
   * Scope: Apply to entire project
   * Set budget amount: `$300`
   * Add thresholds:

     * 50% – send email
     * 80% – send email + Slack (optional)
4. Save

#### 🔒 Access:

You must be a **Billing Admin** or Project Owner to configure budgets.

#### 📷 Screenshot/Diagram to Include:

* Billing Account view
* Budget Threshold Setting Wizard

---

### ✅ **Step 3: Create a New GCP Project**

#### 📍Navigation:

Top menu bar → Click on project dropdown → “NEW PROJECT”

#### 📝 Actions:

1. Click **New Project**
2. Fill in:

   * **Project Name**: `data-engineering-lab`
   * **Billing Account**: Link to the trial account
   * **Location**: No org/folder (keep as-is for personal setup)
3. Click **Create**
4. Wait for project creation (\~10 sec)

#### 🔁 Best Practices:

* Use a **naming convention** for projects:
  Format → `team-name-usecase-env`
  e.g., `ml-pipeline-dev`, `iot-data-prod`

#### 📷 Screenshot/Diagram to Include:

* New project creation dialog
* Project dashboard after creation

---

### ✅ **Step 4: Set Default Region & Zone**

#### 📍Navigation:

`Navigation Menu → Compute Engine → VM Instances → Set Default Region`

#### 📝 Actions:

1. GCP will prompt you to set **Default Region and Zone**
2. Choose based on your location (for latency):

   * **Mumbai** → `asia-south1-a`
   * **Iowa** → `us-central1-a`
3. Save preferences

---

### ✅ **Step 5: Enable Required APIs**

#### 📍Navigation:

`Navigation Menu → APIs & Services → Library`

#### 📦 Recommended APIs to Enable:

| API Name                     | Service Area       | Purpose                          |
| ---------------------------- | ------------------ | -------------------------------- |
| Compute Engine API           | Compute            | VM instance management           |
| Cloud Storage API            | Storage            | Object storage                   |
| BigQuery API                 | Analytics          | Data warehouse                   |
| Cloud Resource Manager API   | Project & IAM Mgmt | Enables IAM, billing, quota      |
| Identity and Access Mgmt API | IAM & Security     | Service account, roles, policies |

#### 📝 Actions:

1. Search each API by name
2. Click **Enable**
3. Confirm status = “Enabled” under `Enabled APIs & Services`

---

### ✅ **Step 6: Create a Service Account (Optional, for advanced labs)**

#### 📍Navigation:

`IAM & Admin → Service Accounts → Create`

#### 📝 Actions:

1. Name: `sa-data-engineer`
2. Grant Roles:

   * Storage Admin
   * BigQuery Admin
3. Download JSON key (if needed for CLI)

---

## 📋 **Verification Checklist**

| Task Description                   | Status |
| ---------------------------------- | ------ |
| GCP Free Trial activated           | ✅      |
| Billing account verified           | ✅      |
| Budget with alerts configured      | ✅      |
| New project created and selected   | ✅      |
| Default region/zone set            | ✅      |
| Required APIs enabled              | ✅      |
| Service account created (optional) | ✅      |

---

## 📦 **Real-World Use Case**

> A company onboarding 20 data engineers would use this setup process to isolate environments, assign permissions, enable tracking, and control spend — with service accounts and budgets per project.

---

## 🧪 **Hands-On Lab Extensions**

| Activity                        | Purpose                                      |
| ------------------------------- | -------------------------------------------- |
| Use gcloud CLI to list projects | Practice terminal interaction                |
| Export billing to BigQuery      | Set up billing dataset for cost analysis     |
| Enable Logging API              | Prepares you for logging and monitoring labs |

---

## 🎓 **Wrap-Up & Evaluation**

### ✅ Submit:

* Screenshots of:

  * GCP Dashboard with project
  * Billing Budgets
  * Enabled APIs page
  * Service Account (optional)

### 📬 Instructor Note:

Cross-verify budget alerts to ensure billing is safely configured.

---

Would you like me to:

1. 🧾 Format this into a **PDF Manual**
2. 📊 Build a **slide deck** for in-class training
3. 📘 Generate a **Lab Sheet (Excel checklist)**

Let me know what format you need this in.
