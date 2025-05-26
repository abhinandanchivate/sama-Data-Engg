 **GCP Basics + Architecture** with **architectural diagrams, key explanations**, and **industry-relevant examples**:

---

## 📘 **GCP Basics + Architecture – Student Manual**

### 🧠 **Theory Coverage: 100%**

---

## **1. Introduction to Google Cloud Platform (GCP)**

### 📌 What is GCP?

GCP is a suite of cloud services by Google offering computing, storage, networking, machine learning, and Big Data.

---

## **2. Core GCP Concepts**

### 🔸 **2.1 Regions and Zones**

| Term       | Description                                                                         |
| ---------- | ----------------------------------------------------------------------------------- |
| **Region** | A specific geographical location (e.g., `us-central1`) with multiple isolated zones |
| **Zone**   | Deployment area within a region, providing fault isolation (e.g., `us-central1-a`)  |

> **Best Practice:** Distribute workloads across **multiple zones** for **high availability**.

#### 🌍 **Diagram: GCP Global Infrastructure**

```
            GCP Global Network
     ┌─────────────────────────────────┐
     │       Region: us-central1       │
     │ ┌────────┬────────┬───────────┐ │
     │ │ Zone A │ Zone B │ Zone C    │ │
     │ └────────┴────────┴───────────┘ │
     └─────────────────────────────────┘
```

---

### 🔸 **2.2 Projects**

* Logical container for all GCP resources.
* Bound by:

  * Billing Account
  * Identity and Access Management (IAM)
  * APIs and services

> **Tip:** Use different projects for **dev**, **test**, and **prod** environments.

---

## **3. Virtual Private Cloud (VPC)**

### 📌 What is VPC?

GCP’s **global network** for managing your virtual resources, providing **isolation**, **IP allocation**, **routing**, **firewalls**, and **subnets**.

### 🔹 Key VPC Components:

* Subnetworks
* Routes
* Firewall Rules
* Peering and VPN

#### 🌐 **Diagram: VPC + Subnets**

```
           VPC Network (Global)
          ┌───────────────────┐
          │    Project A      │
          │ ┌───────────────┐ │
          │ │ Subnet (us)   │ │
          │ └───────────────┘ │
          │ ┌───────────────┐ │
          │ │ Subnet (asia)│ │
          │ └───────────────┘ │
          └───────────────────┘
```

> **Best Practice:** Use **custom mode VPC** for more control and **least privilege** firewall rules.

---

## **4. GCP Resource Hierarchy**

### 📌 Levels of GCP Resource Management

1. **Organization Node (Optional)**
2. **Folders (Optional)**
3. **Projects**
4. **Resources (Compute, Storage, etc.)**

#### 🏗️ **Diagram: Resource Hierarchy**

```
Organization
   └── Folder: Dev Team
       └── Project: dev-project
           └── Resources: VM, Bucket, DB
```

> This hierarchy enables centralized **IAM** and **policy enforcement**.

---

## **5. Pricing and Billing**

### 💰 Key Concepts:

| Concept              | Description                                |
| -------------------- | ------------------------------------------ |
| **Billing Account**  | Linked to credit card/payment              |
| **Budgets & Alerts** | Set thresholds and get alerts              |
| **Cost Breakdown**   | View cost per service, per project         |
| **Commitments**      | Discounts for sustained or committed usage |

### 📊 Diagram: Billing Model Overview

```
             Billing Account
                   │
       ┌───────────┴───────────┐
       │                       │
  Project A                Project B
       │                       │
  Compute, Storage       BigQuery, AI APIs
       │                       │
   Usage Tracked         Usage Tracked
       └───────> Consolidated Invoice
```

> **Tools:** Use **Cloud Billing Reports**, **BigQuery Export**, **Billing Budgets**, and **Cost Table Reports**.

---

## **6. IAM: Identity & Access Management**

### 🛡️ Roles:

| Role Type  | Scope        | Example                      |
| ---------- | ------------ | ---------------------------- |
| Basic      | Broad        | Owner, Editor, Viewer        |
| Predefined | Specific     | Storage Admin, Compute Admin |
| Custom     | Fine-grained | Created per org’s needs      |

> **Principle of Least Privilege** should always be followed.

---

## ✅ **Quick Recap Table**

| Topic           | Purpose                                    | Key Takeaway                       |
| --------------- | ------------------------------------------ | ---------------------------------- |
| Regions & Zones | Geographical isolation and fault tolerance | Use multi-zonal deployment         |
| Projects        | Logical containers for GCP resources       | Use for separating dev/stage/prod  |
| VPC             | Private network for GCP services           | Use custom mode and firewall rules |
| IAM             | Controls who can do what                   | Assign minimal required roles      |
| Billing         | Manage and monitor usage cost              | Set budgets and alerts per project |

---

## 🧪 Suggested Hands-on Labs:

| Lab # | Topic                   | Task                             |
| ----- | ----------------------- | -------------------------------- |
| Lab 1 | Create GCP Project      | Setup using Cloud Console        |
| Lab 2 | Configure VPC & Subnets | Custom VPC with multiple subnets |
| Lab 3 | IAM Roles               | Assign Compute Viewer role       |
| Lab 4 | Billing Dashboard       | Create budget and setup alert    |
| Lab 5 | Deploy Compute Engine   | Launch VM in a selected zone     |

---


